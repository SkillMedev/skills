---
name: retainer-pricing-calculator
description: Prices monthly freelance retainers from capacity math (billable hours at 50-60 percent of working hours), an effective-rate floor, and a value-anchor adjustment, with utilization red lines and a runnable Node calculator. Use when a freelancer says "what should I charge for a retainer", "how many clients can I actually take", "price my monthly package", "am I undercharging", "my calendar is full but I'm broke", or "what's my minimum viable rate". Do NOT use for pricing a software product or subscription tiers — use saas-pricing instead; for one-time salary or offer negotiation, use salary-negotiation.
---

# Retainer Pricing Calculator

Freelancers price retainers by gut, and the gut consistently forgets two things: that only half of a working month is billable, and that a retainer sells guaranteed availability, which is worth more than the hours inside it. The result is the classic trap — fully booked and still underpaid. This calculator prices from capacity up and value down, and tells the freelancer the floor below which a retainer quietly loses money.

This is the third skill in the Freelancer / Consultant OS setup sequence: freelance-positioning chose the niche, productized-service-designer built the tiers, and this skill validates the prices. Worked example: Maya, a freelance brand designer at $6,000/month on hourly work at $75/hr, pricing her Brand System Retainer.

## Operating procedure

### Step 1: Collect the inputs

Label any guess as a guess.

1. Working hours per month (Maya: 40 hrs/week x 4 = 160).
2. Target monthly gross income (Maya: $9,000 — a deliberate raise from her current $6,000).
3. Monthly business overhead: software, insurance, accounting, fees (Maya: $500).
4. Hours budgeted per retainer client per month (Maya: 36, from her Tier 2 scope in productized-service-designer).
5. Current hourly rate, for the before/after comparison (Maya: $75).

### Step 2: Do the capacity math

Billable hours are realistically **50-60 percent of working hours**. The other 40-50 percent goes to sales, admin, invoicing, marketing, and email — it does not disappear because the freelancer wishes it away. Use 0.55 as the default; 0.60 only for freelancers with steady inbound and minimal admin; below 0.50 while actively building a pipeline.

Maya: 160 x 0.55 = **88 billable hours/month**. This number is the whole ballgame — every promise sold must fit inside it.

### Step 3: Compute the effective-rate floor

```
floor hourly = (target income + overhead) / billable capacity
floor retainer = floor hourly x hours per client
```

Maya: ($9,000 + $500) / 88 = **$107.95/hr** floor; x 36 hrs = **$3,886 floor retainer**. Any retainer priced below the floor means the target income is arithmetically unreachable at full capacity. The floor is a red line, never the price.

### Step 4: Apply the value-anchor adjustment

The floor prices hours; the retainer also sells reserved capacity, priority turnaround, and accumulated context. Multiply the floor by a value anchor of **1.15-1.5**: 1.15-1.25 when proof in the niche is thin, 1.25-1.4 with solid case studies, up to 1.5 when the deliverable moves client revenue directly and the freelancer can show it. Round up to a clean number.

Maya, with three food-and-beverage case studies, uses 1.25: $3,886 x 1.25 = $4,858, rounded to **$4,900/month** — the Tier 2 price in productized-service-designer.

### Step 5: Check the utilization red lines

- **80 percent of capacity is the ceiling for sold retainer hours** (Maya: 70.4 of 88). The buffer absorbs overruns, sick days, and sales time; selling to 100 percent guarantees either missed deadlines or a dead pipeline — and sustained utilization above 80 percent is also the trigger condition in raise-your-rates-playbook.
- Max clients = floor(capacity / hours per client). Maya: floor(88/36) = **2 retainer clients**, using 72 hours (82 percent — at the line, so any third engagement must be small or replace one).
- If income at the full roster (clients x retainer) misses the target, the fix is price or scope, not a third client that breaks the red line.

### Step 6: Run the calculator and read the verdict

## Calculator

Self-contained Node script. Save as `retainer_calc.js`, edit the inputs, run `node retainer_calc.js`. No dependencies.

```javascript
// Retainer pricing calculator. Edit the inputs, then: node retainer_calc.js
const inputs = {
  workingHoursPerMonth: 160,   // total working hours (40 hrs/week x 4 weeks)
  billableShare: 0.55,         // realistic billable fraction (0.50-0.60)
  targetMonthlyIncome: 9000,   // gross income goal for the month
  monthlyOverhead: 500,        // software, insurance, subcontractors, fees
  retainerHoursPerClient: 36,  // hours budgeted per retainer client per month
  valueMultiplier: 1.25,       // value-anchor adjustment (1.15-1.5)
  currentHourlyRate: 75,       // what the freelancer charges today
}

function model(i) {
  const capacityHours = i.workingHoursPerMonth * i.billableShare
  const floorHourly = (i.targetMonthlyIncome + i.monthlyOverhead) / capacityHours
  const floorRetainer = floorHourly * i.retainerHoursPerClient
  const targetRetainer = Math.ceil((floorRetainer * i.valueMultiplier) / 100) * 100
  const maxRetainerClients = Math.floor(capacityHours / i.retainerHoursPerClient)
  const utilizationRedLine = capacityHours * 0.8
  const effectiveRateAtTarget = targetRetainer / i.retainerHoursPerClient
  const incomeAtFullRoster = targetRetainer * maxRetainerClients
  return { capacityHours, floorHourly, floorRetainer, targetRetainer,
    maxRetainerClients, utilizationRedLine, effectiveRateAtTarget, incomeAtFullRoster }
}

const r = model(inputs)
const money = (n) => '$' + n.toFixed(2)
console.log('Billable capacity (hrs/month):   ', r.capacityHours.toFixed(1))
console.log('Effective-rate floor (hourly):   ', money(r.floorHourly))
console.log('Retainer floor price:            ', money(r.floorRetainer))
console.log('Target retainer (value-anchored):', money(r.targetRetainer))
console.log('Max retainer clients:            ', r.maxRetainerClients)
console.log('Utilization red line (hrs):      ', r.utilizationRedLine.toFixed(1))
console.log('Effective rate at target:        ', money(r.effectiveRateAtTarget))
console.log('Income at full roster:           ', money(r.incomeAtFullRoster))
console.log('Vs current hourly rate:          ', money(inputs.currentHourlyRate), '->', money(r.effectiveRateAtTarget))
```

### Worked example output

With Maya's inputs above the script prints:

```
Billable capacity (hrs/month):    88.0
Effective-rate floor (hourly):    $107.95
Retainer floor price:             $3886.36
Target retainer (value-anchored): $4900.00
Max retainer clients:             2
Utilization red line (hrs):       70.4
Effective rate at target:         $136.11
Income at full roster:            $9800.00
Vs current hourly rate:           $75.00 -> $136.11
```

Read it: Maya has 88 billable hours, not 160. Her floor is $107.95/hr — her current $75/hr rate was below her own floor, which is exactly why full months still produced only $6,000. The value-anchored retainer lands at $4,900; two clients fill 72 of 88 hours (right at the 80 percent line) and produce $9,800/month at an effective $136/hr — a raise of over 80 percent in effective rate while working fewer billed hours.

## Concrete thresholds

- Billable share: 50-60 percent of working hours; default 0.55.
- Value anchor: 1.15-1.5 on the floor, scaled to proof strength.
- Utilization red line: sold hours at or below 80 percent of billable capacity; sustained above 80 percent triggers raise-your-rates-playbook.
- Never price at or below the floor; the floor is the walk-away number, not the offer.
- Re-run the calculator whenever scope, overhead, or the income target changes, and at every rate review.

## Deliverable

The filled calculator output plus a one-line pricing verdict: floor price, target retainer, max clients, and whether the full roster hits the income target. This validates (or corrects) the tier prices in productized-service-designer.

## Do NOT

- Do not assume 100 percent billable hours; that single error understates the needed rate by nearly half.
- Do not present the floor price to clients — it is an internal red line, and anchoring on it invites negotiation below it.
- Do not add a client past the 80 percent red line to hit an income target; raise the price instead.
- Do not convert the retainer back to an hourly comparison in front of the client ("that's $136/hr!") — the retainer sells an outcome and reserved capacity, and re-hourly-izing it invites hourly objections.
- Do not skip the value anchor out of modesty; pricing at the bare floor leaves the freelancer with zero margin for a single bad month.

## Quality bar

Done when: every input is sourced or labeled a guess; the target retainer clears the floor with the anchor applied; the roster respects the 80 percent red line; and the full-roster income is compared against the target with an explicit pass/fail.

## Escalation and neighbors

Not financial or tax advice — an accountant should sanity-check the overhead and income targets against taxes and benefits, which this model treats as inside the target figure. Scope the hours-per-client input in productized-service-designer; contract the price with statement-of-work-writer; when utilization holds above 80 percent, go to raise-your-rates-playbook.
