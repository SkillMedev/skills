---
name: Team Health Check
description: Assesses a team's health across three independent dimensions - delivery, morale, and clarity - using observable signals from the last 8 weeks, then maps each red or yellow signal to one manager action with a 2-week horizon. Use when a manager asks "is my team okay", "how do I tell if morale is slipping", "we keep missing sprints and I don't know why", or wants a quarterly team health review. Do NOT use for facilitating a single sprint retrospective - use sprint-retro-facilitator instead - for structuring individual 1:1s - use 1on1-agenda - or for defining how a new team works together - use team-charter instead.
---

# Team Health Check

Team health degrades slowly and then suddenly: by the time attrition or missed deadlines are visible to leadership, the manager has had months of earlier signals to act on. This skill makes those signals explicit and - the part most managers skip - forces each one into a single concrete action instead of a vague resolution to "keep an eye on it."

## Operating procedure

Assess the three dimensions independently and in this order - delivery first because its data is objective and anchors the conversation, clarity last because the diagnostic requires a team meeting you may need to schedule. Never collapse them into one score: a team can be green on delivery and red on morale, and a single score masks exactly the problem that needs fixing.

### Step 1: gather inputs

- Sprint or delivery data for the last 8 weeks: completion rate, carry-over counts, unplanned-work interruptions, time from "done" to deployed. 8 weeks, because a single bad sprint is noise and 4 sprints is a trend.
- 1:1 notes or the manager's recollection of the last month of 1:1s.
- PTO calendar and meeting-attendance patterns, if available.
- Any recent survey data - useful, but label it lagging: surveys trail reality by weeks.

Anything the manager reports from memory ("participation feels lower"), record as a guess and pair it with at least one observable before letting it drive an action.

### Step 2: score delivery

Rate red/yellow/green on the 8-week window:

- Sprint completion rate: green at or above 80%, yellow 60-80%, red below 60%.
- Items carried over more than twice: green 0, yellow 1-2, red 3+.
- Unplanned work interrupting the sprint: yellow if it exceeds 20% of capacity, red above 35%.
- "Done" to deployed: yellow if over 3 days, red if over a week.

Declining trends in any two of these warrant a process conversation. High carry-over usually points to scoping problems or dependency mismanagement, not team effort - check that before questioning commitment.

### Step 3: score morale

Morale is harder to measure but not invisible. Count how many of these fired in the window:

- Declining participation in optional meetings.
- Team-member-initiated 1:1s getting shorter, or 1:1s that feel performative - the primary instrument is weekly 1:1 quality.
- PTO clustering (several people booking time off in the same stretch without a holiday explanation).
- Drop in chat/async engagement.
- More closed-door conversations the manager is not part of.

Zero or one signal: green. Two: yellow. Three or more: red. Do not wait for survey confirmation to score red - the survey will confirm it a month too late.

### Step 4: score clarity

In the next team meeting, without priming, ask three questions:

1. What is the team's top priority this quarter?
2. Why does that priority matter to the company?
3. Who makes the final call when the team disagrees on an approach?

Green: answers substantially align. Yellow: aligned on the what, scattered on the why. Red: no two people give the same top priority, or nobody can name the decision-maker. Clarity problems are almost always a manager communication problem, not a team comprehension problem - write that in the scorecard before assigning any action.

### Step 5: map signals to actions

Pick ONE dimension to work on: the one that is both most degraded and most within the manager's direct control. Do not try to fix all three at once. Every red or yellow signal in that dimension gets one action with a 2-week horizon and a named check ("re-measure carry-over at the next sprint boundary"). Rules of thumb:

- Delivery problems respond to process changes (scoping rules, WIP limits, dependency review).
- Morale problems require individual conversations before structural changes - a reorg applied to a trust problem makes it worse.
- Clarity problems require the manager to write the priority down and repeat it consistently; saying it once in a meeting does not count as communicated.

## Worked artifact: scorecard template

```
TEAM HEALTH CHECK - [FILL: team] - [FILL: date] - window: last 8 weeks

DELIVERY: [FILL: R/Y/G]
  Completion rate: [FILL]%   Carry-over >2x: [FILL]   Unplanned: [FILL]%   Done->deployed: [FILL] days
MORALE: [FILL: R/Y/G] - signals fired: [FILL: list, mark guesses]
CLARITY: [FILL: R/Y/G] - three-question result: [FILL: one line]

FOCUS DIMENSION (one only): [FILL]
ACTIONS (each with 2-week check)
  1. [FILL: action] - owner: manager - re-check: [FILL: date + metric]
  2. [FILL]
PARKED (other dimensions' yellows, revisit next check): [FILL]

NEXT FULL CHECK: [FILL: date, quarterly default]
```

## Deliverable

Produce a completed scorecard containing: an independent red/yellow/green rating per dimension with the observables behind each, one chosen focus dimension, 1-3 actions each with a 2-week re-check, and the date of the next quarterly check.

## Do NOT

- Do not average the three dimensions into one health score - the average of a green and a red is a lie.
- Do not treat survey data as current; it lags weeks behind the 1:1 and behavioral signals.
- Do not respond to red morale with a process change first. Process is for delivery; morale starts with individual conversations.
- Do not prime the clarity questions ("as you know, our priority is X - so, what's our priority?"). A primed answer measures politeness, not clarity.
- Do not open actions on all three dimensions simultaneously; the team experiences that as churn, which itself damages morale.

## Quality bar

- Each dimension scored independently, with at least one observable (not a feeling) behind every non-green rating.
- Guesses are labeled as guesses and paired with an observable before driving action.
- Exactly one focus dimension; every action has a 2-week horizon and a named re-check.
- The scorecard is written down and comparable to last quarter's.

## Escalation

If morale stays red for two consecutive quarters despite intervention, stop treating it as a team problem - it is a signal about team-org fit, leadership above the manager, or a systemic issue the manager cannot resolve alone. Escalate upward with a written summary of what was tried and what did not move. For persistent interpersonal conflict surfaced by the check, route to conflict-resolution; for delivering hard individual feedback the check makes necessary, pair with feedback-writer.
