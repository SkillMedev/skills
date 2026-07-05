---
name: Retirement Projection
description: Projects retirement readiness from savings rate, target nest egg, and compound growth - computes the required portfolio via safe-withdrawal-rate math, years to financial independence, and pension or Social Security offsets, with a runnable calculator. Use when someone asks "how much do I need to retire", "am I on track to retire at 60", "what does my savings rate get me", or "can I retire early". General financial education, not personalized investment advice. Do NOT use for choosing funds or setting an asset allocation - use investment-basics instead; do NOT use for a full financial plan with budgeting and insurance - use financial-planner instead.
---

# Retirement Projection

Retirement planning is not about picking a magic number - it is about the relationship between savings rate, time, and withdrawal needs. Small differences in savings rate now create enormous differences in outcomes later, and the most common projection errors are the flattering ones: nominal returns instead of real, base withdrawal rates for 40-year retirements, and forgetting healthcare before government coverage kicks in.

This is general financial education, not personalized investment, tax, or legal advice. Projections rest on uncertain assumptions about returns, inflation, longevity, and taxes.

## Operating procedure

Order matters: spending comes first because it drives the nest-egg target, and offsets must be subtracted before the withdrawal-rate division - applying them afterward double-counts.

### Step 1: Gather inputs

Collect, with defaults; label anything estimated as a guess:

- Current age and intended retirement age (if unknown, solve for it - that is what the calculator does).
- Current invested assets and annual savings across all accounts.
- Expected annual retirement spending in today's dollars. Default estimate: 70-80% of current pre-retirement income, reflecting lower work costs and ended savings contributions. Adjust up for planned travel and for healthcare before Medicare (or local) eligibility - the most significantly underestimated line item; adjust down for paid-off housing.
- Expected pension/Social Security income (use the official government estimator, not a guess).
- Return assumption. Use a REAL (after-inflation) return of 4-5% for a diversified portfolio; using 8-10% nominal against today's-dollar spending is the classic error that overstates readiness by a decade.

### Step 2: Set the withdrawal rate

The 4% rule: a portfolio historically sustains annual withdrawals of 4% of its starting value for at least 30 years with high probability. Divide spending by the rate to get the target nest egg. For retirements of 35-40 years - anyone retiring before ~60 - use 3.5% or 3%: early retirees face more years of sequence-of-returns risk (see below) and should hold the conservative end.

### Step 3: Subtract offsets before dividing

Target nest egg = (annual spending − annual pension/Social Security income) / withdrawal rate. Note the offset usually starts years after retirement begins; the gap years must be funded at full spending. Delaying Social Security claiming from 62 to 70 increases the monthly benefit by approximately 76%, which is often the cheapest longevity insurance available for those who can bridge the gap.

### Step 4: Run the projection

Use the calculator below. For intuition, the savings-rate table (standard return assumptions, starting from zero):

- Save 10% of income → roughly 40 years to financial independence.
- Save 25% → roughly 32 years.
- Save 50% → roughly 17 years.

Savings rate is the lever that matters most because it works both sides: it grows the portfolio faster AND shrinks the spending it must replace.

### Step 5: Stress-test for sequence-of-returns risk

The average return is not the whole story: two retirees with identical average returns can have opposite outcomes depending on WHEN the bad years land. A 30% market drop in the first three years of withdrawals, while selling shares to eat, can permanently cripple a portfolio that would have survived the same drop in year 20 - withdrawals lock in the losses. Mitigations to present: hold the withdrawal rate at the conservative end (3-3.5%) for early retirement, keep 1-2 years of spending in cash/short-term bonds as a buffer, and plan spending flexibility (skip the inflation raise or cut 10% after a bad year). Re-run the calculator with realReturn at 3% as a pessimistic case and report both dates.

## Calculator

Self-contained Node script. Save as `retirement_projection.js`, edit the inputs, run with `node retirement_projection.js`. No dependencies. All figures are real (inflation-adjusted) dollars.

```javascript
// Retirement projection calculator. Edit the inputs, then: node retirement_projection.js
const inputs = {
  currentAge: 35,
  currentPortfolio: 120000,   // invested assets today
  annualSavings: 24000,       // contributed per year, all accounts
  annualSpendInRetirement: 60000, // today's dollars, after pension/Social Security offset
  realReturn: 0.05,           // return AFTER inflation; 0.04-0.05 is a defensible planning range
  withdrawalRate: 0.04,       // 0.03-0.04; use the lower end for retirements over 30 years
}

function project(i) {
  const targetNestEgg = i.annualSpendInRetirement / i.withdrawalRate
  let balance = i.currentPortfolio
  let years = 0
  const rows = []
  while (balance < targetNestEgg && years < 70) {
    balance = balance * (1 + i.realReturn) + i.annualSavings
    years += 1
    if (years % 5 === 0 || balance >= targetNestEgg) {
      rows.push({ age: i.currentAge + years, balance: Math.round(balance) })
    }
  }
  return { targetNestEgg, yearsToTarget: years, retirementAge: i.currentAge + years, rows }
}

const r = project(inputs)
const money = (n) => '$' + n.toLocaleString('en-US')
console.log('Target nest egg:      ', money(r.targetNestEgg))
console.log('Years to target:      ', r.yearsToTarget)
console.log('Projected ready at age', r.retirementAge)
console.log('--- milestones (real dollars) ---')
for (const row of r.rows) console.log('age ' + row.age + ':  ' + money(row.balance))
```

### Worked example output

With the inputs above the script prints:

```
Target nest egg:       $1,500,000
Years to target:       25
Projected ready at age 60
--- milestones (real dollars) ---
age 40:  $285,769
age 45:  $497,337
age 50:  $767,357
age 55:  $1,111,979
age 60:  $1,551,813
```

Read it: $60,000 of spending at a 4% withdrawal rate requires $1,500,000. Saving $24,000/year from a $120,000 base at 5% real reaches it at age 60. Note the compounding shape - the last $400,000 arrives in the final five years. Pessimistic re-run at 3% real: ready at 65 instead of 60; report both ages so the user plans against the range, not the point estimate.

## Deliverable

Produce a projection summary containing: the spending estimate with its basis and healthcare note, the chosen withdrawal rate with justification tied to retirement length, the offset math shown explicitly, the target nest egg, the projected readiness age under both base (4-5% real) and pessimistic (3% real) returns, milestone balances every 5 years, and the sequence-of-returns mitigations.

## Do NOT

- Do not use nominal returns against today's-dollar spending - the projection must be entirely real or entirely nominal.
- Do not apply 4% to a 40-year early retirement; use 3-3.5% and say why.
- Do not subtract Social Security after dividing by the withdrawal rate, and do not ignore the gap years before benefits start.
- Do not omit pre-Medicare healthcare from the spending estimate.
- Do not present one readiness age as certainty; always show the base and pessimistic cases together.

## Quality bar

- Offset subtracted before the withdrawal-rate division, with the arithmetic shown.
- Return assumption explicitly labeled real vs. nominal and inside the 3-5% real band.
- Withdrawal rate matched to retirement length (4% for ~30 years, 3-3.5% beyond).
- Two scenarios reported, and sequence-of-returns risk explained with at least two mitigations.
- Every input either sourced or labeled a guess.

## Escalation

Projections here use deterministic rules of thumb; a fee-only fiduciary financial planner can run Monte Carlo simulations, model individual tax treatment across account types, and handle pensions with complex options - worth the fee within ~10 years of a target retirement date. For choosing the portfolio that earns the assumed return, use investment-basics; for the broader plan around the projection, use financial-planner; for withdrawal-phase tax moves, see tax-optimization.
