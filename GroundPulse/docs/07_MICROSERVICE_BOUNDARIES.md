# Microservice Boundaries

**Document ID:** MS-WEB-01 · **Status:** Draft · **Date:** 2026-09-18

## Can GroundPulse be a microservice system?

Yes. The documented design is a **modular monolith** — one NestJS app whose
modules (`property`, `inspection`, `issue-repair`, `provider`, `notification`,
`admin`, `auth`) already talk to each other through typed service interfaces
rather than reaching into each other's tables. That is the precondition for
splitting: the seams exist, they just aren't process boundaries yet.

Three things in the existing architecture are already service-shaped:

| Existing piece | Why it wants its own process |
| --- | --- |
| BullMQ report-generation worker | CPU/IO-heavy, bursty, and already decoupled from the request path. Scales on queue depth, not on HTTP traffic. |
| Socket.IO gateway | Stateful and long-lived. Cannot scale the same way as a stateless API behind a load balancer. |
| Notification fan-out | Talks to third parties (email) with their own latency and failure modes, and must not block a transaction. |

## The eight services

| Service | Owns | Why it is its own service |
| --- | --- | --- |
| `groundpulse-api-gateway` | Edge routing, JWT verification, dashboard aggregation (BFF) | Gives the frontend one origin and keeps cross-service fan-out out of the browser. Absorbs the `admin` module's aggregate reads. |
| `groundpulse-identity-service` | `User`, password hashing, JWT access/refresh, CASL ability definitions | Every other service depends on it and nothing depends on them. The most reused and most security-sensitive boundary. |
| `groundpulse-property-inspection-service` | `Property`, `Inspection`, `ChecklistItem`, `InspectionReport` | Kept together deliberately: `submitChecklist()` locks items and creates the report in one transaction. Splitting this pair would need a saga for no benefit. |
| `groundpulse-issue-repair-service` | `Issue`, `Repair`, the approval and status state machine | The owner-approval gate and the repair lifecycle are one consistency domain. Also the highest-churn business logic. |
| `groundpulse-report-worker` | Report compilation consumed off the `reports` queue | Scales on queue depth independently of the API tier. The natural home for the v2 ML damage-detection call. |
| `groundpulse-notification-service` | `Notification`, email delivery, fan-out | Isolates third-party email latency and failure. A dead provider must degrade notifications, not approvals. |
| `groundpulse-realtime-gateway` | Socket.IO, `user:{id}` rooms, event push | Stateful connections with their own scaling and reconnect behaviour. |
| `groundpulse-media-service` | Pre-signed S3/R2 URL issuance, media validation, CDN invalidation | Keeps large uploads off the API tier via direct-to-S3, and centralises signed-URL scoping. |

Plus `groundpulse-contracts` — shared DTOs, event payload schemas and error
codes, versioned and consumed by every service. This is the successor to
`packages/shared-types`, and without it the split just relocates the coupling.

### Deliberately not separate services

- **`provider`** — verification and assignment only ever run inside repair
  assignment. It lives in `issue-repair-service`; a network hop between
  "approve" and "check the provider is verified" would weaken the guarantee.
- **`audit`** — every service writes its own audit rows through a shared library
  against an insert-only table. A central audit *service* would become a
  synchronous dependency on the write path of every state change.
- **`admin`** — read-only aggregation, so it belongs in the gateway.

## What this costs

The docs' own cost analysis puts the monolith at ~$150–300/month at MVP scale.
Eight services means eight deployments, eight log streams, distributed tracing,
and cross-service transactions that Postgres used to handle for free — the
`issue approved → repair created → provider notified` flow currently runs inside
`prisma.$transaction()` and would become a saga with compensating actions.

**For a 2-person, 12-week MVP the modular monolith is the correct call.** This
decomposition is the migration target once traffic or team size justifies it,
and the module boundaries are already drawn so it stays a refactor rather than
a rewrite.
