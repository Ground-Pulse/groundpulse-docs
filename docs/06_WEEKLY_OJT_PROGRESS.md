# Weekly Progress Log

**Document ID:** OJT-WEB-01 · **Duration:** 12 weeks · **Team size:** 2

> Status reflects what is actually committed to the repositories, not what is
> planned. Keep it that way — an inflated log is worth less than an honest one.

## Plan at a glance

| Phase | Weeks | Focus |
| --- | --- | --- |
| 1 | 1–2 | Research, setup, architecture, auth/RBAC skeleton |
| 2 | 3–4 | Database schema, property registration, inspection scheduling |
| 3 | 5–6 | Digital checklist, media upload, report generation |
| 4 | 7–8 | Issue flagging, repair approval, verified provider assignment |
| 5 | 9 | Real-time (Socket.IO) and admin dashboard |
| 6 | 10 | Testing: unit, integration, E2E |
| 7 | 11 | Security, observability, CI/CD, deployment |
| 8 | 12 | Documentation, demo prep, portfolio polish |

## Milestones

| Milestone | Due | Description | Status |
| --- | --- | --- | --- |
| M1 — Dev environment + auth | End W2 | Services running, login issuing valid JWTs | Not started |
| M2 — Property + scheduling | End W4 | Property registration and inspection scheduling live | Not started |
| M3 — Checklist + reports | End W6 | Full checklist-to-report flow working end to end | Not started |
| M4 — Issue-to-repair loop | End W8 | Flag → approve → assign → complete fully functional | Not started |
| M5 — Real-time + admin | End W9 | Live status updates and admin oversight dashboard | Not started |
| M6 — Tested | End W10 | Full test suite passing at target coverage | Not started |
| M7 — Production-ready | End W11 | CI/CD live, staging deployed, observability wired | Not started |
| M8 — Portfolio ready | End W12 | Documentation, demo and repositories publishable | In progress |

## Week 1

**Focus:** research, repository structure, documentation.

### Done

- Repository structure decided and created under the
  [Ground-Pulse](https://github.com/Ground-Pulse) organisation. It went through
  three shapes in one week: a single monorepo, then eight services plus a
  contracts package, then — after mentor review — one API, two queue workers,
  a contracts package, the landing page and this docs repo. See *Decisions made*.
- Every queue in the specification identified — there are exactly two,
  `reports` and `notifications` — and service boundaries redrawn around them in
  [07_MICROSERVICE_BOUNDARIES.md](07_MICROSERVICE_BOUNDARIES.md).
- Core documentation written: problem and vision, competitive landscape, PRD and
  user stories, system architecture.
- Public landing page built and pushed to
  [groundpulse-landing](https://github.com/Ground-Pulse/groundpulse-landing) —
  static HTML/CSS/JS, no build step.
- Discovery interview guide prepared in
  [05_CUSTOMER_DISCOVERY_LOGS.md](05_CUSTOMER_DISCOVERY_LOGS.md).

### Not done

- No application code committed yet — the API, both workers and contracts are
  all empty.
- Local development environment (Node 20, Docker, Postgres, Redis) not yet
  stood up.
- Auth module and `CaslAbilityFactory` not started.
- **Zero customer interviews conducted.** All five product assumptions remain
  unvalidated, and A5 (letting a stranger into the property) is the one that
  could invalidate the model.

### Decisions made

| Decision | Reasoning |
| --- | --- |
| ~~Split into eight services plus contracts~~ **Superseded 2026-09-19** | Chosen for the learning value of service boundaries. Six of the eight did synchronous work with no reason to deploy separately. |
| **2026-09-19, after mentor review:** only queued work gets its own service | The mentor's challenge was correct. Searching the spec found exactly two queues, `reports` and `notifications`, so exactly two workers. Everything synchronous consolidated into `groundpulse-api`; the six retired repos are archived with pointers to `07`, not deleted, so the evolution stays visible. |
| Workers get no database credentials | Avoids three copies of `schema.prisma` and three writers to the same tables. The report worker reads a snapshot of the locked checklist from its job payload and reports back through one internal API endpoint. |
| Landing page as a separate repo | It is an independent deployable with no database and no login. |

### Next week

- [ ] Stand up local Docker Postgres + Redis
- [ ] Write the initial Prisma schema for all 10 entities
- [ ] Scaffold `groundpulse-api` with a health endpoint, Dockerfile and the auth module
- [ ] Publish the first version of `groundpulse-contracts` with the `ReportJob` and
      `EmailJob` schemas
- [ ] Wire one end-to-end queue path: API enqueues a `ReportJob`, the report worker
      logs it — proves Redis, BullMQ and the contracts package before any real logic
- [ ] **Book at least 3 owner interviews** — this is behind and blocks nothing
      technically, which is exactly why it will keep slipping

### Blockers and risks

| Item | Risk |
| --- | --- |
| Discovery not started | Building against five unvalidated assumptions. Highest-impact item on this page. |
| Repository sprawl | **Resolved 2026-09-19.** Consolidated from nine code repositories to four after mentor review. |
| No frontend repository | The Next.js app the four roles use has no repository yet. Create `groundpulse-web` when frontend work starts. |
| Vercel deployment pending | Landing page is not yet live at a public URL. |

---

## Week 2

**Focus:** _(to be filled)_

### Done
### Not done
### Decisions made
### Next week
### Blockers and risks

---

## Template for remaining weeks

```markdown
## Week N

**Focus:**

### Done
- What was committed, with links to the repos or PRs

### Not done
- What was planned and did not happen, and honestly why

### Decisions made
| Decision | Reasoning |

### Next week
- [ ] Concrete, checkable items

### Blockers and risks
| Item | Risk |
```
