# Product Requirements and User Stories

**Document ID:** PRD-WEB-01 · **Version:** 1.0 · **Status:** Draft

## Product vision

Give property owners living away from their properties full visibility and
control over their property's condition — turning an occasional, anxious
physical visit into a reliable, transparent, always-available digital view of
the property's health.

## Product goals

| Goal | Description |
| --- | --- |
| Remote visibility | Owners can assess property condition without ever visiting in person |
| Speed | Reports and maintenance alerts reach the owner within 48 hours of an inspection |
| Trust | Every inspector and service provider is verified before assignment |
| Transparency | Every issue, approval and repair has a visible, timestamped audit trail |
| Simplicity | An owner can register a property and schedule their first inspection in under 5 minutes |

## Feature priority (MoSCoW)

### Must have

| ID | Feature |
| --- | --- |
| F-001 | Property registration — address, type, photos |
| F-002 | Inspection scheduling — recurring or one-off |
| F-003 | Digital inspection checklist — structured per room/area |
| F-004 | Photo and video capture on checklist items and issues |
| F-005 | Inspection report generation from the completed checklist |
| F-006 | Maintenance issue flagging by category |
| F-007 | Owner notification system — email and in-app |
| F-008 | Repair request and owner approve/decline |
| F-009 | Service provider assignment, verified-only |
| F-010 | Admin dashboard for cross-entity oversight |
| F-011 | Role-based access for Owner, Inspector, Admin, Provider |
| F-012 | Central, durable SQL persistence |

### Should have

| ID | Feature |
| --- | --- |
| S-013 | Property health score |
| S-014 | Inspection history timeline |
| S-015 | Repair status tracking |
| S-016 | Service provider ratings |
| S-017 | In-app messaging per issue |
| S-018 | Multi-property consolidated overview |

### Could have

| ID | Feature |
| --- | --- |
| C-019 | Calendar sync (Google/Outlook) |
| C-020 | Custom checklist templates per property type |
| C-021 | Cost estimates on repair requests |
| C-022 | Document storage — deeds, insurance, utilities |
| C-023 | AI-powered image analysis for damage detection |
| C-024 | Non-residential asset types |

### Won't have (MVP)

| ID | Feature |
| --- | --- |
| W-025 | Predictive maintenance forecasting |
| W-026 | Smart-device / IoT integration |
| W-027 | Real-time emergency monitoring |

## Personas

| Persona | Role | Goals | Pain points |
| --- | --- | --- | --- |
| **Priya** | Software consultant in Dubai; owns an ancestral apartment back home | Know the property is safe and maintained; handle repairs without flying back | Relies on a neighbour's occasional update; no visibility into actual condition; unsure who to trust |
| **Arvind** | Real-estate investor, 5 rental units across 2 cities | Consolidated oversight of every property's condition and open issues | Managing multiple local agents inconsistently; no single source of truth |
| **Fatima** | Business owner; visits her holiday home 2–3 times a year | Keep the home secure and well-kept between visits | Long idle periods make undetected damage especially costly |

## User stories

### Epic 1 — Property management

**US-001** — As a property owner, I want to register my property with basic
details and photos, so that inspectors and admins know exactly which property
they are servicing.

- [ ] "Add Property" form captures address, property type and cover photo
- [ ] Property appears in the owner's dashboard immediately after saving
- [ ] Property record persists in the database and survives session restarts
- [ ] Validation: address required, at least one photo required

**US-002** — As a property owner with more than one property, I want to see all
my properties in a single overview, so that I can quickly check the status of
each without switching contexts.

- [ ] Dashboard lists all owned properties as cards
- [ ] Each card shows last inspection date and open-issue count
- [ ] Tapping a card opens that property's detail view

### Epic 2 — Inspection and reporting

**US-003** — As a property owner, I want to schedule a property inspection, so
that I get an up-to-date view of my property's condition without visiting it
myself.

- [ ] "Schedule Inspection" flow lets the owner pick a date and optional recurrence
- [ ] Scheduled inspection appears on the assigned inspector's calendar
- [ ] Owner receives confirmation once an inspector is assigned

**US-004** — As an inspector, I want to complete a structured digital checklist
during a visit, so that my findings are consistent and easy for the owner to
review.

- [ ] Checklist organised by room/area with pass/fail/attention items
- [ ] Each item supports attaching a photo or short video
- [ ] Checklist can be saved as a draft and resumed
- [ ] Submitting the checklist locks it and generates the report

**US-005** — As a property owner, I want to receive a report with photos and
videos after each inspection, so that I can see the actual condition of my
property, not just a summary.

- [ ] Report accessible from the property detail view within 48 hours of inspection
- [ ] Report includes all checklist items, statuses and attached media
- [ ] Owner notified by email/in-app the moment the report is ready

### Epic 3 — Maintenance and repair

**US-006** — As an inspector, I want to flag a maintenance issue with a category
and description, so that the owner understands the problem and its urgency.

- [ ] Issue categories: water leakage, electrical, security, cleanliness, other
- [ ] Each issue requires at least one photo and a short description
- [ ] Flagged issue appears in the owner's notifications immediately

**US-007** — As a property owner, I want to review a flagged issue and approve or
decline a repair, so that no work happens on my property without my explicit
consent.

- [ ] Issue detail view shows photos, description and category
- [ ] "Approve Repair" and "Decline" actions available
- [ ] Approval triggers service-provider assignment; decline closes the issue with a reason

**US-008** — As a property owner, I want to track the status of an approved
repair, so that I know when the work has started and been completed.

- [ ] Repair status states: Requested → Assigned → In Progress → Completed
- [ ] Status changes update in real time on the owner's dashboard
- [ ] Completion requires the provider to submit confirmation and optional after-photos

### Epic 4 — Service provider coordination

**US-009** — As an admin, I want to assign a verified local service provider to
an approved repair, so that qualified professionals handle the work.

- [ ] Admin sees a list of verified providers filtered by service type and locality
- [ ] Assigning a provider notifies them with the issue details and location
- [ ] Provider can accept or decline the assignment

**US-010** — As a service provider, I want to see my assigned jobs and mark them
complete, so that owners and admins know the work has been finished.

- [ ] Provider dashboard lists assigned jobs with property address and issue details
- [ ] "Mark Complete" requires a confirmation note and optional photos
- [ ] Completed job moves out of the provider's active list into history

### Epic 5 — Admin and oversight

**US-011** — As an admin, I want a single dashboard of all properties,
inspections and service requests, so that I can monitor and coordinate the whole
platform.

- [ ] Dashboard shows counts: active properties, pending inspections, open issues, in-progress repairs
- [ ] Admin can drill into any property, inspection or repair from the dashboard
- [ ] Admin can manually reassign inspectors or providers if needed

## User flows

### Flow 1 — Schedule and complete an inspection

```
Owner Dashboard -> Tap "Schedule Inspection" on a property
-> Pick date/recurrence -> Confirm
-> Admin/system assigns an inspector
-> Inspector receives job on their calendar
-> Inspector visits property -> completes digital checklist (photos/videos per item)
-> Inspector submits checklist
-> Report auto-generated -> Owner notified
-> Owner opens report from property detail view
```

### Flow 2 — Report an issue and approve a repair

```
During inspection: Inspector flags an issue (category + description + photo)
-> Issue submitted -> Owner notified immediately
-> Owner opens issue detail -> reviews photos and description
-> Owner taps "Approve Repair" (or "Decline")
-> Admin assigns a verified local service provider
-> Provider accepts job -> status: In Progress
-> Provider completes work -> marks job complete (confirmation notes/photos)
-> Owner sees status: Completed
```

### Flow 3 — Onboard a new property

```
Owner signs up / logs in
-> Tap "Add Property"
-> Enter address, property type, upload cover photo
-> Property saved -> appears on Owner Dashboard
-> Owner immediately prompted to "Schedule First Inspection"
```

## Acceptance criteria summary

All Must Have features must pass before the MVP is considered complete:

- Owner, Inspector and Admin each have a distinct, role-restricted interface
- All property, inspection, issue and repair data persists centrally, with no
  data loss on session end
- Inspection reports with photos/videos are viewable by the owner within 48
  hours of the visit
- Every flagged issue requires explicit owner approval before a repair is
  assigned
- Repair status is visible and accurate at every stage
- Admin dashboard accurately reflects live counts
- No crashes or data-loss incidents in happy-path flows across all roles

## Non-functional requirements

| Category | Requirement | Target |
| --- | --- | --- |
| Performance | Inspection report turnaround | ≤ 48 hours |
| Performance | Dashboard load time | < 2 seconds |
| Reliability | Data persistence failure rate | 0% |
| Security | Role-based data isolation | 100% enforced at the API level |
| Transparency | Audit log coverage | 100% of approvals, assignments, status changes |
| Trust | Inspector/provider verification before assignment | 100% |
| Scalability | Concurrent inspections supported | 500+ without degradation |
| Availability | Platform uptime | ≥ 99.5% monthly |
| Compatibility | Supported browsers | Latest 2 versions of Chrome, Safari, Edge, Firefox |
