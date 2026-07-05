---
name: Process Documentation
description: Documents recurring processes as step-by-step SOPs with decision points, edge cases, and a named owner, so anyone can run the process correctly without asking. Use when someone asks "write an SOP for this", "document our onboarding process", "this only lives in one person's head", or wants to delegate a recurring task safely. Do NOT use for incident-response or on-call operational runbooks - use runbook-writer instead; for customer-facing product documentation, use help-documentation.
---

# Process Documentation

A Standard Operating Procedure lets anyone perform a recurring process correctly without asking. The costly failure it prevents is silent: the one person who knows the process leaves, gets sick, or goes on vacation, and the team discovers the "documented" process was actually tribal knowledge. Good SOPs reduce errors, enable delegation, and survive turnover - but only if they cover the branches, not just the happy path.

## Operating procedure

Follow the steps in order: qualify before writing (most processes do not deserve an SOP), and validate before publishing (an untested SOP is a rumor with formatting).

### Step 1: Qualify the process

Write an SOP only when at least two of these hold; otherwise the documentation cost exceeds the risk:

- The process runs weekly or more often, or will be run by more than one person.
- A mistake costs more than an hour of rework, money, or customer trust.
- The knowledge currently lives in one person's head (bus-factor risk).

If the process fails all three, capture it as a two-line checklist note and stop.

### Step 2: Gather inputs

Collect from the process owner before drafting. Where they are unsure, record the answer and label it a guess to verify during validation:

- Process name, trigger (the event or schedule that starts it), and frequency.
- Who runs it today, and the least-experienced person who might run it later. Write for that person - no assumed tribal knowledge.
- Prerequisites: access, permissions, tools, and inputs required before step 1.
- The observable end state - "done when" - in one sentence.
- The failure stories: every time this process has gone wrong and what fixed it. These become the edge-case section, which is where the SOP's real value lives.

### Step 3: Draft the happy path

Number the steps in execution order. Rules that separate a usable SOP from a wall of prose:

- One action per step. If a step contains "and", split it.
- Start each step with a verb: "Open...", "Verify...", "Send...".
- Make results observable: every step that can fail silently gets an "Expected result: you should see X" line so the runner can self-check.
- Keep the SOP to 5-15 steps. Under 5, it is probably a checklist item; over 15, split it into linked sub-SOPs, because runners lose their place.
- Link, don't embed, anything that changes (URLs, contact names, dashboards) so the SOP ages well.

### Step 4: Capture decision points and edge cases

The happy path is the easy part; people get stuck at the branches.

- Write every decision point as an explicit if/then: "If the invoice is over $5,000 → route to finance approval; otherwise → approve directly."
- List every edge case actually hit (from the failure stories in Step 2) as symptom → what it means → how to fix.
- Name the escalation contact by role, not person, for situations the SOP does not cover.

### Step 5: Validate with the stranger test

Have someone who has never run the process follow the SOP start to finish without help. Every question they ask is a gap - fix the doc, not the runner. Do not publish an SOP that has not survived one full unassisted run.

### Step 6: Assign ownership and a review cadence

- Put an owner (role), last-updated date, and frequency in the header. A doc with no owner and no date silently rots.
- Review quarterly and after any process change. Prune dead steps aggressively: one outdated SOP erodes trust in all of them.

## Template: SOP skeleton

Copy this and replace the [FILL] fields.

```
# SOP: [FILL: process name]
Owner: [FILL: role]   Last updated: [FILL]   Frequency: [FILL: how often run]

## Purpose
[FILL: one sentence - what this accomplishes and why it matters.]

## When to run this
[FILL: the trigger event or schedule.]

## Prerequisites
- [FILL: access/permissions needed]
- [FILL: tools / inputs required]

## Steps
1. [FILL: verb + object - exactly what to do.]
   - Expected result: [FILL: what the runner should see]
2. ...

## Decision points
- If [FILL: condition] -> do [FILL: branch A].
- If [FILL: condition] -> do [FILL: branch B].

## Edge cases & troubleshooting
- [FILL: symptom] -> [FILL: what it means] -> [FILL: how to fix]

## Escalation
If the situation is not covered above, contact [FILL: role].

## Done when
[FILL: the observable end state.]
```

## Worked contrast: one step, two ways

Bad: "Handle the weekly export and make sure everything looks right before sending it out."

Good: "1. Open the Reports dashboard and click Export > Weekly CSV. Expected result: a file named `weekly-export-<date>.csv` downloads. 2. Verify the row count is within 10% of last week's (recorded in the log sheet). If it is not, stop and follow the edge-case entry for row-count drift. 3. Email the file to the distribution list linked in Prerequisites."

The bad version has three hidden actions, no observable result, and no branch for the one thing that actually goes wrong.

## Deliverable

Produce a complete SOP document containing: header with owner, date, and frequency; purpose and trigger; prerequisites; 5-15 numbered verb-first steps with expected results; explicit if/then decision points; edge cases with fixes; an escalation contact; and a "done when" end state - validated by one unassisted stranger run.

## Do NOT

- Do not write walls of prose instead of numbered steps - runners lose their place and skip actions.
- Do not document only the happy path - the branches are where people get stuck and where errors cost money.
- Do not publish without an owner and date - the doc rots invisibly and the first stale step destroys trust in the rest.
- Do not over-document trivial processes - match detail to risk and frequency, or nobody maintains it.
- Do not embed volatile details (URLs, names, prices) inline - link to the source of truth instead.

## Quality bar

Before shipping, verify:

- A person who has never run the process completed it start to finish using only the doc.
- Every step starts with a verb, contains one action, and failure-prone steps have an expected result.
- Every decision point is an if/then; every known failure story appears in edge cases.
- Header carries owner, date, and frequency; a quarterly review is scheduled.
- The "done when" state is observable, not "process complete".
