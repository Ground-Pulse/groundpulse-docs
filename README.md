# GroundPulse — Smart Remote Property Monitoring and Maintenance Platform

> **"See your property's true condition, anywhere in the world, without ever stepping through the door."**

[![CI Pipeline](https://img.shields.io/badge/CI-GitHub_Actions-blue?logo=github-actions)](https://github.com/GroundPulse-App/GroundPulse/actions)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Next.js](https://img.shields.io/badge/Frontend-Next.js_14-black?logo=next.js)](https://nextjs.org/)
[![NestJS](https://img.shields.io/badge/Backend-NestJS_10-E0234E?logo=nestjs)](https://nestjs.com/)
[![PostgreSQL](https://img.shields.io/badge/Database-PostgreSQL_15-336791?logo=postgresql)](https://www.postgresql.org/)
[![Prisma](https://img.shields.io/badge/ORM-Prisma-2D3748?logo=prisma)](https://www.prisma.io/)
[![Redis & BullMQ](https://img.shields.io/badge/Queue-Redis_%2B_BullMQ-DC382D?logo=redis)](https://bullmq.io/)
[![Socket.IO](https://img.shields.io/badge/Real--Time-Socket.IO-010101?logo=socket.io)](https://socket.io/)

**Project Code:** `WEB-01` | **Track:** Full-Stack Web Platform Development | **Organisation:** [github.com/GroundPulse-App](https://github.com/GroundPulse-App)

---

## 📌 The Problem

Property owners who live far from their property (NRIs, rental-portfolio investors, vacation-home owners) face three core issues:
* **No reliable remote visibility:** Owners depend on a relative's occasional visit or a neighbor's phone call with no structure, proof, or accountability.
* **No trust or verification:** Generic property management software is built for rent collection and tenant ticketing, not rigorous physical condition inspection or contractor vetting.
* **No transparent repair workflow:** Maintenance happens (or doesn't) with no timestamped photo evidence of what was flagged, approved, or completed.

Small issues (leaks, electrical faults, security gaps) go undetected until they become expensive, causing owners to take costly, unplanned trips home just to "check."

---

## 💡 The Solution

**GroundPulse** is a multi-role web platform (**Owner**, **Inspector**, **Admin**, **Service Provider**) that provides end-to-end property monitoring and maintenance coordination:
1. **Property Registration:** Owners register properties with full address, type, and cover photos in minutes.
2. **Scheduled Digital Inspections:** Recurring or one-off inspections carried out via a structured room/area checklist.
3. **Media-Rich Reporting:** Automated inspection reports delivered within a strict **48-hour SLA**, backed by timestamped photos and videos.
4. **Owner-Gated Repair Workflow:** No repair work or cost occurs without explicit owner approval.
5. **Verified Marketplace:** Only background-verified inspectors and licensed local service providers can ever be assigned to jobs.
6. **Real-Time Visibility:** Live status updates via Socket.IO push across the entire repair lifecycle (*Requested → Assigned → In Progress → Completed*).

---

## ✨ Features Matrix

Every feature below is **specified and designed**, with its owning service and
priority recorded. None is implemented yet — the service repositories are still
empty. Priorities come from the MoSCoW breakdown in
[`docs/03_PRD_AND_USER_STORIES.md`](docs/03_PRD_AND_USER_STORIES.md).

| Feature | Description | Priority | Owning service |
| :--- | :--- | :---: | :--- |
| **Property Registration & Overview** | Add properties with address, type, and cover photo; multi-property dashboard cards | Must | `property-inspection` |
| **Property Health Score** | Auto-computed 0–100 health score with animated SVG ring and trend history | Should | `property-inspection` |
| **Inspection Scheduling** | Book one-off or recurring inspections with automated inspector matching | Must | `property-inspection` |
| **Digital Inspection Checklist** | Room/area checklist with Pass/Fail/Attention status, draft save/resume, and photo capture | Must | `property-inspection` |
| **Automated 48h SLA Reports** | BullMQ async report compiler generating shareable media-rich PDF/web summaries | Must | `report-worker` |
| **Maintenance Issue Flagging** | Inspector flags issues by category (Leak, Electrical, Security, Cleanliness, Other) with photos | Must | `issue-repair` |
| **Owner Approve / Decline Loop** | Owner reviews flagged issues with photo evidence before approving repairs or declining with reasons | Must | `issue-repair` |
| **Verified Service Provider Matching** | Admin assigns approved repairs exclusively to vetted local providers (`verified: true` gate) | Must | `issue-repair` |
| **End-to-End Repair Tracker** | Real-time stage visibility: `Requested` → `Assigned` → `In Progress` → `Completed` | Should | `issue-repair` |
| **Real-Time Push Gateway** | Socket.IO room-scoped event delivery (`user:{userId}`) for instant notifications | Must | `realtime-gateway` |
| **Role-Based Portals (RBAC)** | Dedicated, server-enforced interfaces for Owner, Inspector, Admin, and Provider | Must | `identity + gateway` |
| **Immutable Audit Trail** | Insert-only audit logging on all approvals, assignments, and status transitions | Must | `all services` |

---

## 🛠️ Complete Tech Stack

| Layer | Technology | Architectural Purpose |
| :--- | :--- | :--- |
| **Frontend Framework** | **Next.js 14 (App Router) + React 18** | Role-scoped route groups `(owner)`, `(inspector)`, `(admin)`, `(provider)` with hybrid SSR/CSR. |
| **Styling & Design System** | **Tailwind CSS v4** | Design system tokens (`--color-bg`, `--color-surface`, `--color-primary`) with dark/light themes. |
| **UI Components** | **shadcn/ui + Radix UI Primitives** | Accessible, WCAG 2.1 AA compliant headless modals, dialogs, sheets, and segmented controls. |
| **State Management** | **TanStack Query + Zustand** | TanStack Query for server-cache & revalidation; Zustand for local checklist drafts & Socket.IO state. |
| **Forms & Validation** | **React Hook Form + Zod** | Re-render-free forms with shared schema validation against backend DTOs. |
| **Data Visualization** | **Recharts** | Responsive SVG charts for property health scores and admin analytics. |
| **Animations** | **Framer Motion** | GPU-composited spring animations for the circular `HealthScoreRing` and status pills. |
| **Backend Framework** | **NestJS 10 (TypeScript)** | Modular architecture with DI, Controllers, Services, and CASL-based RBAC Guards. |
| **Database & ORM** | **PostgreSQL 15+ & Prisma ORM** | 10 relational entities, versioned migrations, and ACID `$transaction()` for multi-step writes. |
| **In-Memory Cache & Queue**| **Redis 7+ & BullMQ** | Decoupled background workers for PDF report generation and email fan-out. |
| **Real-Time Push** | **Socket.IO** | Room-scoped bi-directional WebSocket push with automatic reconnection handling. |
| **Media Storage & CDN** | **AWS S3 / Cloudflare R2 + CloudFront**| Direct pre-signed URL uploads; no binary blobs in PostgreSQL. |
| **Auth & Authorization** | **Passport.js + JWT + CASL** | Short-lived access tokens, refresh rotation, and query-level tenant isolation. |
| **Testing Suite** | **Jest, Supertest, RTL, Playwright** | Full unit, integration, and multi-role end-to-end browser testing. |

---

## 🏗️ System Architecture & Data Flow

```
[ Owner / Inspector / Admin / Provider ]
                     │ (Interacts with)
                     ▼
        [ Next.js 14 UI (App Router) ]
                     │ (Reads / Writes)
                     ▼
        [ TanStack Query + Zustand ]
                     │ (Typed REST API & WebSockets)
                     ▼
         [ NestJS API (Guards + CASL) ]
                     │ (Executes Queries via Services)
                     ▼
             [ Prisma ORM ]
            ┌────────┴────────┐
            ▼                 ▼
   [ PostgreSQL 15 ]    [ Redis 7 (BullMQ) ] ──► [ BullMQ Worker (Report Compiler) ]
            ▲                 │                                 │
            │                 │                                 ▼
   [ S3 Media Storage ]       └───────────────────────► [ Socket.IO Gateway ]
 (Pre-signed direct upload)                                     │ (Push status / notifications)
                                                                ▼
                                                   [ Next.js UI State Updates ]
```

---

## 📦 Where the Code Lives

This repository holds the **documentation and architecture** for GroundPulse.
The running code is split across the service repositories in the
[GroundPulse-App](https://github.com/GroundPulse-App) organisation.

| Repository | Kind | Responsibility |
| :--- | :--- | :--- |
| [`groundpulse-api-gateway`](https://github.com/GroundPulse-App/groundpulse-api-gateway) | Service | Edge routing, JWT verification, dashboard aggregation (BFF) |
| [`groundpulse-identity-service`](https://github.com/GroundPulse-App/groundpulse-identity-service) | Service | Accounts, JWT access/refresh, CASL ability definitions |
| [`groundpulse-property-inspection-service`](https://github.com/GroundPulse-App/groundpulse-property-inspection-service) | Service | Properties, inspections, checklist items, inspection reports |
| [`groundpulse-issue-repair-service`](https://github.com/GroundPulse-App/groundpulse-issue-repair-service) | Service | Issue flagging, owner approval gate, repair lifecycle, provider assignment |
| [`groundpulse-report-worker`](https://github.com/GroundPulse-App/groundpulse-report-worker) | Worker | Async report compilation off the BullMQ `reports` queue |
| [`groundpulse-notification-service`](https://github.com/GroundPulse-App/groundpulse-notification-service) | Service | Notification records, email delivery, fan-out |
| [`groundpulse-realtime-gateway`](https://github.com/GroundPulse-App/groundpulse-realtime-gateway) | Service | Socket.IO gateway, `user:{id}` room scoping, live status push |
| [`groundpulse-media-service`](https://github.com/GroundPulse-App/groundpulse-media-service) | Service | Pre-signed S3/R2 upload URLs, media validation, CDN invalidation |
| [`groundpulse-contracts`](https://github.com/GroundPulse-App/groundpulse-contracts) | Library | Shared DTOs, event payload schemas, error codes — installed by every service |
| [`groundpulse-landing`](https://github.com/GroundPulse-App/groundpulse-landing) | Site | Public landing page — static, no build step |

Why these boundaries, what deliberately stays merged, and what the split costs:
[`docs/07_MICROSERVICE_BOUNDARIES.md`](docs/07_MICROSERVICE_BOUNDARIES.md).

> **Build status.** The service repositories are scaffolded but empty — no
> application code is committed yet. See
> [`docs/06_WEEKLY_OJT_PROGRESS.md`](docs/06_WEEKLY_OJT_PROGRESS.md) for the
> honest state of play.

---

## 🚀 Quick Start

### Prerequisites

* **Node.js** `v20.x LTS`
* **Docker & Docker Compose** `v24+`
* **Git**

### Reading the docs

```bash
git clone git@github.com:GroundPulse-App/GroundPulse.git
cd GroundPulse
```

Everything is Markdown — open [`docs/`](docs/) in any editor. Nothing to install.

### Running the landing page

```bash
git clone git@github.com:GroundPulse-App/groundpulse-landing.git
cd groundpulse-landing
npx serve .
```

Static HTML, CSS and one JS file. No `npm install`, no build step.

### Running a service

Each service repository carries its own `README`, `Dockerfile` and
`.env.example`. The shared shape is:

```bash
git clone git@github.com:GroundPulse-App/<service-name>.git
cd <service-name>
npm install
cp .env.example .env          # then fill in DATABASE_URL, REDIS_URL, JWT_SECRET
docker compose up -d          # local Postgres 15 + Redis 7
npx prisma migrate dev        # services that own tables
npm run start:dev
```

Start order matters, because services depend on each other:

```
1. contracts          (publish first — everything installs it)
2. identity-service   (issues the tokens the others verify)
3. media-service, notification-service, realtime-gateway
4. property-inspection-service, issue-repair-service
5. report-worker      (needs Redis and the inspection service)
6. api-gateway        (routes to all of the above)
```

---

## 🧪 Running Tests

Tests live with the code they cover, so run them inside each service repository:

```bash
npm test                  # unit tests, including CASL authorization rules
npm run test:coverage     # coverage report
npm run test:integration  # Supertest against a test database
```

Cross-service end-to-end tests (Playwright, multi-role) run from
`groundpulse-api-gateway`, which is the only entry point a browser talks to:

```bash
npx playwright test
npx playwright test --ui
```

Coverage targets are defined in the testing strategy: services above 90%, CASL
guards at 100% branch coverage, components above 70%.

---

## 📚 Documentation Index

| Document | Contents |
| :--- | :--- |
| [`01_PROBLEM_AND_VISION.md`](docs/01_PROBLEM_AND_VISION.md) | The problem, who faces it, the vision, objectives, scope boundaries and constraints |
| [`02_COMPETITIVE_LANDSCAPE.md`](docs/02_COMPETITIVE_LANDSCAPE.md) | What owners do today, category analysis, honest weaknesses, research still owed |
| [`03_PRD_AND_USER_STORIES.md`](docs/03_PRD_AND_USER_STORIES.md) | MoSCoW feature priority, personas, 11 user stories with acceptance criteria, user flows, NFRs |
| [`04_SYSTEM_ARCHITECTURE.md`](docs/04_SYSTEM_ARCHITECTURE.md) | Layer stack, full tech stack, component responsibilities, data flows, scalability, security, observability |
| [`05_CUSTOMER_DISCOVERY_LOGS.md`](docs/05_CUSTOMER_DISCOVERY_LOGS.md) | Assumptions to validate, interview guide, log template — **no interviews conducted yet** |
| [`06_WEEKLY_OJT_PROGRESS.md`](docs/06_WEEKLY_OJT_PROGRESS.md) | 12-week plan, milestones, and a weekly log of what actually shipped |
| [`07_MICROSERVICE_BOUNDARIES.md`](docs/07_MICROSERVICE_BOUNDARIES.md) | The nine repositories, why each is separate, what stays merged, and what the split costs |

---

## 🔒 Security & Data Privacy

* **Tenant Isolation:** Every database query is scoped strictly by `ownerId` or `inspectorId` via CASL ability guards; owners can never access another owner's properties or media.
* **Pre-Signed Media URLs:** Media files are stored privately in AWS S3 and served only through short-lived, time-limited signed URLs (15-minute expiry).
* **Immutable Audit Trail:** All state transitions (issue approvals, repair assignments, status changes) are committed to an insert-only `audit_logs` table.
* **Sanitized Inputs:** Strict parameterized Prisma queries prevent SQL injection, and Zod DTO filters strip unauthorized payload fields.

---

## 👥 Contributors

* **Student 1 — Backend & Data Engineer:** Prisma schema, NestJS modules, CASL RBAC, BullMQ job processors, Socket.IO gateway, Supertest integration suite.
* **Student 2 — Frontend & UI Engineer:** Next.js 14 App Router, Tailwind CSS design system, Zustand client stores, TanStack Query hooks, Playwright E2E testing suite.

---

## 📄 License

This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for details.
