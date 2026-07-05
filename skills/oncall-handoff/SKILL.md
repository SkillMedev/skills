---
name: On-Call Handoff
description: Produces a complete on-call shift handoff note - shift summary, open incidents, watch items with paging thresholds, silenced alerts, in-flight changes, and escalation contacts - checked against a completeness checklist. Use when someone asks "write my on-call handoff", "what should I tell the next on-call", "my shift ends in an hour, help me hand over", or "make a handoff template for our rotation". Do NOT use for writing the incident postmortem after resolution - use postmortem-writer instead. For live severity assessment during an incident, use sev-triage; for external customer-facing incident comms, use status-page-update.
---

# On-Call Handoff

A handoff note transfers your mental model, not just your ticket queue. The incoming engineer should be able to respond to the next alert without asking you anything - because at 3am, "ask the previous on-call" is not an option, and the most expensive handoff failure is the one-line "quiet shift, nothing to report" that omits the silenced alert or the half-finished migration that pages them four hours later.

## Operating procedure

### Step 1: Gather the shift's raw material

Pull these before writing - memory alone misses exactly the items that matter:

1. Paging history for the shift: count and severity of pages.
2. Open or partially resolved incident tickets.
3. **Every alert you silenced, snoozed, or downgraded** - from the monitoring system, not memory. A silenced alert with no expiry is a landmine.
4. All deploys, config changes, migrations, and infra changes in the last 24 hours - including other teams' changes to shared systems if you know of them.
5. Anything in flight: fixes pending review, vendor tickets open, scheduled maintenance windows.

### Step 2: Write the note in this fixed order

The order is deliberate: the reader triages the note the same way they triage a page - summary first, active fires second, smoke third.

**1. Shift summary (2-3 lines).** SEV count by level, total pages received, one-sentence characterization. "Quiet - 2 SEV4 tickets, no pages." or "Rough - 1 SEV2 that took 4 hours to resolve, system is stable now."

**2. Open incidents.** For each open or partially resolved incident: ticket/incident ID and title; current status and last action taken; what you expect to happen next and by when; who owns it if not on-call. If there are none, say so explicitly: "No open incidents." Silence is ambiguous; the explicit line is not.

**3. Watch items (limit 3-5).** Things that are not incidents yet but could become one. For each: the signal, why it is concerning, and - critically - the threshold that should trigger paging, plus the dashboard link. Example: "Payment latency at p99 = 820ms, normal is under 400ms. No SLO breach yet. If it crosses 1s for 10 minutes, page the payments team. Link: [dashboard]." A watch item without a numeric threshold delegates a judgment call to someone with less context than you.

**4. Silenced and modified alerts.** Every alert currently silenced, snoozed, or with a temporarily raised threshold: which alert, why, and when the silence expires or should be lifted. If a silence has no expiry, set one now - before handing off.

**5. Recent changes (last 24h).** Deploys, config changes, migrations, infra changes. These are the first suspects when the next alert fires. One line each: `[Time] [Service] [What changed] [Who made it] [Rollback? Y/N]`. The rollback flag matters most - it is the difference between a 5-minute mitigation and a 2-hour investigation.

**6. Context and notes.** Anything needed that fits nowhere above: vendor communications in progress, scheduled maintenance windows, a known flaky alert, a fix pending review. Bullet points only. If a service involved has unusual architecture, add one sentence of context rather than assuming shared jargon.

**7. Escalation contacts (only if non-standard).** If a vendor ticket is open or an unusual escalation path is active, list the contact and reference number. Omit the standard rotation - it is in the paging tool.

### Step 3: Run the completeness checklist

Do not send until every box is checked:

- [ ] Every open incident has a next-expected-action and a time.
- [ ] Every watch item has a numeric paging threshold and a dashboard link.
- [ ] Every silenced alert is listed with a reason and an expiry.
- [ ] Every in-flight change (deploy, migration, pending fix) appears with its rollback status.
- [ ] "No open incidents" is stated explicitly if true - no section left blank.
- [ ] Every ticket, dashboard, and Slack thread is linked, not pasted in.

### Step 4: Deliver before the shift ends

Post the note before your shift ends, not after the next person has started - and offer a 5-minute synchronous overlap if any open incident is active. Written notes carry facts; the overlap carries the hunches.

## Worked artifact: filled handoff template

```
ON-CALL HANDOFF - [FILL: rotation name] - [FILL: date] - [FILL: your name] → [FILL: incoming name]

SHIFT SUMMARY
[FILL: e.g. "Moderate - 1 SEV3 (resolved), 6 pages total, 4 were the flaky
disk alert on db-replica-2."]

OPEN INCIDENTS
[FILL: "INC-4312 - checkout errors for EU users. Mitigated by failing over to
eu-west-2 at 14:20; root cause unknown. Expect networking team update by 09:00.
Owner: networking (Dana), not on-call." - or "No open incidents."]

WATCH ITEMS (max 5, each with a threshold + link)
1. [FILL: "Payment p99 latency at 820ms, normal <400ms. No SLO breach. Page
   payments team if >1s for 10 min. Dashboard: <link>"]

SILENCED / MODIFIED ALERTS
[FILL: "disk-usage-warn on db-replica-2 silenced until Tue 10:00 - known noisy
after Friday's compaction change, tracking in OPS-889." - or "None."]

RECENT CHANGES (last 24h)
[FILL: "16:40 | billing-svc | v2.14 deploy (invoice rounding fix) | Marco |
Rollback: Y"]

CONTEXT / NOTES
- [FILL: vendor tickets, maintenance windows, pending fixes, flaky alerts]

NON-STANDARD ESCALATION
[FILL: "AWS support case #8834412001 open re: NAT gateway - respond via the
case, not the TAM." - or "None."]
```

## Deliverable

Produce a posted handoff note in the seven-section order above, with every checklist box passing, delivered before shift end, plus a synchronous overlap offer when any incident is open.

## Do NOT

- Do not include solved incidents with no follow-up - past is past unless a watch item remains; resolved-incident storytelling buries the live signals.
- Do not under-communicate a watch item because it will "probably resolve" - if you are watching it, they need to know; the cost of one extra bullet is zero.
- Do not hand off a silenced alert without an expiry - an indefinite silence is how a real outage goes unnoticed.
- Do not copy-paste ticket or dashboard contents - link them; pasted content goes stale the moment the incident moves.
- Do not use team-internal jargon or codenames without one sentence of context - the incoming engineer may be from a sister rotation.
- Do not write the note after your shift ends - the gap between shifts is exactly when an unowned page lands.

## Quality bar

A handoff is done when the incoming engineer could handle the next page without contacting you: every live thread has a link, an owner, and an expected next step; every threshold is a number, not an adjective; and the note reads in under three minutes.

## Related skills

When a resolved incident needs a write-up, hand it to postmortem-writer - the handoff note is continuity, not analysis. During an active incident, severity and mobilization decisions belong to sev-triage. Recurring noisy alerts surfacing in every handoff are an alert-tuning problem; route them there instead of re-documenting them each shift.
