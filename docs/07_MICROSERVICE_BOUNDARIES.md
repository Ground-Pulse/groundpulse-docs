# Service Boundaries

**Document ID:** MS-WEB-01 · **Version:** 2.0 · **Status:** Accepted · **Revised:** 2026-09-19

## The rule

A piece of work gets its own deployable service **only when it is queued** —
when the user who triggered it does not wait for it to finish.

Queued work has a different shape from request/response. It arrives in bursts,
it has to retry when a dependency fails, and it scales on the length of the
queue rather than on web traffic. Those are real reasons to run it as a separate
process. Everything a user waits for stays in one API.

> Version 1 of this document split the platform into eight services plus a
> contracts package. Mentor review on 2026-09-19 challenged it: six of those
> services did synchronous work and had no reason to be deployed separately.
> The review was right, and this version replaces that design. The old
> reasoning is kept in git history.

## Where the queues are

GroundPulse uses **BullMQ on Redis**. Across the whole specification there are
exactly **two queues in the MVP**.

| Queue | Producer | Consumer | Why it is queued |
| --- | --- | --- | --- |
| `reports` | `InspectionService.submitChecklist()` | `groundpulse-report-worker` | Compiling a report reads the full checklist and every attached photo, then renders a document. It is slow and bursty — inspections cluster at the end of a working day. The inspector taps *Submit* and must get an instant response. The 48-hour SLA gives minutes of headroom to spare. |
| `notifications` | Every state change: report ready, issue flagged, repair status moved | `groundpulse-notification-worker` | Email goes through a third-party provider with unpredictable latency that can fail outright. It must never run inside `prisma.$transaction()`, or a slow email API would freeze an owner's repair approval. It needs retry with backoff. |

### Background work that is *not* a queue

| Work | Where it runs | Why it does not get a service |
| --- | --- | --- |
| Spawning the next recurring inspection | `groundpulse-api`, via `@nestjs/schedule` | A timer, not a queue. It runs about once a day, creates a handful of rows, and has nothing to scale. If recurring rules ever number in the thousands, move it to a BullMQ repeatable job on a worker. |
| Socket.IO live status push | `groundpulse-api` | Synchronous from the server's side — an event is emitted the moment a status commits. Thousands of long-lived connections *would* justify a split, but that is a different trigger, listed below. |
| Issuing signed media URLs | `groundpulse-api` | Signing a URL takes milliseconds. The heavy part, the upload, already goes directly from the phone to S3 and never touches our servers. |

### Deferred to v2

`ml-detection` — photo damage detection with YOLOv8 (feature C-023). It *will*
meet the rule: GPU inference takes seconds to minutes and runs in Python, a
different runtime entirely. It gets a repository when it is built, not before.
Empty repositories for future work are exactly what this revision removes.

## The repositories

| Repository | Kind | Responsibility |
| --- | --- | --- |
| [`groundpulse-api`](https://github.com/Ground-Pulse/groundpulse-api) | Service | Everything synchronous: auth and RBAC, properties, inspections and checklists, issues and repairs, provider verification and assignment, admin dashboard, signed media URLs, Socket.IO. **Produces** jobs for both queues. The only service with database credentials. |
| [`groundpulse-report-worker`](https://github.com/Ground-Pulse/groundpulse-report-worker) | Worker | **Consumes** `reports`. Renders the report, uploads it to S3, reports the result back to the API. |
| [`groundpulse-notification-worker`](https://github.com/Ground-Pulse/groundpulse-notification-worker) | Worker | **Consumes** `notifications`. Delivers email, retrying with backoff. |
| [`groundpulse-contracts`](https://github.com/Ground-Pulse/groundpulse-contracts) | Library | The job payload schemas the producer and both consumers agree on. Runs nothing, deploys nothing. |
| [`groundpulse-landing`](https://github.com/Ground-Pulse/groundpulse-landing) | Site | Public landing page. Static, no build step. |
| [`groundpulse-docs`](https://github.com/Ground-Pulse/groundpulse-docs) | Docs | This repository. |

**Not yet created:** `groundpulse-web`, the Next.js application the four roles
actually use (owner dashboard, inspector checklist, admin console, provider
jobs). It is a frontend, not a service, and gets a repository when frontend work
begins.

## How the workers get their data

The obvious way to build a worker is to hand it the database. That would put
`schema.prisma` in three repositories and give three processes write access to
the same tables. Instead, **the workers hold no database credentials at all.**

**The notification worker needs no database.** The API writes the in-app
`Notification` row inside the same transaction as the state change that caused
it, then enqueues an email job carrying everything required: recipient, template
and payload. The worker's only external dependency is the email provider. If
that provider is down, the job retries — and the approval it describes has
already committed.

**The report worker reads a snapshot.** `submitChecklist()` locks the checklist;
after submission it can never change. That lock is what makes it safe to put the
full checklist — items, statuses and media keys — into the job payload. The
worker renders the report, uploads it to S3, and calls one internal endpoint on
the API with the result. The API writes the `InspectionReport` row, and that
write enqueues the "report ready" notification.

```
Inspector taps Submit
  → groundpulse-api     locks checklist, enqueues ReportJob(snapshot)  → responds instantly
  → report-worker       renders, uploads to S3
                        POST /internal/inspections/:id/report
  → groundpulse-api     writes InspectionReport, enqueues EmailJob
  → notification-worker emails the owner, retrying on failure
```

This is also what keeps `groundpulse-contracts` small. It holds three things:
the payload schema for each queue (`ReportJob`, `EmailJob`), the internal
callback body, and shared error codes. Producer and consumers both validate
against the same Zod schemas, so renaming a field breaks the build — never
production.

## Retired repositories

Archived on 2026-09-19. Each repository is read-only, links back here, and can
be unarchived if a trigger below is ever met.

| Repository | Responsibility now lives in | Why it was not a service |
| --- | --- | --- |
| `groundpulse-api-gateway` | `groundpulse-api` | With a single API there is nothing to route between. |
| `groundpulse-identity-service` | `groundpulse-api` | Login and token checks are synchronous — the user waits for them. |
| `groundpulse-property-inspection-service` | `groundpulse-api` | Synchronous CRUD. The slow part, rendering reports, moved to the report worker. |
| `groundpulse-issue-repair-service` | `groundpulse-api` | The approval gate is synchronous and must be transactional with repair creation. |
| `groundpulse-media-service` | `groundpulse-api` | Signing a URL takes milliseconds, and uploads bypass the server entirely. |
| `groundpulse-realtime-gateway` | `groundpulse-api` | Split only if connection count demands it — see the triggers below. |

`groundpulse-notification-service` was not retired. It was renamed to
`groundpulse-notification-worker` to say what it does: consume a queue.

## When to split further

These are the conditions under which a new service would be justified. Each one
is measured, not guessed.

| Trigger | Split out |
| --- | --- |
| Concurrent WebSocket connections exceed what one API instance holds comfortably | `realtime-gateway` — long-lived connections scale differently from stateless requests |
| Feature C-023 is built | `ml-detection` — a queued, GPU-bound workload in a different runtime |
| Recurring inspection rules number in the thousands | Move spawning to a BullMQ repeatable job on a worker |
| A second team owns part of the API and needs its own release cadence | Split along the NestJS module boundary that team owns |

## What this costs, and what it saves

At MVP scale this is **three deployable processes**: one API and two workers. Add
the frontend and landing page when they exist. Version 1 needed eight backend
deployments, eight log streams, distributed tracing, and sagas to replace
transactions that Postgres handles for free.

The approval flow — issue approved, repair created, provider notified — stays
inside one `prisma.$transaction()`. Only the email leaves the transaction, and it
leaves through a queue, which is exactly what queues are for.
