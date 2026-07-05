---
name: SEV Triage
description: Classifies incident severity (SEV1-4) using impact, scope, and urgency signals and decides who to page. Use when an alert fires or a report comes in and a severity call must be made quickly.
---

# SEV Triage

Severity is a paging decision, not a feelings meter. Assign the highest SEV that any single signal justifies, then downgrade only with evidence.

## Severity Definitions

- SEV1 — Revenue-impacting or total service loss. More than 5% of users cannot complete a critical flow. Data loss or breach possible. Page IC + engineering lead + exec on-call immediately.
- SEV2 — Significant degradation. Core feature broken for a subset, elevated error rate above SLO breach threshold, or a workaround exists but is unacceptable long-term. Page IC + team on-call within 5 minutes.
- SEV3 — Partial or minor degradation. Non-critical feature affected, SLO still within budget, no customer escalations yet. Ticket created, team notified async, fix before next business day.
- SEV4 — Cosmetic or edge case. No user impact, caught proactively. Ticket only.

## Signal Checklist

Answer these to land on a SEV. First 'yes' that matches wins.

1. Is a payment, auth, or data-integrity flow broken for any user? -> SEV1 candidate.
2. Is error rate above your SLO threshold for the last 10 minutes? -> SEV2 at minimum.
3. Are multiple regions or availability zones affected? -> Escalate one level.
4. Is there active data exfiltration or corruption risk? -> SEV1 regardless of user count.
5. Is a single non-critical endpoint slow but the rest healthy? -> SEV3.

## Scope Multipliers

- Blast radius: estimate percentage of users affected. Below 1% rarely justifies SEV1 unless the 1% are paying customers or the data risk is severe.
- Growth rate: is impact spreading? A SEV3 that doubles every 10 minutes is a SEV2.
- Detection lag: if the issue started more than 30 minutes ago undetected, assume blast radius is larger than current signals show.

## Who to Page

- SEV1: IC (incident commander), service owner, on-call SRE, and engineering lead. Open a war room immediately.
- SEV2: IC and service owner on-call. Invite others as needed.
- SEV3: Service team on-call async. No war room.
- SEV4: Ticket, no page.

## Worked example: making the call

Bad: "Checkout seems broken for some users. Feels like a SEV3 — I'll dig into the cause first and page someone if it turns out to be serious."

Good: "Checkout error rate at 12% for the last 15 minutes (SLO threshold: 1%). Payment flow broken -> signal 1 -> SEV1. Paged IC, service owner, and on-call SRE at 14:32; war room open. Blast radius estimate: ~12% of checkout attempts, growth unclear. Cause unknown — irrelevant to the SEV. Re-triage at 15:00."

The bad version inverts the order (cause before severity), hedges on a broken payment flow, and delays paging. The good version cites the signal that set the level, pages immediately, records blast radius, and schedules the re-triage — all before anyone knows why it broke.

## Deliverable

Produce a triage record — posted to the incident channel or ticket — containing: the assigned SEV, a one-line justification naming the checklist signal that set it, the blast radius estimate (percentage of users or requests), who was paged and when, and the scheduled re-triage time. The record is what lets the next responder trust the level instead of re-deriving it.

## Quality bar

- The SEV was assigned from impact signals before any root-cause investigation began.
- The justification names a specific checklist signal, not a gut feel.
- Paging happened on assignment, not after confirmation — a potential SEV1 pages on one data point.
- A re-triage time is set (every 30 minutes) and the level moves with the evidence, both up and down.

## Do / Don't

- Do: assign SEV before investigating root cause. Severity is about impact now, not cause.
- Do: re-triage every 30 minutes. A SEV2 resolved is a SEV4; a SEV3 spreading is a SEV1.
- Don't: under-SEV to avoid waking people. False SEV1s are cheaper than missed ones.
- Don't: wait for a second data point before paging on a potential SEV1.
