# Customer Discovery Logs

**Document ID:** CD-WEB-01 · **Status:** Not started

> **This file is a template, not findings.**
>
> No owner interviews have been conducted yet. The personas in
> [03_PRD_AND_USER_STORIES.md](03_PRD_AND_USER_STORIES.md) — Priya, Arvind and
> Fatima — are **design personas invented to guide the build**. They are not
> research participants and must never be cited as evidence.
>
> Anything written below the line in *Interview log* should come from a real
> conversation with a real person. Until then this document proves nothing, and
> saying otherwise in a viva or a README would be a false claim about the
> project.

## What needs validating

The product rests on five assumptions. Each is currently **unvalidated**. The
point of discovery is to try to break them, not to collect agreement.

| # | Assumption | Currently | How it fails |
| --- | --- | --- | --- |
| A1 | Remote owners feel enough anxiety about condition to pay for scheduled inspections | Unvalidated | They are content with a relative checking in occasionally, and the pain is not worth a subscription |
| A2 | Owners want to approve each repair individually | Unvalidated | They find per-repair approval annoying and would rather delegate up to a spend limit |
| A3 | Photo evidence is what creates trust | Unvalidated | Trust comes from a named human they can phone, and photos are secondary |
| A4 | A 48-hour report turnaround matters | Unvalidated | Nobody cares whether it takes 2 days or 7; the schedule matters more than the latency |
| A5 | Owners will let a platform-sourced stranger into a family home | Unvalidated | This is a hard no without a personal referral, which breaks the marketplace model |

A5 is the one most likely to be fatal. Test it early and ask it directly.

## Who to talk to

Target **8–12 conversations** before Week 4, across all three owner types:

- [ ] 4–5 NRI owners with a property back home (Priya-shaped)
- [ ] 2–3 portfolio investors with 3+ units (Arvind-shaped)
- [ ] 2–3 vacation-home owners (Fatima-shaped)
- [ ] 2–3 **supply side** — local inspectors or contractors, since the
      cold-start problem in [02_COMPETITIVE_LANDSCAPE.md](02_COMPETITIVE_LANDSCAPE.md)
      is a supply problem, not a demand one

## Interview guide

Ask about past behaviour, not future intentions. "Would you use this?" produces
polite lies; "what did you do last time?" produces facts.

**Opening — establish the situation**
1. Tell me about the property. Where is it, who is there, how often do you visit?
2. When did you last find out something was wrong with it? How did you find out?
3. Walk me through exactly what happened next.

**The current workaround**
4. Who checks on it now? How did that arrangement start?
5. What does it cost you — money, favours, time, worry?
6. When has that arrangement let you down?

**The repair decision**
7. Tell me about the last repair. Who decided it was needed? Who chose who did it?
8. How did you know the work was actually done?
9. Did you ever pay for something you were not sure happened?

**Probing the assumptions**
10. Would you be comfortable with someone you had never met entering the
    property while you were away? *(A5 — do not soften this question)*
11. Would you want to approve every repair, or set a limit and let it run? *(A2)*
12. What would convince you a stranger's inspection was honest? *(A3)*

**Closing**
13. If this problem vanished tomorrow, what would change for you?
14. Who else do you know with the same situation? *(referral for the next interview)*

### Rules

- Never describe GroundPulse before question 10. Pitching contaminates everything after it.
- Record exact quotes. Paraphrase loses the signal.
- Log disconfirming evidence with equal prominence. An interview that breaks an
  assumption is the most valuable outcome here.
- Note who declined to be interviewed and why — that is data too.

## Interview log

Copy the block below per conversation. Do not fill any of it in from memory or
imagination.

```markdown
### CD-00X — <first name or initials>, <role>, <city>
**Date:** YYYY-MM-DD · **Duration:** __ min · **Format:** call / in person
**Property:** <type, location, how often visited>

**Situation in their words:**
> "<exact quote>"

**Current workaround:**

**Last time something went wrong:**

**Last repair, start to finish:**

**A5 — stranger entering the property:**
> "<exact quote — this one matters most>"

**A2 — per-repair approval or a spend limit:**

**A3 — what would make an inspection credible:**

**Assumptions this supported:**
**Assumptions this contradicted:**
**Surprise — something we had not considered:**
**Would they pay? What did they actually say:**
**Referral given:**
```

---

*(no interviews recorded yet)*

---

## Synthesis

Fill in only after 8+ interviews. Leave blank until then rather than guessing.

- **Assumptions that held:**
- **Assumptions that broke:**
- **Changes to the PRD as a result:**
- **Features to cut:**
- **Features discovered that are not in the PRD:**
- **Decision — build, pivot, or reframe:**
