---
name: hiring-a-players-and-sops
description: Build a gym's role-outcome hiring funnel — scorecards, work-sample screening, A-player pay logic, hire-order sequencing — and turn the owner's work into handoff-ready SOPs. Use when a gym owner says "hire a coach", "find A-players", "first hire for my gym", "build an interview process", "document my processes", "write an SOP", or "get out of the day-to-day". Do NOT use when the user needs a generic, inclusive, legally-careful job posting for any company — use job-description-writer instead.
---

# Hiring A-Players and SOPs

A gym stops scaling when the owner is the bottleneck. Run a role-outcome hiring funnel that finds people who produce results without supervision, and a four-step method that turns the owner's knowledge into SOPs someone else can run.

## Workflow

Pick the entry point: if the owner is drowning in operations, start at Step 5 (SOPs). If growth is capped by the owner's own selling or coaching hours, start at Step 1 (hiring).

### Step 1: Sequence the hire before defining it

A scaling single-location gym hires in this order:

1. Sales setter or salesperson first — book and close so the owner stops being the only seller. Frees the most owner time and funds the next hire.
2. Coach next — deliver sessions as the member base grows, protecting retention.
3. Manager last — own the daily operation and the scoreboard so the owner can step out of the seat.

Name which role you are hiring now and why it is next.

### Step 2: Define the role by its outcome, not a task list

A task list attracts task-doers; an outcome attracts people who want to win. Fill the role-scorecard before posting anything:

- Mission: one line stating what this role is responsible for producing.
- Outcomes with numbers: success at 90 days and 12 months (e.g. "book 60 consults/mo at 70% show rate").
- Competencies: only the few that actually predict success in that outcome.

### Step 3: Run a volume-based candidate funnel

Hiring is a funnel like lead generation — more candidates at the top means a better hire at the bottom.

- Source widely: job boards, your member base, referrals, local networks. Aim for many applicants.
- Screen fast: a short application plus one or two knockout questions.
- Use a work sample: finalists do the actual work — a setter runs a mock booking call, a coach runs a real session, a manager fixes a broken process on paper. Work samples predict performance far better than interviews.

See references/hiring-funnel for sourcing and screening detail.

### Step 4: Pay top of market and decide on cost per result

An A-player earns more in salary but costs less per result — more output, less management, longer tenure. Run `ab_player_cost.js`, then decide on cost per result, not on salary.

### Step 5: Document work into SOPs with the four-step method

Fill the sop-template for each process, starting with the highest-frequency or highest-risk ones (consult booking, front-desk open, new-member onboarding). See references/sop-method.

1. Do it: perform the task and capture every step, including the judgment calls.
2. Document it: write purpose, trigger, numbered steps, definition of done, owner. Short and specific beats long and vague.
3. Demonstrate it: walk someone through the SOP live; wherever they get confused, the document is missing a step; fix it.
4. Hand it off: let the new owner run it from the document while you watch once, then step away.

## Quality bar

- Every role on the scorecard has an outcome with a number, not a task list.
- Every finalist passed a work sample, not just an interview.
- The hire decision references cost per result from the calculator, not sticker salary.
- Every SOP states a trigger and a definition of done, and has been run by someone other than the author without you in the room.
- Each new role owns specific numbers an outside reader could check.

## Do NOT

- Do NOT write a generic, inclusive, EEO-careful job posting here — that is job-description-writer's job; this skill produces a gym-specific role scorecard tied to outcomes and pay.
- Do NOT hire out of sequence to fill a gap you could systematize with an SOP.
- Do NOT pick the cheapest candidate to save salary; price the output, not the sticker.
- Do NOT ship an SOP the author never watched someone else run.
- Do NOT define a role by tasks ("teach classes, clean equipment"); define it by the outcome it owns.

## Calculator

Self-contained Node script. Save as `ab_player_cost.js` and run with `node ab_player_cost.js`. No dependencies.

```javascript
// A-player vs B-player cost per result. Edit inputs, then: node ab_player_cost.js
const inputs = {
  aPlayerSalary: 70000,
  aPlayerResults: 100,  // results produced per year (e.g. members closed)
  bPlayerSalary: 45000,
  bPlayerResults: 45,
  targetResults: 100,   // results the gym needs per year
}

function compare(i) {
  const aCostPerResult = i.aPlayerSalary / i.aPlayerResults
  const bCostPerResult = i.bPlayerSalary / i.bPlayerResults
  const cheaperPct = (bCostPerResult - aCostPerResult) / bCostPerResult
  const aNeeded = i.targetResults / i.aPlayerResults
  const bNeeded = i.targetResults / i.bPlayerResults
  const aCost = aNeeded * i.aPlayerSalary
  const bCost = bNeeded * i.bPlayerSalary
  return { aCostPerResult, bCostPerResult, cheaperPct, aNeeded, bNeeded, aCost, bCost }
}

const r = compare(inputs)
const m = (n) => '$' + Math.round(n).toLocaleString('en-US')
console.log('A-player cost per result:', m(r.aCostPerResult))
console.log('B-player cost per result:', m(r.bCostPerResult))
console.log('A-player is', (r.cheaperPct * 100).toFixed(0) + '% cheaper per result')
console.log('To deliver', inputs.targetResults, 'results:')
console.log('  A-players needed:', r.aNeeded.toFixed(1), '=>', m(r.aCost))
console.log('  B-players needed:', r.bNeeded.toFixed(1), '=>', m(r.bCost))
console.log('  Savings with A-players:', m(r.bCost - r.aCost) + '/yr')
```

Worked example output:

```
A-player cost per result: $700
B-player cost per result: $1,000
A-player is 30% cheaper per result
To deliver 100 results:
  A-players needed: 1.0 => $70,000
  B-players needed: 2.2 => $100,000
  Savings with A-players: $30,000/yr
```

Read it: the A-player earns more in salary yet costs $300 less per result and saves $30,000/yr to deliver the same output — before counting the management time and turnover the B-players add. Pay for output, not for the lowest sticker price.

## Template: role-scorecard

```
ROLE SCORECARD. [FILL: role title]

MISSION (one line)
  [FILL: what this role produces]

OUTCOMES (with numbers)
  90 days:   [FILL]
  12 months: [FILL]
  Ongoing:   [FILL: e.g. book 60 consults/mo at 70% show]

COMPETENCIES (the few that predict success)
  [FILL]
  [FILL]
  [FILL]

INTERVIEW + WORK SAMPLE
  Screen question:   [FILL]
  Work sample:       [FILL: the actual task, e.g. mock booking call]
  Pass bar:          [FILL: what good looks like]
```

## Template: sop-template

```
SOP. [FILL: process name]

PURPOSE
  [FILL: why this process exists]
TRIGGER
  [FILL: what starts it]
STEPS
  1. [FILL]
  2. [FILL]
  3. [FILL]
DEFINITION OF DONE
  [FILL: how you know it was done right]
OWNER
  [FILL: role responsible]
```

## references/hiring-funnel

Sourcing: treat openings like a marketing campaign. Post where your candidate avatar looks, ask members and your network for referrals, and keep a pipeline warm even when you are not hiring. Volume at the top raises quality at the bottom.

Role-outcome definition: candidates rise to the bar you set. A task list ("teach classes, clean equipment") attracts people who want a job. An outcome ("members you coach hit their goals and renew at 80%") attracts people who want to win. Define and screen on the outcome.

Work-sample screening: interviews reward people who interview well. Work samples reward people who do the work well, which is what you are buying. Have a setter run a mock booking call, a coach run a real session with real members, a manager redesign a broken process. Watch how they actually perform.

A-player pay logic: A-players are not expensive, they are efficient. They produce more per dollar, need less oversight, and stay longer, so the fully loaded cost per result is lower even at a premium salary. Paying bottom of market usually costs more in output, management, and turnover. Run the calculator and decide on cost per result.

## references/sop-method

The four-step method turns tacit knowledge into a transferable process:

1. Do it. The person who already does the task performs it and records every step, including the small judgment calls they make without thinking.
2. Document it. Write it plainly: purpose, trigger, numbered steps, definition of done, owner. Short and specific beats long and vague.
3. Demonstrate it. Walk someone through the SOP live. Wherever they get confused, the document is missing a step; fix it.
4. Hand it off. Let the new owner run it from the document while you observe once, then step away. The test of an SOP is that it works without you in the room.

Worked example, consult-booking SOP:

- Purpose: turn a new lead into a booked consult fast.
- Trigger: a lead submits a form.
- Steps: call within 5 minutes; if no answer, text then email; offer two specific times; confirm with a calendar invite; send a reminder the day before.
- Definition of done: consult on the calendar with a confirmation sent.
- Owner: the setter.

Document the highest-frequency and highest-risk processes first. Each SOP you finish is one more thing the owner no longer has to do personally.
