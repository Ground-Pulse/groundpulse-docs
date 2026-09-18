# Problem and Vision

**Document ID:** PV-WEB-01 · **Project Code:** WEB-01 · **Status:** Draft

## Vision

> "See your property's true condition, anywhere in the world, without ever
> stepping through the door."

Build the most trusted, transparent platform for remote property oversight —
where every inspection, issue and repair is verified, timestamped, and visible
to the owner who cannot be there in person.

## The problem

Owners who cannot be physically present rely on informal, unverifiable
arrangements — a relative's occasional visit, a phone call with a local
caretaker, or a rushed trip home — to know whether their property is safe,
maintained and secure.

This breaks down in three specific ways:

**No reliable remote visibility.** Owners depend on someone else's occasional
visit, with no structure, no photographs, and no accountability for what was or
wasn't checked.

**No trust or verification.** Generic property tools are built for rent
collection, not condition inspection, and there is no way to confirm that an
inspector or contractor is actually qualified.

**No transparent repair process.** Repairs happen — or don't — with no
timestamped record of what was flagged, what was approved, and what was
completed.

### Who faces it

- NRI property owners with an ancestral home or investment property back home
- Real-estate investors managing a rental portfolio across multiple cities
- Vacation-home owners who visit only a few times a year
- Landlords who want oversight without micromanaging tenants or agents

### What it costs them

- Small issues — leaks, electrical faults, security gaps — go undetected until
  they are expensive
- Owners lose trust in local agents with no visible audit trail of what was
  approved and done
- Owners take unplanned, costly trips home purely to "go and check"

## The solution

A role-based web platform — **Owner**, **Inspector**, **Admin**, **Service
Provider** — that gives owners full visibility and control without a visit:

1. **Property registration** — address, type and photos in minutes
2. **Scheduled digital inspections** — recurring or one-off, via a structured
   room/area checklist
3. **Media-rich reporting** — every report delivered within 48 hours, backed by
   photos and video
4. **Owner-gated repair workflow** — no repair happens without explicit owner
   approval
5. **Verified marketplace** — only verified inspectors and providers can ever be
   assigned

## Objectives

| ID | Objective | Metric | Target |
| --- | --- | --- | --- |
| OBJ-01 | Remote visibility | Registered properties assessable without an owner visit | 100% |
| OBJ-02 | Report turnaround speed | Time from inspection completion to report delivery | ≤ 48 hours |
| OBJ-03 | Trust through verification | Assigned inspectors/providers that are verified | 100% |
| OBJ-04 | Approval-gated repairs | Repairs with explicit owner approval before assignment | 100% |
| OBJ-05 | Fast onboarding | Time to register a property and schedule the first inspection | < 5 minutes |
| OBJ-06 | Data durability | Property/inspection/issue/repair data surviving session restarts | 100% |

## Business value

| Stakeholder | Value |
| --- | --- |
| Remote property owners | Full visibility and control over their property's condition without a physical visit |
| Verified inspectors and providers | Structured job assignment through a trusted, verification-gated marketplace |
| Admins | Centralised, live oversight of every property, inspection, issue and repair |
| The build team | Demonstrates full-stack architecture, server-enforced RBAC, real-time systems, async job processing and multi-role platform design |

## Scope boundaries

**In scope for MVP:** property registration, inspection scheduling, digital
checklist with draft/resume, photo and video capture, automated report
generation within 48 hours, issue flagging by category, owner notifications,
approve/decline repair workflow, verified provider assignment, role-based
access for all four roles, admin oversight dashboard, full audit trail.

**Explicitly out of scope for MVP:** predictive maintenance forecasting,
IoT/smart-device integration, real-time emergency monitoring, AI-powered image
analysis of damage, calendar sync, and non-residential asset types.

## What makes this more than a coursework project

- Real multi-role RBAC enforced server-side (CASL policies as NestJS guards),
  not hidden UI elements
- A normalised relational schema across 10 interrelated entities with versioned
  migrations
- Async job architecture decoupling report generation from the request path
- Real-time status push rather than client-side polling
- Verification-gated marketplace logic — an inspector or provider literally
  cannot be assigned unless verified
- An immutable audit trail on every state-changing action
- An architecture with a clear extension point for a v2 ML damage-detection
  service, without re-architecting

## Constraints

| Constraint | Detail |
| --- | --- |
| Infrastructure | Requires a real backend, database, object storage and background jobs — not a client-only app |
| Provider network | Repair fulfilment is only as good as the verified-provider network in a given locality |
| Duration | 12 weeks, 2-person team |
| Verification | Manual admin verification of inspectors/providers is a launch bottleneck |
| Regulatory | Media storage and repair records must respect local data-privacy and contractor-licensing norms |
