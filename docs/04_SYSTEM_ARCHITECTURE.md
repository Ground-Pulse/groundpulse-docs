# System Architecture

**Document ID:** ARCH-WEB-01 · **Version:** 1.1 · **Status:** Draft

GroundPulse is a multi-role web application backed by a central API and
database. Owner, Inspector, Admin and Service Provider all interact through the
same platform, with server-persisted state, media in object storage, and async
background processing driving notifications and reports.

For how this is split across deployable repositories, see
[07_MICROSERVICE_BOUNDARIES.md](07_MICROSERVICE_BOUNDARIES.md).

## Layer stack

```
[ Owner / Inspector / Admin / Provider ]
      ↓ interacts with
[ Next.js UI (App Router + Components) ]
      ↓ reads / writes
[ State Layer (TanStack Query + Zustand) ]
      ↓ calls
[ NestJS API (Controllers + Guards) ]
      ↓ executes queries
[ Service / Repository Layer (Prisma) ]
      ↓ persists to
[ PostgreSQL ] + [ S3 Media Storage ]

[ Background Events ]
      ↓ job completion / status change
[ BullMQ Worker → Socket.IO Gateway → State Layer → UI ]
```

## Technology stack

### Frontend

| Purpose | Technology |
| --- | --- |
| Language | TypeScript (strict mode) |
| Framework | React 18 + Next.js 14 (App Router) |
| Styling | Tailwind CSS |
| UI components | shadcn/ui (Radix UI based) |
| Server state / data fetching | TanStack Query |
| Client state | Zustand |
| Forms and validation | React Hook Form + Zod |
| Charts | Recharts |
| Calendar / scheduling UI | FullCalendar |
| Real-time client | Socket.IO client |

### Backend

| Purpose | Technology |
| --- | --- |
| Language | TypeScript / Node.js 20 LTS |
| Framework | NestJS 10 |
| ORM | Prisma |
| Validation | class-validator / Zod |
| Auth | Passport.js + JWT |
| Authorization | CASL |
| Real-time server | Socket.IO |
| Background jobs | BullMQ (Redis-backed) |
| Email | Nodemailer + Resend/SendGrid |
| Upload handling | Multer |
| Image processing | Sharp |

### Data and storage

| Purpose | Technology |
| --- | --- |
| Primary database | PostgreSQL |
| Cache / queue backend | Redis |
| Media storage | AWS S3 (or Cloudflare R2) |
| CDN | CloudFront / Cloudflare |

### Infrastructure

| Purpose | Technology |
| --- | --- |
| Containerisation | Docker + Docker Compose |
| CI/CD | GitHub Actions |
| Frontend hosting | Vercel |
| Backend hosting | AWS ECS/Fargate (or Railway/Render) |
| Monitoring and errors | Sentry |
| Logging | Pino / Winston |
| Secrets | dotenv locally, cloud secrets manager deployed |
| Testing | Jest, Supertest, React Testing Library, Playwright |

## Component responsibilities

### UI layer

| Component | Purpose |
| --- | --- |
| `OwnerDashboard` | Multi-property overview — cards with last inspection date, open-issue count |
| `PropertyDetail` | Reports, issue history and health score for one property |
| `InspectionScheduler` | Date/recurrence picker for booking an inspection |
| `ChecklistScreen` | Inspector's structured room/area checklist with media capture |
| `IssueRepairView` | Issue detail, approve/decline, repair status tracker |
| `ProviderDashboard` | Assigned jobs list and mark-complete flow |
| `AdminConsole` | Cross-entity dashboard and manual reassignment |
| `NotificationCenter` | In-app notification feed and badge |

### State layer

Three Zustand stores hold client-only state; everything server-owned goes
through TanStack Query.

- **`activeChecklistStore`** — the in-progress checklist: item statuses, attached
  media, draft save/submit/discard. Rehydrates from the last saved draft so an
  inspector never loses field work.
- **`preferencesStore`** — theme, active role, notification channel.
- **`realtimeStore`** — live repair statuses keyed by repair id, unread
  notification count, socket connect/reconnect handling.

### Service layer

NestJS services wrapping Prisma repositories — the **only** place business rules
and queries live. Controllers never touch Prisma directly.

| Service | Entities | Key methods |
| --- | --- | --- |
| `PropertyService` | `Property` | `create`, `listByOwner`, `getById`, `computeHealthScore` |
| `InspectionService` | `Inspection`, `ChecklistItem`, `InspectionReport` | `schedule`, `assignInspector`, `saveChecklistDraft`, `submitChecklist`, `generateReport` |
| `IssueRepairService` | `Issue`, `Repair` | `flagIssue`, `approveRepair`, `declineIssue`, `updateRepairStatus`, `completeRepair` |
| `ProviderService` | `ServiceProvider` | `listVerified`, `assign`, `accept`, `decline` |
| `NotificationService` | `Notification` | `dispatch`, `markRead` |

### Persistence layer

- A single PostgreSQL database is the system of record for all property,
  inspection, issue and repair data
- Media lives in S3/R2; only URLs are persisted in Postgres rows
- Multi-step writes — issue approval → repair creation → provider notification —
  run inside `prisma.$transaction()`
- Schema migrations are managed by Prisma Migrate and versioned in source control

### Real-time and async layer

- **BullMQ** processes report generation and notification fan-out, and in v2 will
  enqueue ML damage-detection jobs
- **Socket.IO gateway** pushes `repair.status.updated`, `issue.flagged`,
  `report.ready` and `notification.new` to connected clients, scoped to
  `user:{userId}` rooms
- Subscriptions are established on login; reconnect logic handles dropped
  connections during long inspector field sessions

## Data flows

### Inspection and report generation

```
Owner schedules inspection (InspectionScheduler)
-> InspectionService.schedule() called (API)
-> Inspector assigned, added to their calendar
-> Inspector opens ChecklistScreen on-site
-> activeChecklistStore updates per item (in-memory, optimistic)
-> Inspector taps "Save Draft" periodically
-> InspectionService.saveChecklistDraft() (Postgres UPSERT)
-> Inspector taps "Submit"
-> InspectionService.submitChecklist() — checklist locked
-> BullMQ report-generation job enqueued
-> Worker compiles InspectionReport from checklist + media
-> NotificationService.dispatch() -> email + in-app + Socket.IO push
-> Owner sees "Report ready" within the 48h SLA
```

### Issue flagging through repair completion

```
Inspector flags issue (category + description + photo) in ChecklistScreen
-> IssueRepairService.flagIssue() -> Postgres INSERT (status=FLAGGED)
-> NotificationService.dispatch() -> Owner notified immediately
-> Owner opens IssueRepairView -> taps "Approve" or "Decline"

If approved:
  -> IssueRepairService.approveRepair() -> Repair created (status=REQUESTED)
  -> Admin notified -> assigns verified ServiceProvider
  -> ProviderService.assign() -> Repair status=ASSIGNED -> Provider notified
  -> Provider accepts -> Repair status=IN_PROGRESS
  -> Provider marks complete (notes + photos) -> status=COMPLETED
  -> Socket.IO pushes repair.status.updated at each transition

If declined:
  -> Issue status=CLOSED (with reason recorded)
```

## Routing architecture

| Role | Routes |
| --- | --- |
| Owner | `/dashboard`, `/property/:id`, `/inspection/schedule`, `/issue/:id` |
| Inspector | `/inspections/assigned`, `/inspection/:id/checklist` |
| Admin | `/admin/dashboard`, `/admin/reassign` |
| Provider | `/provider/jobs`, `/provider/job/:id/complete` |

Route groups are guarded by role in Next.js middleware, and independently
enforced server-side — the middleware is convenience, not the security boundary.

## Scalability

| Concern | Strategy |
| --- | --- |
| Large inspection history (10,000+ reports per property) | Postgres indexes on `propertyId` / `scheduledDate`; paginated list, 20 per page |
| Many concurrent inspectors/providers | Stateless NestJS instances behind a load balancer; horizontal scale |
| Heavy media volume | Direct-to-S3 upload via pre-signed URLs, bypassing the API server; CDN for reads |
| Report generation under load | BullMQ priority queues; worker pool scales independently of the API tier |
| Admin dashboard aggregates | Read-optimised queries now; Postgres read replica as counts grow |

## Reliability

| Mechanism | Implementation |
| --- | --- |
| Transaction safety | Multi-step writes wrapped in `prisma.$transaction()` |
| Draft checklist recovery | `activeChecklistStore` rehydrated from the last saved draft on resume |
| Media upload confirmation | A checklist item is marked complete only after S3 upload is confirmed; client retries on failure |
| Async job durability | BullMQ persists jobs in Redis with retry/backoff; jobs survive worker restarts |
| Socket reconnection | Gateway re-subscribes clients on reconnect so live updates aren't missed |

## Security

| Concern | Implementation |
| --- | --- |
| Authentication | Passport.js issuing short-lived JWT access + refresh tokens |
| Authorization | CASL policies enforced as NestJS guards, scoped per role |
| Data isolation | Owners can only query their own properties; inspectors only assigned inspections — enforced at the query layer, not hidden in the UI |
| Audit trail | Every approval, status transition and assignment writes an immutable `AuditLog` row |
| Media access | S3 objects served via signed, time-limited CDN URLs scoped to the requesting user |
| Input validation | class-validator / Zod DTO validation rejects malformed payloads at every endpoint |
| Secrets | dotenv locally; cloud secrets manager in deployed environments |
| Transport | TLS/HTTPS end to end |

## Observability

| Signal | Tool | Purpose |
| --- | --- | --- |
| Application errors | Sentry | Production error tracking, frontend + backend |
| Structured logs | Pino / Winston | Request tracing, job execution logs |
| API performance | Sentry performance / timing middleware | Detect slow endpoints, especially report generation |
| Queue health | Bull Board | Job backlog, failures, retry counts |
| CI signal | GitHub Actions | Catch regressions before deploy |

## Planned v2 — ML damage detection

Scoped as C-023 and deliberately out of the MVP, documented here so the async
hooks exist now and slotting it in later is not a re-architecture.

| Purpose | Technology |
| --- | --- |
| Language | Python |
| Deep learning | PyTorch |
| Object detection | YOLOv8 (Ultralytics) |
| Image preprocessing | OpenCV + Pillow |
| Annotation | Label Studio / Roboflow |
| Experiment tracking | Weights & Biases / MLflow |
| Serving | FastAPI + TorchServe / ONNX Runtime |
| Async processing | Celery + Redis |

```
React/Next.js UI
  ↓
NestJS API (auth, RBAC, business logic)
  ↓
PostgreSQL (via Prisma) ←→ Redis (cache/queue)
  ↓
S3 (photo/video storage)
  ↓ (async job)
BullMQ → Celery → FastAPI + YOLOv8 (damage detection)
  ↓
Result written back to PostgreSQL → surfaced in the owner's report
```
