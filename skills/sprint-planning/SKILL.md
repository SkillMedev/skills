---
name: Sprint Planning
description: Facilitates sprint planning end to end — backlog refinement checks against a Definition of Ready, a one-sentence sprint goal, capacity math from velocity and PTO, story selection and team commitment, task breakdown, and flagged risks with owners. Use when a team asks "plan our next sprint", "how much should we commit to", "write a sprint goal", or keeps blowing sprint forecasts. Do NOT use for running the retrospective after the sprint ends — use sprint-retro-facilitator instead.
---

# Sprint Planning

Sprint planning answers two questions: **what** can we deliver this sprint, and **how**
will we do it? This skill keeps planning focused, realistic, and fast.

## Before planning: refine the backlog

Hold backlog refinement *ahead* of planning (mid-previous-sprint), so stories arrive ready:

- Each candidate story meets the **Definition of Ready**: clear, has acceptance criteria,
  estimated, dependencies known, small enough to finish in a sprint.
- Order the backlog by priority so the top is "next up".
- Split any story too big to fit a sprint.

Going into planning with an unrefined backlog is the #1 cause of bad sprints.

## Step 1: Set the sprint goal

Start with a single **sprint goal** — a one-sentence objective the sprint serves
(e.g. "Users can reset their password end to end"). The goal:
- Gives coherence and a reason to say no to scope creep.
- Lets the team make trade-offs mid-sprint without re-planning.

## Step 2: Determine capacity

Don't plan to 100% — plan to realistic capacity:

```
For each member:
  available days in sprint
  - planned PTO / holidays
  - meetings / on-call / support overhead (~20-30%)
  = effective capacity
```

Use the team's recent **velocity** (avg points completed over last 3-4 sprints) as the
anchor for how much to pull in. Adjust for capacity changes this sprint.

## Step 3: Select and commit

- Pull stories from the top of the refined backlog until you reach capacity/velocity.
- For each, the team confirms it understands the work and acceptance criteria.
- The team **commits** to a forecast it believes is achievable — commitment is the team's,
  not imposed. Leave a small buffer for the unexpected.

## Step 4: Break down into tasks

For committed stories, decompose into tasks (the "how"):
- Tasks are concrete steps ("write migration", "add endpoint", "QA flow").
- Surfacing tasks reveals hidden work and dependencies *before* the sprint starts.

## Step 5: Flag risks and dependencies

Explicitly call out:
- **Dependencies** on other teams / external inputs — and their status.
- **Unknowns** that could blow up estimates.
- **Single points of failure** (only one person can do X).

Note a mitigation or owner for each risk. Risks named on day 1 are manageable; risks
discovered on day 8 are fire drills.

## Planning meeting checklist

- [ ] Sprint goal stated in one sentence.
- [ ] Capacity calculated, not assumed.
- [ ] Every committed story meets Definition of Ready.
- [ ] Stories broken into tasks.
- [ ] Risks and dependencies flagged with owners.
- [ ] Team genuinely believes the forecast is achievable.

## Anti-patterns

- Planning to maximum capacity with no buffer — one surprise sinks the sprint.
- Committing to unrefined stories — they expand and derail the plan.
- A manager dictating scope — kills ownership and accuracy.
- No sprint goal — the sprint becomes a disconnected task list with no way to prioritize.
