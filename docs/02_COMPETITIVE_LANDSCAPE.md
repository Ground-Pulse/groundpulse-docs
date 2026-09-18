# Competitive Landscape

**Document ID:** CL-WEB-01 · **Status:** Draft

> **Note on sourcing.** The category analysis below is derived from the problem
> statement in [01_PROBLEM_AND_VISION.md](01_PROBLEM_AND_VISION.md). Named
> competitors, pricing and feature comparisons still need first-hand research —
> see [Research still owed](#research-still-owed). Do not present this document
> as completed market research until that section is filled.

## The four things owners do today

Every remote owner already has a solution. Understanding where each one runs out
is the whole competitive argument, because GroundPulse is not displacing a
product — it is displacing a habit.

| What owners do today | Where it runs out |
| --- | --- |
| Informal caretaker or neighbour check-ins | No structure, no photos, no accountability; easy to forget or misreport |
| Generic property management software | Built for rent collection and tenant ticketing, not condition inspection or repair approval |
| Video calls with a local agent | Not timestamped, not comprehensive, and entirely dependent on the agent's honesty and availability |
| Hiring a one-off local inspector | No recurring schedule, no digital report, no built-in repair-approval workflow |

## Category analysis

### Property management software

Optimised for the **landlord–tenant relationship**: rent ledgers, lease
documents, tenant-raised maintenance tickets. The implicit assumption is that a
tenant lives in the property and will report problems.

That assumption fails for GroundPulse's user. A vacant ancestral flat has nobody
to raise a ticket. The whole point is proactive inspection of an *unoccupied or
unmonitored* property, which is a different product shape — scheduled outbound
visits rather than inbound complaints.

### Home inspection apps

Usually built for the **transaction moment** — a pre-purchase or pre-sale survey
producing one report for one buyer. They are checklist-and-report tools, and
often genuinely good at that part.

What they lack is the second half of GroundPulse's loop: no recurring schedule,
no owner-approval gate, and no route from "issue found" to "verified provider
completed the repair." The report is the end of their workflow; for GroundPulse
it is the middle.

### NRI property-management services

Human-operated agencies offering caretaking for overseas owners. These are
GroundPulse's **closest substitute** and the sharpest competitor, because they
solve the trust problem with relationships rather than software.

Their structural weaknesses are the opening: service quality varies by the
individual assigned, there is rarely a durable digital audit trail, coverage is
limited to cities where the agency has staff, and pricing is opaque. GroundPulse
competes by making the evidence and the approval trail a property of the
*system* rather than of whoever happens to be assigned.

### Field-service and work-order platforms

Strong at dispatching and tracking jobs, and often at provider management too.
But they are built for a **business operating its own workforce**, not for an
individual owner who needs to approve each job. There is no concept of a remote
principal whose explicit consent gates the work.

## Where GroundPulse is actually differentiated

Three claims, each enforced in code rather than in marketing copy:

**Verification is a hard gate.** A repair cannot be assigned to a provider whose
`verified` flag is false — the assignment is refused at the service layer, not
greyed out in a dropdown.

**Approval is a hard gate.** A repair record cannot exist without a
corresponding owner approval. Declining writes the owner's reason permanently.

**The audit trail is immutable.** Every approval, assignment and status change
writes one insert-only row. The application database role has no `UPDATE` or
`DELETE` grant on that table.

Two further differentiators are structural rather than enforced: the
**four-role model** in a single system (most substitutes cover two), and the
**48-hour report SLA** measured from checklist submission with both timestamps
printed on the report.

## Honest weaknesses

Worth writing down, because a comparison that only flatters the product is not
useful:

- **Cold-start dependency on supply.** Value is zero in a locality with no
  verified providers. Human agencies already have people on the ground.
- **Manual verification does not scale.** Admin review is a real bottleneck, and
  a competitor with an existing vetted bench starts ahead.
- **No relationship.** Some owners specifically want a named human who answers
  the phone. Software is a worse fit for them, and that is fine.
- **Trust in a new platform is hard.** Asking someone to let a stranger into a
  family home is a higher bar than any feature comparison reflects.

## Research still owed

This document is a category framework, not finished research. To complete it:

- [ ] Identify 3–5 named competitors per category, with current pricing
- [ ] Sign up for the closest 2 substitutes and record the actual onboarding flow
- [ ] Verify whether any competitor enforces an owner-approval gate in-product
- [ ] Collect real review data on the top NRI property-management services
- [ ] Confirm which competitors operate in the chosen launch city
- [ ] Replace every category claim above with a cited source

Findings from owner conversations belong in
[05_CUSTOMER_DISCOVERY_LOGS.md](05_CUSTOMER_DISCOVERY_LOGS.md).
