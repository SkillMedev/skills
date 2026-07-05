---
name: Escalation Summary
description: Writes a tight escalation summary to engineering or management with a one-line headline, quantified customer impact, repro steps, prior attempts, and a single clear ask. Use when someone says "I need to escalate this ticket", "write this up for engineering", "this bug needs eng eyes", or a support issue must leave the queue for specialist action. Do NOT use for replying to the customer - use support-ticket-reply instead; do NOT use for assigning incident severity and coordinating an active outage - use sev-triage instead; do NOT use for the after-the-fact incident writeup - use postmortem-writer instead.
---

# Escalation Summary

Engineers and managers receive escalations all day; a well-built summary gets a response, a vague one gets queued behind everything else. The costly mistake this skill prevents is the partial escalation - sent early with "details to follow" - which burns the escalation team's first look and resets the clock on the customer. Write as if the reader has zero context and thirty seconds: if the first line does not tell them what is broken, for whom, and since when, the rest never gets read.

## Operating procedure

Order matters: qualify before gathering, gather before writing - an escalation written before the evidence exists gets bounced back for the missing pieces.

1. Qualify the escalation. Confirm the issue is genuinely outside support's scope to resolve. If a documented workaround or known fix exists, apply it instead of escalating.
2. Gather the inputs below completely. Do not start writing until everything is in hand.
3. Assign severity using the definitions below and put the label in the subject line or ticket title.
4. Write the five required sections in order, headline first. The first line is the whole escalation in miniature - spend the most editing time there.
5. Attach the evidence package.
6. Send through the designated escalation channel, never copying the customer.

### Step 1: gather inputs

- The original customer ticket(s) and any screenshots or recordings the customer provided.
- Affected account/order IDs needed to reproduce.
- Count of affected accounts or users, and their revenue or tier. Use hard numbers where available; estimate where not, and label estimates as estimates ("~40 accounts, est. from ticket volume").
- Exact repro steps in a known environment, or explicit confirmation that repro failed and what evidence exists instead.
- Every action support has already tried, with outcomes.
- Any deadline pressure: customer SLA, renewal date, or contractual commitment.

## Required sections (always all five, in this order)

1. **One-line summary**: what is broken, for whom, since when. Example shape: "Checkout fails with a 500 for all EU customers paying by SEPA since 09:00 UTC."
2. **Customer impact**: number of affected accounts or users, revenue or tier, and the business consequence (cannot complete checkout, cannot log in, data loss). Hard numbers where available; labeled estimates where not.
3. **Repro steps**: numbered, precise, environment-specific, with expected vs actual behavior. If repro failed, say so explicitly and list the evidence that exists instead.
4. **What support has already tried**: every action taken, so the escalation team repeats nothing.
5. **The ask**: one sentence stating exactly what is needed - a fix, a workaround, a timeline, or a decision. An escalation with two asks gets neither.

## Severity thresholds

- **P1**: service down or data loss - immediate response required.
- **P2**: major feature broken with significant customer impact - response within 4 hours.
- **P3**: degraded feature with a workaround - response within 1 business day.

A known workaround usually drops severity one level; state it prominently, because it changes the receiver's urgency calculation. Time-sensitivity from an SLA or renewal raises priority - say so plainly with the date.

## Worked artifact: copy-paste template

```
[P_] [FILL: one line - what is broken, for whom, since when]

IMPACT
- Affected: [FILL: N accounts/users - hard number or labeled estimate]
- Tier/revenue: [FILL]
- Business impact: [FILL: what they cannot do]
- Deadline pressure: [FILL: SLA/renewal date, or "none"]

REPRO ([FILL: environment])
1. [FILL]
2. [FILL]
Expected: [FILL]   Actual: [FILL]
(If not reproducible: state so + list evidence: [FILL])

ALREADY TRIED
- [FILL: action → outcome]
- [FILL: action → outcome]

WORKAROUND: [FILL: steps, or "none known"]

ASK: [FILL: one sentence - the single thing needed]

EVIDENCE: [links: original ticket(s), screenshots/recordings,
log excerpts (failure lines only), account/order IDs]
```

Bad ask: "Please look into this when you get a chance - it seems pretty bad and the customer is upset."
Good ask: "Need a fix or supported workaround for the SEPA 500 by Thursday - customer's renewal call is Friday."

## Evidence package

Attach or link: the original customer ticket(s), customer-provided screenshots or recordings, relevant log excerpts (only the lines showing the failure, never full logs), and the account or order IDs needed to reproduce.

## Deliverable

Produce a single escalation message containing the severity-labeled headline, the five sections in order, and the evidence package linked - ready to paste into the escalation channel or ticket with no follow-up required from the receiver before they can act.

## Do NOT

- Do not escalate before confirming the issue is outside support's scope - premature escalations train engineering to deprioritize the queue.
- Do not send a partial escalation with "more details to follow" - the first read is the only read; gather everything first.
- Do not copy the customer on an internal escalation - internal severity language and candid impact estimates are not customer-facing.
- Do not editorialize about how the customer felt or assign blame - emotion and fault-finding both slow the response; facts speed it.
- Do not bury the ask in the impact narrative - the receiver should be able to quote the ask in one sentence.

## Quality bar

Before sending, verify: the first line alone tells a cold reader what is broken, for whom, and since when; the severity label matches the definitions above; every number is either hard or labeled as an estimate; repro steps were re-run as written (or non-repro is stated); the already-tried list would prevent any duplicated work; there is exactly one ask; and the evidence links resolve.
