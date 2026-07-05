---
name: gym-money-model
description: Model gym unit economics — Client-Financed Acquisition, 30-day cash, CAC, LTV, LTV:CAC, and payback — and return a verdict on whether growth self-funds. Use when a gym owner asks "what's my CAC", "how much can I spend to get a member", "what should my challenge cost to break even", "is my gym profitable to scale", "what's my LTV:CAC", or "why does growth stall when ads work". Do NOT use for generic SaaS or non-gym business unit economics — use unit-economics instead.
---

# Gym Money Model

Build this first. Every other gym growth skill spends money or sets a price, and
this model tells you whether that spend pays for itself. The goal is
Client-Financed Acquisition: the front-end collects enough cash in the first 30
days that each new member funds the acquisition of the next one, so growth is not
capped by the bank balance.

Work the example gym throughout: one location, semi-private coaching plus
memberships, a 6-week transformation challenge as the front-end.

## Operating procedure

Follow these steps in order. Do not skip to LTV before the front-end math is
clean, because a gym dies from cash timing long before it dies from lifetime
value. If the owner is setting the challenge price, pair with
gym-transformation-challenge; if setting an ad budget, pair with
gym-meta-ads-funnel.

### Step 1: Define the unit and gather six inputs

The unit is one new member. Collect these from the owner. If a number is a guess,
label it a guess and move on; refine later.

1. Cost per challenge sale. Fully loaded cost to fill one challenge spot: ad spend
   plus any sales labor and setter cost, divided by challenge sales. Not just ad
   spend.
2. Challenge price. What one challenger pays up front for the 6-week front-end.
3. Conversion to membership. The share of challengers who become paying members at
   the end of the challenge.
4. Membership price. Monthly recurring dues for the core membership.
5. Average tenure. How many months a member stays, on average. If unknown,
   estimate as 1 divided by monthly churn rate (5 percent churn means 20 months).
6. Gross margin on delivery. Revenue minus the variable cost to deliver (coaching
   hours, space, consumables), as a fraction. A semi-private gym commonly runs
   0.6 to 0.75.

### Step 2: Compute the front-end cash position

The front-end is the challenge. Two numbers decide if it self-funds.

- 30-day cash per challenge sale equals the challenge price, since the challenger
  pays up front.
- Front-end surplus equals challenge price minus cost per challenge sale.

Apply the 2x cash rule: the challenge should collect at least twice its own
acquisition cost within 30 days. If 30-day cash is below 2 times cost per
challenge sale, the front-end is not strong enough to fund growth and you fix that
before scaling spend. See references/the-2x-cash-rule below.

### Step 3: Compute member economics

- CAC per member equals cost per challenge sale divided by conversion to
  membership. You pay to acquire challengers, and only some become members, so the
  cost concentrates on the ones who convert.
- LTV per member equals membership price times average tenure times gross margin.
  This is contribution, not revenue, because margin is already applied.
- LTV:CAC equals LTV divided by CAC per member.
- Payback period is how many months of member cash it takes to cover CAC. When the
  front-end cash collected per member already exceeds CAC, payback is under one
  month and acquisition is self-funding.

### Step 4: Read the verdict against targets

- LTV:CAC at or above 3:1. Below that, the model is fragile. Above 5:1 usually
  means you are underspending on growth and could acquire faster.
- Payback under 30 days where the front-end allows it. That is the signature of
  Client-Financed Acquisition.
- Front-end at or above the 2x cash rule.

If all three pass, the owner can scale spend with confidence. If any fails, go to
Step 5.

### Step 5: Diagnose the constrained lever

When growth is capital-constrained, one lever is usually the bottleneck. Use this
order, because the cheapest fixes come first.

1. Conversion to membership is low (under 40 percent). Fix the consult and
   onboarding before spending more on ads. Send the owner to closer-sales-script
   and retention-and-churn-killer. This is the highest-impact fix because it
   improves both CAC and LTV at once.
2. Front-end fails the 2x rule. Raise challenge price or lower cost per challenge
   sale. Send to gym-pricing-and-guarantees and gym-meta-ads-funnel.
3. LTV is low. Raise membership price, raise margin, or extend tenure. Tenure is a
   retention problem; send to retention-and-churn-killer.
4. CAC is high but everything else is healthy. The model can afford it; scale
   spend and watch payback.

See references/diagnosing-economics for the full decision tree.

### Step 6: Produce the unit economics sheet

Fill the template below with the owner's numbers and the computed outputs. This is
the artifact they keep and revisit monthly. Run the calculator to populate the
outputs.

## Quality bar

A finished model is A+ only when all hold:

- Every input is sourced or explicitly labeled a guess — no silent placeholders.
- CAC divides by conversion (it includes spend wasted on non-converters); LTV uses
  contribution margin, never revenue.
- The verdict cites all three targets (2x cash rule, LTV:CAC, payback), not one.
- If not self-funding, the sheet names exactly one constrained lever and one
  action, and you re-run the model after the proposed change.

## Calculator

Self-contained Node script. Save as `money_model.js` and run with
`node money_model.js`. Edit the inputs block for the gym in question. No
dependencies.

```javascript
// Gym Money Model calculator. Edit the inputs, then: node money_model.js
const inputs = {
  costPerChallengeSale: 200, // fully loaded cost to fill one challenge spot
  challengePrice: 499,       // up-front price of the 6-week challenge
  conversionToMembership: 0.5, // share of challengers who become members
  membershipPrice: 159,      // monthly recurring dues
  avgTenureMonths: 14,       // average months a member stays
  grossMargin: 0.7,          // delivery contribution margin (0-1)
}

function model(i) {
  const frontEndCash30d = i.challengePrice
  const frontEndSurplus = i.challengePrice - i.costPerChallengeSale
  const passesTwoXRule = frontEndCash30d >= 2 * i.costPerChallengeSale
  const cacPerMember = i.costPerChallengeSale / i.conversionToMembership
  const ltv = i.membershipPrice * i.avgTenureMonths * i.grossMargin
  const ltvToCac = ltv / cacPerMember
  // 30-day cash earned per member: you sold 1/conversion challenge spots to
  // produce one member, each collecting the challenge price up front.
  const cash30dPerMember = i.challengePrice / i.conversionToMembership
  const paybackUnderThirtyDays = cash30dPerMember >= cacPerMember
  const paybackMonths = paybackUnderThirtyDays
    ? 0
    : (cacPerMember - cash30dPerMember) / (i.membershipPrice * i.grossMargin)
  const selfFunding = passesTwoXRule && ltvToCac >= 3 && paybackUnderThirtyDays
  return {
    frontEndCash30d, frontEndSurplus, passesTwoXRule,
    cacPerMember, ltv, ltvToCac, cash30dPerMember,
    paybackUnderThirtyDays, paybackMonths, selfFunding,
  }
}

const r = model(inputs)
const money = (n) => '$' + n.toFixed(2)
console.log('Front-end 30-day cash per sale: ', money(r.frontEndCash30d))
console.log('Front-end surplus per sale:     ', money(r.frontEndSurplus))
console.log('Passes 2x cash rule:            ', r.passesTwoXRule ? 'yes' : 'no')
console.log('CAC per member:                 ', money(r.cacPerMember))
console.log('LTV per member (contribution):  ', money(r.ltv))
console.log('LTV:CAC:                        ', r.ltvToCac.toFixed(2) + ':1')
console.log('30-day cash per member:         ', money(r.cash30dPerMember))
console.log('Payback under 30 days:          ', r.paybackUnderThirtyDays ? 'yes' : 'no')
console.log('Self-funding verdict:           ', r.selfFunding ? 'SELF-FUNDING' : 'NOT YET')
```

### Worked example output

With the inputs above the script prints:

```
Front-end 30-day cash per sale:  $499.00
Front-end surplus per sale:      $299.00
Passes 2x cash rule:             yes
CAC per member:                  $400.00
LTV per member (contribution):   $1558.20
LTV:CAC:                         3.90:1
30-day cash per member:          $998.00
Payback under 30 days:           yes
Self-funding verdict:            SELF-FUNDING
```

Read it: the challenge collects 499 against a 200 acquisition cost, clearing the
2x rule. Each member costs 400 to acquire and returns 1558 in lifetime
contribution, a 3.9:1 ratio. The front-end alone collects 998 per member in the
first 30 days, more than the 400 CAC, so acquisition pays for itself before the
first membership payment clears. This gym can scale ad spend.

## Template: unit-economics-sheet

Copy this, replace the FILL fields, and paste the calculator outputs into the
OUTPUTS block.

```
GYM UNIT ECONOMICS. [FILL: gym name]. [FILL: month/year]

INPUTS
  Cost per challenge sale (loaded):  $[FILL]
  Challenge price:                   $[FILL]
  Conversion to membership:          [FILL]%
  Membership price (monthly):        $[FILL]
  Average tenure (months):           [FILL]
  Gross margin on delivery:          [FILL]%

OUTPUTS (from calculator)
  Front-end 30-day cash per sale:    $____
  Front-end surplus per sale:        $____
  Passes 2x cash rule:               yes / no
  CAC per member:                    $____
  LTV per member (contribution):     $____
  LTV:CAC:                           ____:1
  Payback under 30 days:             yes / no
  Self-funding verdict:              SELF-FUNDING / NOT YET

CONSTRAINED LEVER (if not self-funding)
  Bottleneck:                        [FILL: conversion / front-end / LTV / CAC]
  Action this month:                 [FILL: one move]
  Owner:                             [FILL]
```

## Do NOT

- Do not use ad spend alone as CAC; load in sales labor and setter cost.
- Do not divide CAC by 1 (per challenger). Divide by conversion to membership, so
  wasted spend on non-converters lands on the members who actually convert.
- Do not use revenue for LTV; use contribution after gross margin.
- Do not bless scaling on LTV:CAC alone while 30-day cash is weak — a gym with
  great LTV and weak 30-day cash still goes broke.
- Do not raise the back end (LTV) to rescue a front-end that fails the 2x rule;
  fix price or cost per challenge sale first.

## references/money-model

Client-Financed Acquisition means the customer funds their own acquisition. The
mechanism is timing: most gyms can eventually earn back acquisition cost over a
year of dues, but they run out of cash long before that because they pay for ads
today and collect dues slowly. The front-end challenge breaks the timing trap by
collecting a lump of cash up front. If that lump covers acquisition cost, and
ideally doubles it, the gym can reinvest immediately and growth compounds without
outside capital.

The four numbers that matter, in plain terms:

- CAC. What it truly costs to land one member, including the spend wasted on
  challengers who never convert. Hiding the wasted spend makes CAC look better than
  it is and leads to overspending.
- 30-day cash. Cash actually in the account within a month of acquiring a member.
  This is the survival metric. A gym with great LTV and weak 30-day cash still goes
  broke.
- LTV. Lifetime contribution per member, after delivery cost. Revenue figures
  flatter the model; use margin.
- LTV:CAC and payback. The return on each acquisition dollar and how fast it comes
  back. Fast payback beats high LTV when cash is tight.

## references/the-2x-cash-rule

The front-end should collect at least twice its own acquisition cost within 30
days. At exactly 1x, the gym treads water: each sale returns what it cost, with
nothing left to accelerate. At 2x, every two members fund a third for free, which
is what turns spend into compounding growth. If the challenge cannot clear 2x, the
fix is almost always price (too low to anchor value) or cost per challenge sale
(ads or sales process leaking money), not LTV. Solve the front-end before touching
the back end.

## references/diagnosing-economics

Decision tree when the gym is capital-constrained:

1. Is conversion to membership below 40 percent? If yes, fix it first. It lifts CAC
   and LTV together and costs nothing in ad spend. Consult skill and onboarding
   skill.
2. Else, does the front-end clear the 2x cash rule? If no, raise challenge price or
   cut cost per challenge sale. Pricing skill and ads skill.
3. Else, is LTV:CAC below 3:1? If yes, the leak is lifetime value: raise membership
   price, raise margin, or extend tenure. Retention skill.
4. Else, all three pass and CAC is simply high. The model affords it. Scale spend
   and monitor payback monthly.

Re-run the model after every change. One lever moves the others, so re-read the
whole sheet, not just the number you changed.
