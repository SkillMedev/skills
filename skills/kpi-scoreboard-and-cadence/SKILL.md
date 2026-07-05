---
name: kpi-scoreboard-and-cadence
description: Build a gym's KPI scoreboard, separate leading from lagging indicators, and set the daily/weekly/monthly meeting cadence that turns numbers into decisions. Use when a gym owner asks what KPIs to track, wants a scoreboard or numbers huddle, asks for a weekly meeting agenda, or wants to find the weakest stage of their lead-to-member funnel. Do NOT use to compute CAC, LTV, or the cash-flow money model — use gym-money-model instead; do NOT use to fix churn or the sales/booking conversion the scoreboard flags — use retention-and-churn-killer, objection-handling-and-speed-to-lead, or closer-sales-script instead.
---

# KPI Scoreboard and Cadence

Pick the handful of numbers a gym runs on, organize them by funnel stage, and review them on a fixed rhythm where each meeting ends in a decision.

## Workflow

### Step 1: Track the KPIs by funnel stage

Organize metrics along the path from stranger to retained member, so a problem shows up at the stage it lives in. Definitions, formulas, sources, and healthy ranges are in the "KPI reference" section below.

- Leads: new prospects captured. Source: ad platform, forms, manual log.
- Booked: leads who scheduled a consult. Source: booking calendar.
- Shown: booked consults who attended. Source: consult log.
- Closed: shows who joined. Source: sales log.
- Active members: current paying members. Source: billing.
- Churn: members lost this month divided by members at the start. Source: billing.
- Revenue: recurring plus front-end. Source: billing.
- CAC and LTV:CAC: produced by gym-money-model; pull the latest figures in.

### Step 2: Separate leading from lagging indicators

- Leading indicators predict the future and you can act on them today: leads, booked, shown, daily activity, attendance. Manage these daily and weekly.
- Lagging indicators report the past: revenue, active members, churn, LTV:CAC. Review these weekly and monthly. They confirm whether the leading work paid off.

Managing only lagging numbers is driving by the rearview mirror. Move the leading numbers to change the lagging ones.

### Step 3: Build the morning scoreboard

Put the few numbers the owner reviews every morning on one screen or board. Fill the "Scoreboard template" below. If it does not fit on one view, it has too many numbers.

### Step 4: Find the weakest stage each week

Copy the script in the "Calculator" section into a file named `scoreboard.js`, edit the week's funnel numbers and benchmarks, and run `node scoreboard.js`. It returns each stage's conversion rate, the weakest stage against benchmark, and the neighbor skill to apply. Fixing the single weakest stage moves more members than tuning everything at once.

### Step 5: Run the cadence

Three meetings, each producing one decision. Full agendas are in the "Meeting-agendas template" below.

- Daily numbers huddle (10 minutes): yesterday's leads, booked, shown, closed, and today's targets. Decision: what each person does today to hit the number.
- Weekly scorecard review (30-45 minutes): the week's funnel and conversion rates, the weakest stage, and the fix. Decision: the one stage to improve and who owns it.
- Monthly economics review (60 minutes): revenue, churn, CAC, LTV:CAC, and the money model. Decision: where to invest next month and which lever to pull.

## Quality bar

- The scoreboard fits on one screen and shows targets next to actuals.
- Every number has a named owner and a source.
- Each meeting ends in a written decision with an owner, not a status recap.
- Each week names exactly one weakest stage to fix, then checks next week whether it moved.

## Do NOT

- Do not put forty numbers on the scoreboard. A scoreboard with eight numbers gets used; one with forty gets ignored.
- Do not manage only lagging numbers; you cannot act on the past.
- Do not fix every stage at once; the weakest stage returns the most.
- Do not let a meeting end without a decision and an owner.
- Do not redefine CAC, LTV, or the money model here; consume those figures from gym-money-model.

## Calculator

Self-contained Node script. Save as `scoreboard.js` and run `node scoreboard.js`. No dependencies.

```javascript
// Weekly funnel scoreboard. Edit inputs, then: node scoreboard.js
const week = {
  leads: 120,
  booked: 54,
  shown: 38,
  closed: 14,
}

// Healthy benchmarks for a local gym (edit to your own history).
const benchmarks = {
  leadToBooked: 0.50,
  bookedToShown: 0.70,
  shownToClosed: 0.40,
}

const fixes = {
  leadToBooked: 'objection-handling-and-speed-to-lead (faster contact, follow-up to book)',
  bookedToShown: 'objection-handling-and-speed-to-lead (reminders and confirmation sequence)',
  shownToClosed: 'closer-sales-script (sharpen the consult and the close)',
}

const stages = {
  leadToBooked: week.booked / week.leads,
  bookedToShown: week.shown / week.booked,
  shownToClosed: week.closed / week.shown,
}

// Weakest = largest shortfall below benchmark (in percentage points).
let weakest = null
let worstGap = Infinity
for (const k of Object.keys(stages)) {
  const gap = stages[k] - benchmarks[k]
  if (gap < worstGap) { worstGap = gap; weakest = k }
}

const pct = (n) => (n * 100).toFixed(1) + '%'
console.log('STAGE CONVERSIONS (actual vs benchmark)')
for (const k of Object.keys(stages)) {
  console.log('  ' + k.padEnd(14), pct(stages[k]), 'vs', pct(benchmarks[k]))
}
console.log('Weakest stage:', weakest, '(' + (worstGap * 100).toFixed(1) + ' pts vs benchmark)')
console.log('Focus this week:', fixes[weakest])
```

### Worked example output

```
STAGE CONVERSIONS (actual vs benchmark)
  leadToBooked   45.0% vs 50.0%
  bookedToShown  70.4% vs 70.0%
  shownToClosed  36.8% vs 40.0%
Weakest stage: leadToBooked (-5.0 pts vs benchmark)
Focus this week: objection-handling-and-speed-to-lead (faster contact, follow-up to book)
```

Read it: booking is the weakest stage at 45 percent against a 50 percent benchmark, a 5-point gap, while shows and closes are near target. The fix is not more ads or a new script; it is faster lead contact and better follow-up to get booked. One stage, one owner, one week. Re-run next week to confirm it moved.

## Scoreboard template

The few numbers reviewed every morning.

```
GYM SCOREBOARD. [FILL: gym name]. [FILL: date]

THIS WEEK            TARGET    ACTUAL
  Leads               [FILL]    ____
  Booked              [FILL]    ____
  Shown               [FILL]    ____
  Closed (joined)     [FILL]    ____

HEALTH (lagging)
  Active members      [FILL]    ____
  Churn this month    [FILL]%   ____
  Revenue (MTD)       $[FILL]   ____
  LTV:CAC             [FILL]:1  ____

WEAKEST STAGE THIS WEEK: [FILL from scoreboard.js]
FOCUS + OWNER:           [FILL]
```

## Meeting-agendas template

```
DAILY HUDDLE (10 min, every morning)
  1. Yesterday: leads / booked / shown / closed
  2. Today's targets per person
  3. Blockers
  -> Decision: what each person does today to hit the number

WEEKLY SCORECARD (30-45 min)
  1. Week's funnel and conversion rates (run scoreboard.js)
  2. Weakest stage and why
  3. Last week's focus: did it move?
  -> Decision: the one stage to fix and who owns it

MONTHLY ECONOMICS (60 min)
  1. Revenue, active members, churn
  2. CAC, 30-day cash, LTV:CAC (from gym-money-model)
  3. Which lever is constrained
  -> Decision: where to invest next month, which lever to pull
```

## KPI reference

Each metric, definition, formula, source, and healthy range for a local gym:

- Leads. New prospects captured. Source: ad platform and forms. Range: set by goal and spend; track the trend.
- Booked. Leads who scheduled a consult. Lead-to-booked formula: booked / leads. Source: calendar. Healthy: around 50 percent.
- Shown. Booked who attended. Booked-to-shown: shown / booked. Source: consult log. Healthy: around 70 percent.
- Closed. Shows who joined. Show-to-close: closed / shown. Source: sales log. Healthy: 30 to 50 percent.
- Active members. Current paying members. Source: billing. The base everything scales from.
- Churn. Members lost / members at month start. Source: billing. Healthy: 3 to 6 percent monthly for a local gym; lower is better.
- Revenue. Recurring dues plus front-end. Source: billing. Track recurring separately, since it is the stable base.
- CAC. Fully loaded acquisition cost per member, from gym-money-model.
- LTV:CAC. Lifetime contribution over CAC, from gym-money-model. Healthy: at or above 3:1.

Pick the few that drive decisions. A scoreboard with forty numbers gets ignored; one with eight gets used.

## Cadence reference

The rhythm matters as much as the metrics. Each meeting has a fixed length, a fixed agenda, and produces one decision.

- Daily huddle. Short and standing. Yesterday's funnel and today's targets. Its job is focus: everyone leaves knowing the number they own today. Keep it to ten minutes; problems that need longer get scheduled, not solved here.
- Weekly scorecard. The core operating meeting. Review the week's conversion rates, name the weakest stage, check whether last week's fix worked, and assign this week's single focus. One stage, one owner. Resist fixing everything; the weakest stage returns the most.
- Monthly economics. Step back to the money model. Revenue, churn, CAC, and LTV:CAC tell you whether the machine is healthy and where to invest. The decision is allocation: which lever (acquisition, conversion, retention, price) gets next month's attention and budget.

The pattern across all three: look at the numbers, find the one thing to change, assign an owner, and check it next time. A cadence without a decision is just a status update.
