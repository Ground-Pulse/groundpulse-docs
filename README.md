# GroundPulse — Smart Remote Property Monitoring and Maintenance Platform

> **"See your property's true condition, anywhere in the world, without ever stepping through the door."**

[![CI Pipeline](https://img.shields.io/badge/CI-GitHub_Actions-blue?logo=github-actions)](https://github.com/Raj-vardhan01/GroundPulse/actions)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Next.js](https://img.shields.io/badge/Frontend-Next.js_14-black?logo=next.js)](https://nextjs.org/)
[![NestJS](https://img.shields.io/badge/Backend-NestJS_10-E0234E?logo=nestjs)](https://nestjs.com/)
[![PostgreSQL](https://img.shields.io/badge/Database-PostgreSQL_15-336791?logo=postgresql)](https://www.postgresql.org/)
[![Prisma](https://img.shields.io/badge/ORM-Prisma-2D3748?logo=prisma)](https://www.prisma.io/)
[![Redis & BullMQ](https://img.shields.io/badge/Queue-Redis_%2B_BullMQ-DC382D?logo=redis)](https://bullmq.io/)
[![Socket.IO](https://img.shields.io/badge/Real--Time-Socket.IO-010101?logo=socket.io)](https://socket.io/)

**Project Code:** `WEB-01` | **Track:** Full-Stack Web Platform Development | **Repository:** [github.com/Raj-vardhan01/GroundPulse](https://github.com/Raj-vardhan01/GroundPulse)

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

| Feature | Description | Status |
| :--- | :--- | :---: |
| **Property Registration & Overview** | Add properties with address, type, and cover photo; multi-property dashboard cards | ✅ |
| **Property Health Score** | Auto-computed 0–100 health score with animated SVG ring and trend history | ✅ |
| **Inspection Scheduling** | Book one-off or recurring inspections with automated inspector matching | ✅ |
| **Digital Inspection Checklist** | Room/area checklist with Pass/Fail/Attention status, draft save/resume, and photo capture | ✅ |
| **Automated 48h SLA Reports** | BullMQ async report compiler generating shareable media-rich PDF/web summaries | ✅ |
| **Maintenance Issue Flagging** | Inspector flags issues by category (Leak, Electrical, Security, Cleanliness, Other) with photos | ✅ |
| **Owner Approve / Decline Loop** | Owner reviews flagged issues with photo evidence before approving repairs or declining with reasons | ✅ |
| **Verified Service Provider Matching** | Admin assigns approved repairs exclusively to vetted local providers (`verified: true` gate) | ✅ |
| **End-to-End Repair Tracker** | Real-time stage visibility: `Requested` → `Assigned` → `In Progress` → `Completed` | ✅ |
| **Real-Time Push Gateway** | Socket.IO room-scoped event delivery (`user:{userId}`) for instant notifications | ✅ |
| **Role-Based Portals (RBAC)** | Dedicated, server-enforced interfaces for Owner, Inspector, Admin, and Provider | ✅ |
| **Immutable Audit Trail** | Insert-only audit logging on all approvals, assignments, and status transitions | ✅ |

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

## 🚀 Quick Start (Local Development)

### Prerequisites
* **Node.js:** `v20.x LTS`
* **Docker & Docker Compose:** `v24+`
* **Git**

### Step-by-Step Installation

1. **Clone the repository:**
   ```bash
   git clone https://github.com/Raj-vardhan01/GroundPulse.git
   cd GroundPulse
   ```

2. **Install all dependencies across the monorepo:**
   ```bash
   npm install
   ```

3. **Start local PostgreSQL and Redis services:**
   ```bash
   docker compose up -d
   ```

4. **Run database migrations and seed demo data:**
   ```bash
   cd apps/api
   npx prisma migrate dev
   npx prisma db seed
   cd ../..
   ```

5. **Start the backend API (NestJS):**
   ```bash
   cd apps/api
   npm run start:dev
   ```
   *API will run at `http://localhost:3001` (Swagger docs at `/api/docs`).*

6. **Start the frontend application (Next.js):** *(in a new terminal)*
   ```bash
   cd apps/web
   npm run dev
   ```
   *Frontend will run at `http://localhost:3000`.*

---

## 🧪 Running Tests

```bash
# Run backend unit and CASL authorization tests
cd apps/api && npm test

# Run backend test coverage suite
cd apps/api && npm run test:coverage

# Run API integration tests (Supertest against test DB)
cd apps/api && npm run test:integration

# Run frontend unit and React Testing Library component tests
cd apps/web && npm test

# Run full multi-role End-to-End tests (Playwright)
npx playwright test

# Run Playwright E2E tests in interactive UI mode
npx playwright test --ui
```

---

## 📁 Repository Structure

```
GroundPulse/
├── apps/
│   ├── web/                             # Next.js 14 App Router Frontend
│   │   ├── app/
│   │   │   ├── (owner)/                 # Owner portal (/dashboard, /property/[id], /issue/[id])
│   │   │   ├── (inspector)/             # Inspector portal (/inspections, /inspection/[id]/checklist)
│   │   │   ├── (admin)/                 # Admin oversight portal (/admin/dashboard, /reassign)
│   │   │   ├── (provider)/              # Service provider portal (/provider/jobs)
│   │   │   ├── (modals)/                # Intercepted modals (Add Property, Schedule Inspection)
│   │   │   ├── layout.tsx               # Root layout + TanStack Query & Theme Providers
│   │   │   └── middleware.ts            # Server-side role route protection
│   │   ├── components/                  # UI components (PropertyCard, HealthScoreRing, ChecklistItemRow)
│   │   ├── stores/                      # Zustand client stores (activeChecklistStore, realtimeStore)
│   │   ├── hooks/                       # TanStack Query custom hooks
│   │   └── lib/                         # apiClient, socket.io client singleton
│   │
│   └── api/                             # NestJS 10 Backend API
│       ├── src/
│       │   ├── modules/
│       │   │   ├── property/            # Property CRUD & HealthScore calculations
│       │   │   ├── inspection/          # Inspection scheduling & checklist submission
│       │   │   ├── issue-repair/        # Issue flagging, owner approvals & repair lifecycle
│       │   │   ├── provider/            # Service provider vetting & assignment
│       │   │   ├── notification/        # Notification service & Socket.IO gateway
│       │   │   └── admin/               # Platform metrics & reassignment services
│       │   ├── auth/                    # Passport.js JWT strategies & CaslAbilityFactory
│       │   ├── jobs/                    # BullMQ report generation & email workers
│       │   ├── prisma/                  # Prisma service & schema.prisma
│       │   └── main.ts                  # NestJS bootstrap with Helmet & CORS configuration
│       └── Dockerfile                   # Multi-stage production container build
│
├── packages/
│   └── shared-types/                    # Shared TypeScript interfaces and DTOs
├── e2e/                                 # Playwright multi-role E2E tests
├── docs/                                # Complete 21-chapter architectural documentation
├── docker-compose.yml                   # Local Postgres 15 + Redis 7 definition
└── .github/workflows/
    ├── ci.yml                           # PR validation (Lint, Typecheck, Unit, Integration, E2E)
    └── deploy.yml                       # CD pipeline (Docker build, ECS Fargate, Vercel prod)
```

---

## 📚 Documentation Index

For exhaustive architectural, security, database, and implementation details, refer to the individual specifications in [`docs/`](file:///c:/Users/LENOVO/Downloads/asset-project/docs):

| Document | File Link |
| :--- | :--- |
| **01. Project Overview** | [`docs/01-project-overview.md`](file:///c:/Users/LENOVO/Downloads/asset-project/docs/01-project-overview.md) |
| **02. Business Requirements (BRD)** | [`docs/02-brd.md`](file:///c:/Users/LENOVO/Downloads/asset-project/docs/02-brd.md) |
| **03. Product Requirements (PRD)** | [`docs/03-prd.md`](file:///c:/Users/LENOVO/Downloads/asset-project/docs/03-prd.md) |
| **04. UX Requirements** | [`docs/04-ux-requirements.md`](file:///c:/Users/LENOVO/Downloads/asset-project/docs/04-ux-requirements.md) |
| **05. Technical Requirements (TRD)** | [`docs/05-trd.md`](file:///c:/Users/LENOVO/Downloads/asset-project/docs/05-trd.md) |
| **06. High-Level Design (HLD)** | [`docs/06-hld.md`](file:///c:/Users/LENOVO/Downloads/asset-project/docs/06-hld.md) |
| **07. Database Design & ERD** | [`docs/07-database-design.md`](file:///c:/Users/LENOVO/Downloads/asset-project/docs/07-database-design.md) |
| **08. API Specification** | [`docs/08-api-specification.md`](file:///c:/Users/LENOVO/Downloads/asset-project/docs/08-api-specification.md) |
| **09. Low-Level Design (LLD)** | [`docs/09-lld.md`](file:///c:/Users/LENOVO/Downloads/asset-project/docs/09-lld.md) |
| **10. Frontend Architecture** | [`docs/10-frontend-architecture.md`](file:///c:/Users/LENOVO/Downloads/asset-project/docs/10-frontend-architecture.md) |
| **11. Security Design & RBAC** | [`docs/11-security-design.md`](file:///c:/Users/LENOVO/Downloads/asset-project/docs/11-security-design.md) |
| **12. Testing Strategy** | [`docs/12-testing-strategy.md`](file:///c:/Users/LENOVO/Downloads/asset-project/docs/12-testing-strategy.md) |
| **13. CI/CD Pipeline** | [`docs/13-cicd-pipeline.md`](file:///c:/Users/LENOVO/Downloads/asset-project/docs/13-cicd-pipeline.md) |
| **14. Observability & Logging** | [`docs/14-observability.md`](file:///c:/Users/LENOVO/Downloads/asset-project/docs/14-observability.md) |
| **15. Deployment Architecture** | [`docs/15-deployment-architecture.md`](file:///c:/Users/LENOVO/Downloads/asset-project/docs/15-deployment-architecture.md) |
| **16. Cost Analysis** | [`docs/16-cost-analysis.md`](file:///c:/Users/LENOVO/Downloads/asset-project/docs/16-cost-analysis.md) |
| **17. Project Roadmap** | [`docs/17-project-roadmap.md`](file:///c:/Users/LENOVO/Downloads/asset-project/docs/17-project-roadmap.md) |
| **18. Team Responsibilities** | [`docs/18-team-responsibilities.md`](file:///c:/Users/LENOVO/Downloads/asset-project/docs/18-team-responsibilities.md) |
| **19. GitHub Repository Structure** | [`docs/19-github-repository-structure.md`](file:///c:/Users/LENOVO/Downloads/asset-project/docs/19-github-repository-structure.md) |
| **20. Architecture Decision Records (ADRs)** | [`docs/21-adr.md`](file:///c:/Users/LENOVO/Downloads/asset-project/docs/21-adr.md) |

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
