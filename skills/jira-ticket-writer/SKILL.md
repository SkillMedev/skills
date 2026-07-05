---
name: Jira Ticket Writer
description: Writes ready-to-build Jira stories with user-story summaries, Given/When/Then acceptance criteria, subtasks, and story-point guidance, checked against INVEST and a Definition of Ready. Use when someone asks "write a Jira ticket for this", "turn these meeting notes into a story", "are these acceptance criteria testable", "should I split this story", or "how many points is this". Do NOT use for configuring Linear teams, cycles, labels, or triage workflows — use linear-workflow instead. For deciding what fits in the sprint, use sprint-planning.
---

# Jira Ticket Writer

A good ticket is understandable by someone who was not in the conversation. The costly failure mode is the "fix the thing" ticket: it looks fine the day it is written, then a week later nobody can build it, estimate it, or verify it, and the sprint absorbs a re-discovery meeting that the ticket was supposed to replace. This skill produces stories that are ready to estimate and build on first read.

## Operating procedure

Follow the steps in order: the summary shapes the acceptance criteria, the criteria expose the edge cases, and the edge cases decide whether the story needs splitting. Estimating before the criteria exist produces numbers nobody can defend.

### Step 1: Gather inputs

Collect these before drafting. Where the requester does not know, record the best guess and label it a guess.

1. Who the user is (a real role — "billing admin", not "user").
2. The problem and why now — the source conversation, incident, or request to link.
3. The desired behavior in one sentence.
4. Known edge cases: empty states, error paths, permission boundaries.
5. Links to designs, API contracts, or prior decisions, if any exist.
6. What is explicitly NOT included, if the requester has said so.

### Step 2: Draft the summary as a user story

Use the shape `As a <user>, I want <capability> so that <benefit>`. If the benefit clause is hard to write, the story may not be valuable — surface that instead of faking one. For pure technical work (a migration, a library upgrade), skip the user-story shape and state the outcome plainly; do not contort "As a developer, I want a migration".

### Step 3: Fill the structured description

```
Summary: As a <user>, I want <capability> so that <benefit>

Description:
## Context
<Why now? What problem does this solve? Link to the source.>

## Acceptance Criteria
- [ ] Given <state>, when <action>, then <result>
- [ ] Given <edge case>, when <action>, then <result>

## Out of scope
- <explicitly not included>

## Notes / Design
<links to designs, API contracts, decisions>
```

### Step 4: Write acceptance criteria that QA can execute

- Write testable statements — Given/When/Then or a checkbox list a QA engineer could verify without asking anyone.
- Cover the happy path AND the key edge cases: empty state, error handling, permissions. A story with only the happy path is roughly half specified.
- If a criterion cannot be verified objectively ("works smoothly", "is fast"), rewrite it until it can ("p95 page load under 2s on the dashboard").
- Done means all criteria pass. No criteria means no shared definition of done.
- 3–7 criteria is the healthy range. One criterion usually means missing edge cases; ten or more usually means the story should split.

### Step 5: Check INVEST

Run the story against all six letters; failing any one is a signal to rework, not a formality:

- **Independent** — buildable without waiting on another open story. If not, name the dependency in the ticket.
- **Negotiable** — describes the outcome, not the implementation; the how stays open.
- **Valuable** — the "so that" clause names a benefit someone would pay for or notice.
- **Estimable** — the team can put a point number on it. If they cannot, the unknowns belong in a spike, not this story.
- **Small** — completable within one sprint by one or two people.
- **Testable** — every criterion passes Step 4.

### Step 6: Break into subtasks when warranted

Split into subtasks when the story spans more than ~1–2 days of work, or when multiple people or disciplines are involved (backend + frontend + QA). Each subtask must be independently completable and named with an action verb ("Add migration for X", "Wire up Y endpoint"). Do not create subtasks so small they are noise — a 30-minute subtask is a checkbox in the description, not a ticket.

### Step 7: Estimate in points, then gate on the Definition of Ready

Estimate relative effort, complexity, and uncertainty — not hours. Fibonacci-ish scale, anchored to a shared reference story:

- **1** — trivial, well understood, under half a day.
- **2–3** — small, clear, no unknowns.
- **5** — medium; some complexity or coordination.
- **8** — large or uncertain; consider splitting.
- **13** — too big; split it before committing. 13+ point stories committed as-is always slip.

If the team cannot agree within one or two points, the story is under-specified — discuss and clarify, never average the estimates.

Definition of Ready, before the story enters a sprint:

- [ ] Summary follows the user-story format (or plainly states a technical outcome).
- [ ] Acceptance criteria are testable and cover edge cases.
- [ ] Dependencies and out-of-scope are noted.
- [ ] Designs/contracts linked where needed.
- [ ] Estimated, and small enough to finish in one sprint.

## Worked example: bad vs good

**Bad:**

```
Summary: Fix the export
Description: Users are complaining about exports. Make it work properly.
Points: 5
```

Unbuildable: no user, no defined behavior, no way to verify done, and the 5 is a guess about a mystery.

**Good:**

```
Summary: As a billing admin, I want to export invoices as CSV so that I can
reconcile them in our accounting system

Description:
## Context
Finance reconciles invoices monthly in NetSuite; today they copy-paste from the
UI (~2h/month). Requested in #finance-ops, 2024 close retro. [link]

## Acceptance Criteria
- [ ] Given an org with invoices, when the admin clicks "Export CSV" on the
      Invoices page, then a CSV downloads with columns: invoice_id, date,
      amount, currency, status.
- [ ] Given an org with zero invoices, when the admin exports, then an empty
      CSV with headers downloads (no error).
- [ ] Given a non-admin user, when they view the Invoices page, then the
      export button is not shown.
- [ ] Given an export of 10,000+ invoices, when triggered, then it completes
      or fails with a visible error — never silently truncates.

## Out of scope
- PDF export; scheduled/recurring exports.

## Notes / Design
- Figma: [link]. CSV spec matches NetSuite import template: [link].

Points: 3
```

## Deliverable

Produce a paste-ready Jira ticket containing: a user-story summary, a Context section with a linked source, 3–7 testable Given/When/Then acceptance criteria covering happy path and edge cases, an Out of scope list, linked notes/designs, subtasks where the 1–2 day rule triggers, and a point estimate with the split recommendation if it lands at 8 or above.

## Do NOT

- Do not write "fix the thing" tickets with no context — they are unbuildable a week later, and the context is cheapest to capture now.
- Do not write acceptance criteria that restate the title instead of defining behavior — they create the illusion of specification.
- Do not commit 13+ point stories as-is — the size hides unknowns, and unknowns are what slip.
- Do not estimate in hours and call them points — hours anchor to the fastest engineer and destroy the relative scale.
- Do not resolve estimate disagreement by averaging — a 2-vs-8 split means two different stories are in people's heads.

## Quality bar

Before the ticket ships to the backlog:

- A developer who missed every meeting could start building from the ticket alone.
- Every criterion is objectively verifiable by QA without a conversation.
- Edge cases (empty, error, permission) are covered or explicitly ruled out of scope.
- The story passes all six INVEST checks and every Definition of Ready box.
- The point estimate is 8 or below, or a split is proposed in the ticket.

## Related skills

For setting up the tracker itself — states, cycles, triage — use linear-workflow. For deciding which ready tickets fit the sprint, use sprint-planning. For turning a raw meeting into candidate tickets first, pair with meeting-notes-to-actions.
