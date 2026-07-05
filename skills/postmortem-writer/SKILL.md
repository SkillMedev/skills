---
name: Postmortem Writer
description: Writes blameless incident postmortems with a log-reconstructed timeline, multi-causal contributing factors, and owned, dated action items. Use when someone asks "write the postmortem for yesterday's outage", "run a blameless retro on this incident", "turn these incident notes into a postmortem doc", or after any SEV1 or SEV2 event. Do NOT use for customer-facing incident updates while the incident is live — use status-page-update instead; for transferring context at a shift change, use oncall-handoff; for classifying and running a live incident, use sev-triage.
---

# Postmortem Writer

A postmortem turns an incident into organizational learning. Done blamelessly, it makes systems safer; done as a witch hunt, it trains people to hide the next incident, which is the most expensive outcome available. The document is only worth writing if it produces owned, dated action items that get tracked to closure — everything else is theater.

## The blameless principle

Assume everyone acted reasonably given the information they had at the time. The goal is to fix systems and processes, never to assign fault to people. If a person could make the mistake, the system allowed it — fix the system. This is not softness; it is the only policy under which people report problems early.

## Operating procedure

### Step 1: Gather inputs

Collect before writing; label anything reconstructed from memory as unverified until confirmed against a log:

- Incident severity (SEV1–4), start and end times, and the incident channel or ticket.
- Raw artifacts: logs, alerts, dashboards, chat transcripts, deploy history, pager records. The timeline is reconstructed from these, not from memory alone.
- Impact data: users affected (how many, which segments), duration, and business impact (revenue, SLA breach, trust).
- Names of responders (as authors and reviewers, never as causes).

Timing discipline: draft within 48 hours while artifacts and memories are fresh; hold the review within 5 business days. SEV1 requires a full, exec-visible postmortem; SEV2 requires a full postmortem; SEV3–4 get a lightweight writeup with lessons captured.

### Step 2: Reconstruct the timeline

- Use minute-level timestamps in one declared timezone.
- Include the four mandatory anchors: impact start, detection, mitigation, resolution. If any anchor is missing, the timeline is not done.
- Separate what happened (events) from what responders knew (detection and perception). The gap between impact start and detection is usually the single biggest improvement opportunity — call it out explicitly with its duration.

### Step 3: Find contributing factors, not a root cause

Incidents are almost always multi-causal; a single "root cause" is a red flag. Run "5 whys" on each thread and stop at systemic answers, never personal ones.

Bad chain (stops at a person): "Why did the site go down? Bad config was deployed. Why? Alex made a typo. Done — Alex should be more careful."

Good chain (stops at process): "Why did the site go down? Bad config was deployed. Why did the typo reach production? Config changes are not validated pre-deploy. Why not? No schema check exists in the pipeline. Why? Config was assumed low-risk when the pipeline was built. Systemic fix: add config validation to CI."

If a "why" answer names a person, ask why the system let that action cause damage, and keep going.

### Step 4: Record what went well and what went poorly

Reinforce detection, response, and tooling that helped. Name gaps in monitoring, runbooks, and ownership — without naming individuals as gaps.

### Step 5: Write action items that stick

- Each action is specific, owned by one person, dated, and ticketed — or it will not happen.
- Classify each as prevent / detect faster / mitigate faster. A postmortem with only "prevent" items is under-invested in the next incident's response.
- Due dates within 30 days; anything larger gets split, with the first slice due inside 30 days.
- Prefer systemic fixes (guardrails, automated checks, safer defaults) over "be more careful" — humans do not become more careful on command.
- Track items to closure in the next review; an open postmortem with stale actions is theater.

### Step 6: Review and close

Circulate the draft, hold the review meeting within 5 business days, move status from Draft to Reviewed, and Closed only when every action item is done or explicitly re-owned.

## Template: postmortem skeleton

Copy and replace the [FILL] fields.

```
# Postmortem: [FILL: short incident title]
Date of incident: [FILL]   Severity: [FILL: SEV1-4]   Authors: [FILL]
Status: Draft | Reviewed | Closed

## Summary
[FILL: 2-4 sentences — what happened, impact, resolution. Readable by anyone.]

## Impact
- Users affected: [FILL: how many / which]
- Duration: [FILL: impact start -> detection -> mitigation -> resolution]
- Business impact: [FILL: revenue, SLA, trust]

## Timeline (all times in [FILL: TZ])
- [FILL: HH:MM]  Impact start — [FILL: what began failing]
- [FILL: HH:MM]  Detection — [FILL: how it was noticed; alert or human?]
- [FILL: HH:MM]  [FILL: mitigation step]
- [FILL: HH:MM]  Resolved — [FILL: what restored service]
Detection gap: [FILL: minutes between impact start and detection]

## Contributing factors
[FILL: the chain of conditions that allowed this — usually several. 5 whys per
thread, stopping at systemic answers, never at a person.]

## What went well
[FILL: detection, response, tooling that helped — reinforce these.]

## What went poorly
[FILL: gaps in monitoring, runbooks, ownership — no individual blame.]

## Action items
| Action | Type (prevent/detect/mitigate) | Owner | Due | Ticket |
|--------|-------------------------------|-------|-----|--------|
| [FILL] | [FILL]                        | [FILL]| [FILL: <=30d] | [FILL] |

## Lessons learned
[FILL: what the org now knows that it did not before.]
```

## Deliverable

Produce a complete postmortem document: summary, quantified impact, minute-level timeline with all four anchors and the detection gap stated, multi-causal contributing factors ending at systemic answers, what went well and poorly, and an action-item table where every row has a type, owner, due date within 30 days, and ticket.

## Do NOT

- Do not name and shame — it guarantees the next incident is hidden longer.
- Do not settle for a single "root cause" — incidents are multi-causal, and one cause means the analysis stopped early.
- Do not let "5 whys" terminate at a person — a chain ending in a name is an unfinished chain.
- Do not write action items without an owner and date — they evaporate.
- Do not skip the postmortem because "we're too busy" — the incident will run again on schedule.
- Do not reconstruct the timeline from memory alone — memory compresses and reorders; use logs, alerts, chat, and deploy history.

## Quality bar

- Timeline has minute-level timestamps, one timezone, all four anchors, and the detection gap called out.
- No individual is named as a cause anywhere; every "why" chain ends at process or system.
- At least two contributing factors, or an explicit justification for one.
- Every action item is specific, owned, dated within 30 days, ticketed, and classified prevent/detect/mitigate.
- Draft existed within 48 hours; review held within 5 business days; severity matches the SEV1–4 guide.
