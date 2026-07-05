---
name: Team Charter
description: Creates a team charter covering mission, roles via RACI, decision rights, working norms, communication protocols, and a conflict protocol, built in a facilitated workshop and delivered as a fill-in template. Use when someone says "my new team needs a charter", "we keep stepping on each other's toes", "who decides what on this team", or "write our team's working agreement". Do NOT use for diagnosing dysfunction in an existing team - use team-health-check instead - or for setting the team's goals and key results - use okr-builder.
---

# Team Charter

A charter makes the implicit explicit. New and reorganized teams waste weeks on collisions a charter would have prevented - two people doing the same work, decisions stalling because nobody knows who owns them, norms enforced by whoever is most annoyed that day. The costly mistake this skill prevents is the manager-written charter: drafted alone, imposed top-down, ignored within a week. The document only binds people who built it.

## Inputs to collect

1. Team name, size, and whether it is new, reorganized, or existing-but-friction-y. For an existing team with unclear dysfunction, diagnose first with team-health-check - a charter papering over a trust problem fixes nothing.
2. The team's mandate as leadership states it, verbatim.
3. Known friction points: past collisions, stalled decisions, turf disputes. These become test cases for the draft.
4. Recurring decision types the team faces (prioritization, hiring, technical direction, spend).
5. Time zones and working-hours spread.

## Operating procedure

Order matters: mission comes before scope, scope before roles, roles before decision rights - each section is meaningless without the one above it.

### Step 1: Run it as a workshop

Schedule 60-90 minutes with the whole team. The facilitator brings the template below, not a finished draft. Every section gets filled with the team's words; the facilitator's job is forcing concreteness, not supplying content. Revisit the charter quarterly and after any major change (new members, new mandate) - a charter that no longer matches reality is worse than none, because it teaches the team to ignore written agreements.

### Step 2: Mission and success

Write why the team exists - the outcome only this team owns - plus who it serves (internal or external customers) and 2-3 measurable success signals. If the mission also describes a neighboring team, it is not done. Detailed goal-setting on top of the mission belongs to okr-builder.

### Step 3: Scope and boundaries

List what is explicitly in scope, what is explicitly out of scope and who owns it instead, and the key dependencies in both directions. The out-of-scope list prevents more conflict than the in-scope list; turf disputes live in the unwritten middle.

### Step 4: Roles via RACI

For each major work stream and recurring decision type, assign: Responsible (does the work), Accountable (owns the outcome - exactly one person, always), Consulted (input required before), Informed (told after). Two names in the A column is the single most common charter defect, and it means the decision will stall exactly when it matters. Replay the known friction points from intake against the grid: each past collision should now have an obvious answer.

### Step 5: Decision rights

Classify the recurring decision types into three modes: individual (the Accountable person decides, no meeting), consensus (the team decides together - reserve this for genuinely high-stakes, hard-to-reverse calls), and escalate (goes above the team, to a named person). Most teams over-allocate to consensus; every decision routed to consensus costs a meeting, so any decision that is cheap to reverse should be individual. Record the disagreement rule: debate openly before the decision, then commit to it out loud even if you argued the other side.

### Step 6: Working norms and communication

Norms must be concrete enough to check compliance.

**Bad:** "We communicate well and respect each other's time."

**Good:** "PRs get first review within 24 working hours. Chat gets same-day response during core hours (10:00-15:00 ET); email within 48h. No meetings Wednesdays. Decisions live in the decision log, not in chat scrollback."

The bad norm cannot be violated, so it cannot be enforced. Cover: core hours across time zones, which channels carry what (chat vs. docs vs. issues vs. meetings), response expectations per channel, the meeting load the team commits to and what is deliberately async, and definition of done for shared work.

### Step 7: Conflict protocol

Agree on the path before it is needed: (1) the two people involved talk directly within 48 hours of the friction - no triangulating through the manager or the group channel; (2) unresolved after a direct attempt, either party brings in the manager or a neutral facilitator; (3) safety, harassment, or ethics issues skip straight to HR. For running an actual mediation, pair with conflict-resolution.

### Step 8: Ratify and post

Each member states out loud what they are agreeing to and flags anything they cannot commit to - silence is not agreement. Pin the charter where the team works, cite it when friction arises, and put the quarterly review on the calendar before the workshop ends.

## Worked artifact: charter template

```
# [FILL: Team Name] Charter

## Mission
We exist to [FILL: outcome only this team owns], serving [FILL: customers].
Success signals: [FILL: 2-3 measurable]

## Scope
In: [FILL]        Out (and owner): [FILL: item -> owning team]
Dependencies: we rely on [FILL]; [FILL] relies on us for [FILL]

## Roles (RACI) - one A per row, no exceptions
| Work / decision type | R | A (exactly one) | C | I |
| [FILL]               |   |                 |   |   |

## Decision rights
Individual (A decides): [FILL: decision types]
Consensus (rare, hard-to-reverse): [FILL]
Escalate to [FILL: name]: [FILL]
Disagreement rule: debate before, commit after - out loud.

## Norms (each one checkable)
Core hours: [FILL] | PR review: within [FILL]h | Chat: [FILL] | Email: [FILL]h
Meetings we keep: [FILL] | Deliberately async: [FILL]
Definition of done: [FILL]

## Conflict protocol
Direct conversation within 48h -> facilitated by [FILL] -> HR for safety/ethics.

## Review
Quarterly on [FILL: recurring date]; also after new members or new mandate.
```

## Deliverable

Produce a ratified one-page charter containing: mission with 2-3 measurable success signals, explicit in/out scope with owners for out-of-scope work, a RACI grid with exactly one Accountable per row, decision types sorted into individual/consensus/escalate with the commit rule, checkable norms with response times per channel, the 48-hour conflict protocol, and a scheduled quarterly review.

## Do NOT

- Do not write it for the team; a charter without the team's fingerprints has no authority.
- Do not allow two names in any Accountable column - shared accountability is deferred conflict.
- Do not accept uncheckable norms; "communicate well" enforces nothing.
- Do not default decisions to consensus; reversible decisions belong to individuals.
- Do not skip decision rights to save workshop time - it is the section that prevents the most friction.
- Do not charter over an unresolved dysfunction; run team-health-check first.

## Quality bar

- Every past friction point from intake now has an unambiguous answer in the document.
- Every RACI row has exactly one A.
- Every norm can be violated - and therefore enforced.
- Every decision type has a mode and, for escalations, a named person.
- The team ratified out loud, the charter is posted where work happens, and the quarterly review is on the calendar.
