---
name: OKR Builder
description: Drafts a quarter's OKRs - an inspiring qualitative objective with 3-5 measurable key results, each with a baseline, target, and owner - plus the weekly confidence check-in and end-of-quarter grading cadence. Use when someone asks "help me write OKRs", "are these key results any good", "turn our goals into OKRs for next quarter", or "how do we score our OKRs". Do NOT use for building the full-year company plan that OKRs slot into - use annual-plan instead - or for personal goal-tracking with a check-in partner - use goals-accountability instead.
---

# OKR Builder

OKRs align effort around outcomes, and they fail in one predictable way: key results that are really a to-do list in disguise, so the team "completes" everything and moves nothing. The job of this skill is to produce few, measurable, honest OKRs - where a 0.7 at quarter end is a good result on a stretch goal, not a failure to explain away.

## Operating procedure

### Step 1: gather inputs

Collect before drafting; label anything the user estimates as a guess.

- The company or org priority this quarter rolls up to (one sentence). If the user cannot state it, that is the first problem - OKRs written in a vacuum get orphaned. If no annual plan exists, route to annual-plan first.
- The team's candidate goals, in whatever raw form exists.
- Current baselines for any metric that might become a key result. No baseline means no KR yet - default to spending the first two weeks of the quarter instrumenting it, and say so in the OKR.
- Who is available to own each key result.

### Step 2: draft the objectives - max 3

An objective is qualitative, inspiring, and short: where the team wants to be, not what it will do. Hard cap at 3 objectives per team per quarter; 1-2 is better. Focus is the point - every objective added past three dilutes the others by more than a third, because attention doesn't split evenly.

### Step 3: draft 3-5 key results per objective

Each KR must be a number that proves the objective happened. Apply three filters in order:

1. Outcome, not activity. "Launched the referral feature" is a task; "30% of new signups come through referral" is a KR. If a KR could be marked done by shipping something regardless of whether anyone's behavior changed, rewrite it.
2. Two-person test. Could two reasonable people disagree at quarter end on whether it was hit? If yes, make it more measurable.
3. Baseline and target. Every KR states where the number is now and where it should be. "Improve NPS" fails; "NPS from 31 to 45" passes.

Then assign exactly one owner per KR. Owners drive the number; they don't do all the work alone.

### Step 4: calibrate ambition

Set stretch KRs so that landing about 70% is a good outcome - the target should feel roughly 50/50 achievable at full effort. If a KR must be hit at 100% (a compliance deadline, a contractual commitment), label it committed rather than aspirational and grade it pass/fail; mixing the two types without labels is how sandbagging starts. A team that scores 1.0 across the board sandbagged; a team averaging below 0.4 either planned badly or hit a real obstacle worth a written postmortem paragraph.

### Step 5: set the cadence

- Set quarterly.
- Check in weekly: each KR owner posts the current number and a confidence score (0.1-1.0, "how likely are we to hit the target?"). Two consecutive weekly confidence drops on the same KR triggers a conversation, not a silent re-forecast.
- No mid-quarter target edits. If the world genuinely changed (reorg, market shock), annotate the KR and grade against both the original and revised target at quarter end.
- Grade at quarter end on the 0.0-1.0 scale, honestly, and write one sentence of learning per miss. The grading meeting is for learning, not for performance review - the fastest way to kill honest stretch targets is to tie scores to compensation.

## Worked example: bad/good pair

Bad:

```
Objective: Improve the product.
KRs:
- Ship the new onboarding flow
- Migrate to the new billing system
- Do 20 customer interviews
```

Every KR is an activity. The team can go 3-for-3 while activation, revenue, and insight all stay flat. "Improve the product" gives no direction on what improvement means.

Good:

```
Objective: Make the first week so good that new users stay.
KRs:
- Week-1 retention from 22% to 35% (owner: Dana)
- Median time-to-first-value from 26 min to under 10 min (owner: Ravi)
- Activation-step completion from 41% to 60% (owner: Dana)
```

Each KR is an outcome with a baseline, target, and owner. The onboarding flow, the billing migration, and the interviews may all still happen - as means, not as the score.

## Template

```
QUARTER: [FILL]   TEAM: [FILL]   ROLLS UP TO: [FILL: company priority]

OBJECTIVE 1 (of max 3): [FILL: qualitative, inspiring, short]
  KR 1.1: [FILL: metric] from [FILL: baseline] to [FILL: target] - owner: [FILL] - type: aspirational / committed
  KR 1.2: [FILL] from [FILL] to [FILL] - owner: [FILL] - type: [FILL]
  KR 1.3: [FILL] from [FILL] to [FILL] - owner: [FILL] - type: [FILL]

CADENCE
  Weekly check-in: [FILL: day/channel] - owners post number + confidence 0.1-1.0
  End-of-quarter grading: [FILL: date] - 0.0-1.0 per KR + one learning sentence per miss
```

## Deliverable

Produce a filled OKR sheet containing: at most 3 objectives, 3-5 key results each with baseline, target, owner, and aspirational/committed label, the named weekly check-in slot, and the quarter-end grading date.

## Do NOT

- Do not write activity KRs. "Shipped X" measures effort; OKRs measure whether the effort worked.
- Do not exceed 3 objectives or 5 KRs per objective. Past those caps the document becomes a backlog, and backlogs already exist.
- Do not treat 0.7 on a stretch KR as failure - that framing teaches teams to sandbag next quarter.
- Do not grade committed and aspirational KRs on the same curve; a missed committed KR is a real miss requiring an explanation, not a 0.8.
- Do not silently move targets mid-quarter. Annotate and dual-grade instead.
- Do not tie OKR scores to compensation or ratings; it converts every target-setting session into a negotiation.

## Quality bar

- Every KR passes the two-person test and has a baseline, target, and single owner.
- No KR is an activity in disguise.
- Objective count is 3 or fewer; KR count per objective is 3-5.
- Aspirational vs committed is labeled on every KR.
- The weekly confidence check-in and quarter-end grading date are written down, not implied.
