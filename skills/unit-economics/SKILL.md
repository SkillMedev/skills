---
name: Unit Economics
description: Compute CAC, contribution margin, LTV, LTV:CAC, and payback with honest definitions, a runnable calculator, and a verdict on whether each customer makes money. Use when someone asks "what's my CAC", "is my LTV:CAC healthy", "how long is my payback period", "can I afford to spend more on ads", or before scaling acquisition spend. Do NOT use for gym-specific front-end economics and Client-Financed Acquisition - use gym-money-model instead; for multi-year revenue projections and MRR bridges use revenue-modeling; for cohort-level retention and growth diagnosis use growth-accounting.
---

# Unit Economics

Unit economics answer one question: does each customer make money, and how fast? Get the definitions wrong and you will scale a business that loses money faster the more it grows - the most expensive spreadsheet error a founder can make, because the flattering version of CAC and LTV looks fine right up until the cash runs out. This skill enforces honest math.

## Operating procedure

Work in order: definitions first, blended numbers second, segmented numbers third, verdict last. A verdict on blended numbers alone is not a verdict.

### Step 1: Gather inputs

Collect for a single recent period (a month or quarter). If a number is a guess, label it a guess and refine later.

1. Fully-loaded sales and marketing spend for the period: salaries, tools, ad spend, commissions - not ad spend alone. The most common mistake is counting ad spend only and ignoring people.
2. New customers acquired in the same period. Same-period matching keeps CAC honest; for long sales cycles, lag spend by the average cycle length and note the adjustment.
3. ARPA: average monthly revenue per account.
4. Variable cost to serve one customer per month: hosting, payment fees, support, third-party APIs. Not rent, not R&D.
5. Monthly customer churn rate. If unknown, estimate from cancellations / customers-at-start and label it a guess.
6. The available splits: channel, segment or size, and cohort.

### Step 2: Compute the four core metrics

- CAC = fully-loaded S&M spend / new customers in the same period.
- Contribution margin = revenue per customer minus the variable cost to serve them. This is the real margin that funds CAC and overhead - not gross revenue.
- LTV = (ARPA x gross margin %) / monthly churn rate - equivalently, monthly contribution x average lifetime in months. Use gross margin, never raw revenue. Cap the lifetime horizon at 3 years (36 months) for credibility; infinite-life LTV math flatters early-stage churn, where a 1% churn estimate implies a fantasy 100-month customer.
- Payback period = CAC / monthly contribution margin per customer. The number of months to earn back acquisition cost - the metric investors trust most because it is hard to fake.

### Step 3: Read against the benchmarks

- LTV:CAC - 3:1 or better is healthy. Below 1:1 the business loses money on every customer; between 1:1 and 3:1 it is fragile; above 5:1 it is likely underinvesting in growth and could acquire faster.
- Payback - under 12 months for SMB, under 18-24 months for enterprise. A 30-month payback is a financing problem even if LTV:CAC looks fine, because someone has to fund the gap.
- Contribution margin - 70%+ for software; structurally lower for usage-heavy or services-heavy models. Below 50% for a pure software product, investigate cost to serve before anything else.

### Step 4: Segment before concluding

Blended unit economics hide the truth: a great SMB segment can mask a brutal enterprise one, or vice versa. Recompute by:

- Acquisition channel - paid vs organic CAC differ wildly, and blending them makes paid look safer than it is.
- Customer segment or size.
- Cohort - newer cohorts may churn differently, and the blended churn rate lags reality.

The verdict applies per segment. "Scale spend" can be simultaneously right for organic SMB and wrong for paid enterprise.

### Step 5: Rank the levers

To improve LTV:CAC, in order of usual leverage:

1. Reduce churn - the single biggest LTV lever; it compounds, and it also signals product truth.
2. Expand revenue - upsell and net revenue retention above 100%.
3. Raise prices - direct margin, and the most under-tested lever (pair with pricing-strategy).
4. Lower CAC - shift to compounding channels, improve conversion.
5. Improve gross margin - renegotiate infrastructure, automate support.

Recommend the top three for this business ranked by expected impact on payback period, each with the specific metric it moves.

## Calculator

Self-contained Node script. Save as `unit_economics.js` and run with `node unit_economics.js`. Edit the inputs block; rerun per segment by swapping the inputs. No dependencies.

```javascript
// Unit Economics calculator. Edit the inputs, then: node unit_economics.js
const inputs = {
  salesMarketingSpend: 60000, // monthly, fully loaded: salaries, tools, ad spend, commissions
  newCustomers: 40,           // new customers acquired in the same month
  arpa: 250,                  // average monthly revenue per account
  variableCostPerCustomer: 45,// monthly cost to serve: hosting, payment fees, support, APIs
  monthlyChurnRate: 0.025,    // customer churn per month (0-1)
  ltvCapMonths: 36,           // credibility cap on customer lifetime
}

function model(i) {
  const cac = i.salesMarketingSpend / i.newCustomers
  const contribution = i.arpa - i.variableCostPerCustomer
  const contributionMarginPct = contribution / i.arpa
  const lifetimeMonths = Math.min(1 / i.monthlyChurnRate, i.ltvCapMonths)
  const ltv = contribution * lifetimeMonths
  const ltvToCac = ltv / cac
  const paybackMonths = cac / contribution
  let verdict = 'HEALTHY - scale with confidence'
  if (ltvToCac < 1) verdict = 'LOSING MONEY PER CUSTOMER - stop scaling'
  else if (ltvToCac < 3) verdict = 'FRAGILE - fix a lever before scaling'
  else if (paybackMonths > 12) verdict = 'CASH-CONSTRAINED - LTV fine, payback too slow'
  else if (ltvToCac > 5) verdict = 'HEALTHY - possibly underinvesting in growth'
  return { cac, contribution, contributionMarginPct, lifetimeMonths, ltv, ltvToCac, paybackMonths, verdict }
}

const r = model(inputs)
const money = (n) => '$' + n.toFixed(2)
console.log('CAC (fully loaded):            ', money(r.cac))
console.log('Contribution/customer/month:   ', money(r.contribution), `(${(r.contributionMarginPct * 100).toFixed(0)}% margin)`)
console.log('Lifetime used (months, capped):', r.lifetimeMonths.toFixed(1))
console.log('LTV (contribution, capped):    ', money(r.ltv))
console.log('LTV:CAC:                       ', r.ltvToCac.toFixed(2) + ':1')
console.log('Payback (months):              ', r.paybackMonths.toFixed(1))
console.log('Verdict:                       ', r.verdict)
```

### Worked example output

With the inputs above the script prints:

```
CAC (fully loaded):             $1500.00
Contribution/customer/month:    $205.00 (82% margin)
Lifetime used (months, capped): 36.0
LTV (contribution, capped):     $7380.00
LTV:CAC:                        4.92:1
Payback (months):               7.3
Verdict:                        HEALTHY - scale with confidence
```

Read it: $60k of fully-loaded spend bought 40 customers, so each cost $1,500. Each contributes $205/month after cost to serve (82% margin - healthy for software). Churn of 2.5%/month implies a 40-month lifetime, capped at 36 for credibility, giving a $7,380 LTV. The ratio is 4.92:1 and spend comes back in 7.3 months - inside the 12-month SMB bar. This business can scale acquisition; at nearly 5:1 it may even be underspending.

## Deliverable

Produce a unit economics model containing: CAC, contribution margin, LTV, LTV:CAC, and payback - computed blended and split by at least channel and segment - a verdict per segment against the benchmarks, and the top three improvement levers ranked by expected impact on payback period. Include the filled calculator inputs so the model reruns as numbers change.

## Do NOT

- Do not use revenue instead of gross margin in LTV; it inflates LTV by the entire cost of serving the customer.
- Do not exclude fully-loaded headcount from CAC; ad-spend-only CAC understates cost by 2x or more for any team with salespeople.
- Do not average across segments that should be separated; blended numbers are where bad segments hide.
- Do not run uncapped LTV math on early-stage churn data; cap at 36 months.
- Do not ignore time value - a 30-month payback is a financing problem even when LTV:CAC clears 3:1.
- Do not treat a healthy blended ratio as permission to scale every channel; the verdict is per segment.

## Quality bar

- Every input is sourced or explicitly labeled a guess.
- CAC includes salaries, tools, and commissions; LTV uses contribution, never revenue; lifetime is capped at 36 months.
- Numbers are computed blended and for at least two splits (channel and segment).
- The verdict cites both LTV:CAC and payback against the stated benchmarks, per segment.
- The three recommended levers each name the metric they move and the expected direction on payback.

## Escalation

Not financial or investment advice; for fundraising-grade models have a finance professional review. Route gym economics to gym-money-model, multi-year projections and MRR bridges to revenue-modeling, cohort retention diagnosis to growth-accounting, and price changes surfaced by lever 3 to pricing-strategy.
