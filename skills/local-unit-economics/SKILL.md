---
name: local-unit-economics
description: Models the money engine of a local service business - cost per lead by channel, booking rate, show rate, close rate, average ticket, and repeat rate - computing CAC, first-90-day cash, LTV to CAC, and a self-funding verdict, with a runnable calculator. Use when an owner asks "what does a new customer actually cost me", "can I afford more ad spend", "is my marketing paying for itself", or "which lead channel should get the next dollar". Do NOT use for gyms - use gym-money-model instead. Do NOT use for SaaS or subscription software - use unit-economics instead.
---

# Local Unit Economics

Install this skill first. Every other local-service growth skill -
google-business-profile-optimizer, review-engine, local-seo-playbook,
quote-follow-up-sequences, no-show-reduction, local-referral-engine - spends
money or staff time, and this model is what says whether that spend pays for
itself. The costly mistake it prevents is judging marketing by cost per lead
instead of cost per acquired customer: a cheap lead that never books, shows, or
closes is the most expensive lead there is.

Work the example throughout: Summit Plumbing, a 4-tech residential plumbing
company in a mid-size metro, average job $450, doing $75,000 a month - about
167 jobs.

## Operating procedure

Follow the steps in order. CAC has to be computed before any verdict, and the
funnel rates have to be gathered before CAC, because the whole model hinges on
how many leads it takes to produce one paying customer.

### Step 1: Define the unit and gather the inputs

The unit is one new customer - not one lead and not one job. Collect these,
per lead channel (Local Services Ads, paid search, GBP/organic, referral). If a
number is a guess, label it a guess and refine next month.

1. Cost per lead, fully loaded. Channel spend plus attributable answering or
   CSR cost, divided by leads. Not ad spend alone.
2. Booking rate. Share of leads that become booked appointments. Phone answer
   speed dominates this number.
3. Show rate. Share of booked appointments that actually happen. This is
   1 minus the no-show rate.
4. Close rate. Share of completed visits that turn into a paid job. Blend
   on-the-spot service work and written estimates.
5. Average ticket. Revenue per job.
6. Gross margin after direct labor and materials. Residential trades commonly
   run 0.50 to 0.60.
7. Repeat rate in the first 90 days. Extra jobs per new customer inside 90
   days, as a fraction (0.15 means 15 percent come back once within 90 days).
8. Jobs per customer over roughly three years, including the first.

Summit's numbers, used in the calculator below: $50 loaded cost per Local
Services Ads lead, 75 percent booking, 86 percent show (a 14 percent no-show
rate), 65 percent blended close, $450 ticket, 55 percent margin, 0.15 repeat
rate, 2.2 jobs over three years.

### Step 2: Compute cost per acquired customer

Lead-to-customer rate equals booking rate times show rate times close rate.
CAC equals cost per lead divided by that rate. Divide - never use cost per
lead as CAC - because the spend wasted on leads that never became customers
lands on the ones who did.

### Step 3: Apply the 2x cash rule to the first job

The first job collects the average ticket at completion. The 2x cash rule:
the first job should collect at least twice CAC. At 1x the business treads
water; at 2x, every two customers fund the acquisition of a third, and growth
compounds without outside cash. This rule is defined here once; the other
skills in this pack cite it when justifying spend.

### Step 4: Compute first-90-day cash and lifetime value

- First-90-day cash per customer equals average ticket times (1 plus the
  90-day repeat rate).
- LTV equals average ticket times jobs per customer over three years times
  gross margin. This is contribution, not revenue.

### Step 5: Read the verdict against three targets

- First job passes the 2x cash rule.
- LTV to CAC at or above 3 to 1. Above 5 to 1 usually means underspending on
  growth.
- Payback inside the first job: first-job cash at or above CAC.

All three pass: scale that channel until crew capacity, not cash, is the
constraint. Any fail: go to Step 6.

### Step 6: Diagnose the constrained lever, cheapest fix first

1. Booking rate under 70 percent. Fix phone answering before anything else -
   answer live or call back within five minutes. Costs nothing in ad spend.
2. No-show rate above the 10 percent red line (show rate under 90 percent).
   Go to no-show-reduction. Summit books roughly 300 appointments a month, so
   its 14 percent no-show rate is about 42 missed visits; cutting it to 7
   percent recovers about 21 visits, roughly 14 jobs, roughly $6,100 a month
   in gross revenue.
3. Written-estimate close rate under 50 percent. Go to
   quote-follow-up-sequences - the leak is usually follow-up, not price.
4. Cost per lead high but funnel healthy. Build free prominence before buying
   more leads: google-business-profile-optimizer, review-engine, then
   local-seo-playbook.
5. Average ticket low. Quote good/better/best options instead of one price.
   Never fix a ticket problem by discounting.

### Step 7: Produce the channel economics sheet

Fill the template below per channel and re-run monthly. Compare channels on
CAC and payback, not cost per lead.

## Inputs to elicit

Ask for: monthly spend and lead count per channel, booked appointments, no-show
count, jobs won from those visits, average ticket, and margin. Defaults if
unknown: booking 70 percent, show 88 percent, close 55 percent, margin 0.55 -
all labeled as guesses until replaced with real counts.

## Calculator

Self-contained Node script. Save as `local_econ.js`, edit the inputs block,
run with `node local_econ.js`. No dependencies.

```javascript
// Local service unit economics. Edit inputs, then: node local_econ.js
const inputs = {
  costPerLead: 50,        // fully loaded, for the channel being judged
  bookingRate: 0.75,      // leads that become booked appointments
  showRate: 0.86,         // booked appointments that happen (1 - no-show rate)
  closeRate: 0.65,        // completed visits that become a paid job
  avgTicket: 450,         // average revenue per job
  grossMargin: 0.55,      // after direct labor and materials
  repeatRate90d: 0.15,    // extra jobs per new customer within 90 days
  jobsPerCustomer3y: 2.2, // jobs over ~3 years, including the first
}

function model(i) {
  const leadToCustomer = i.bookingRate * i.showRate * i.closeRate
  const cac = i.costPerLead / leadToCustomer
  const firstJobCash = i.avgTicket
  const passesTwoXRule = firstJobCash >= 2 * cac
  const cash90d = i.avgTicket * (1 + i.repeatRate90d)
  const ltv = i.avgTicket * i.jobsPerCustomer3y * i.grossMargin
  const ltvToCac = ltv / cac
  const paybackFirstJob = firstJobCash >= cac
  const selfFunding = passesTwoXRule && ltvToCac >= 3 && paybackFirstJob
  return { leadToCustomer, cac, firstJobCash, passesTwoXRule,
           cash90d, ltv, ltvToCac, paybackFirstJob, selfFunding }
}

const r = model(inputs)
const money = (n) => '$' + n.toFixed(2)
console.log('Lead-to-customer rate:           ', (r.leadToCustomer * 100).toFixed(1) + '%')
console.log('CAC per customer:                ', money(r.cac))
console.log('First-job cash:                  ', money(r.firstJobCash))
console.log('Passes 2x cash rule:             ', r.passesTwoXRule ? 'yes' : 'no')
console.log('First-90-day cash per customer:  ', money(r.cash90d))
console.log('LTV per customer (contribution): ', money(r.ltv))
console.log('LTV:CAC:                         ', r.ltvToCac.toFixed(2) + ':1')
console.log('Payback inside first job:        ', r.paybackFirstJob ? 'yes' : 'no')
console.log('Self-funding verdict:            ', r.selfFunding ? 'SELF-FUNDING' : 'NOT YET')
```

### Worked example output

With Summit Plumbing's inputs above the script prints:

```
Lead-to-customer rate:            41.9%
CAC per customer:                 $119.26
First-job cash:                   $450.00
Passes 2x cash rule:              yes
First-90-day cash per customer:   $517.50
LTV per customer (contribution):  $544.50
LTV:CAC:                          4.57:1
Payback inside first job:         yes
Self-funding verdict:             SELF-FUNDING
```

Read it: it takes about 2.4 leads to make one customer, so the $50 lead
becomes a $119 customer. The first job collects $450 against that $119 -
comfortably past the 2x cash rule - and lifetime contribution of $544.50
puts LTV to CAC at 4.57 to 1. Summit can scale this channel. But note the
diagnosis in Step 6: the 86 percent show rate sits below the 90 percent line,
and cutting no-shows to 7 percent with no-show-reduction would drop CAC from
$119.26 to about $110 and recover roughly $6,100 a month in gross revenue.
Self-funding is not the same as leak-free.

## Template: channel-economics-sheet

```
LOCAL SERVICE UNIT ECONOMICS. [FILL: business]. [FILL: channel]. [FILL: month]

INPUTS
  Cost per lead (loaded):       $[FILL]
  Booking rate:                 [FILL]%
  Show rate:                    [FILL]%   (no-show [FILL]%)
  Close rate:                   [FILL]%
  Average ticket:               $[FILL]
  Gross margin:                 [FILL]%
  90-day repeat rate:           [FILL]
  Jobs per customer (3y):       [FILL]

OUTPUTS (from calculator)
  CAC per customer:             $____
  Passes 2x cash rule:          yes / no
  First-90-day cash:            $____
  LTV:CAC:                      ____:1
  Payback inside first job:     yes / no
  Verdict:                      SELF-FUNDING / NOT YET

CONSTRAINED LEVER (if not self-funding, or any funnel rate below floor)
  Bottleneck:  [FILL: booking / show / close / cost per lead / ticket]
  Action:      [FILL: one move]
  Owner:       [FILL]
```

## Deliverable

A filled channel-economics-sheet per lead channel, the calculator output
pasted in, a one-line verdict per channel, and - if any target fails - exactly
one named bottleneck with one action and the skill that fixes it.

## Do NOT

- Do not treat cost per lead as CAC. The wasted spend on leads that never
  close belongs in the number.
- Do not use revenue for LTV; use contribution after gross margin, or every
  channel looks affordable.
- Do not blend all channels into one average - a great referral channel can
  hide a bleeding paid channel.
- Do not scale spend on LTV alone while first-job cash misses the 2x rule; a
  business with strong LTV and weak near-term cash still runs out of money.
- Do not fix a funnel-rate problem with more lead spend. More leads through a
  leaky funnel just raises the water bill.

## Quality bar

- Every input sourced from real counts or explicitly labeled a guess.
- CAC divides by the full lead-to-customer rate, not by booking rate alone.
- The verdict cites all three targets, not one.
- If not self-funding, the sheet names exactly one lever, one action, and the
  pack skill that executes it, and the model is re-run after the change.

## Escalation and routing

This is operating math, not accounting or tax advice - margin and cash
questions with tax consequences go to the business's accountant. Route gym
economics to gym-money-model and software or subscription economics to
unit-economics. When the bottleneck is identified, hand off: booking problems
to phone process, show problems to no-show-reduction, estimate-close problems
to quote-follow-up-sequences, cost-per-lead problems to
google-business-profile-optimizer, review-engine, and local-seo-playbook, and
referral expansion to local-referral-engine.
