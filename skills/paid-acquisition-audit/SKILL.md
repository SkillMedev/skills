---
name: Paid Acquisition Audit
description: Audits paid acquisition channels for wasted spend and untapped scaling headroom - computing break-even ROAS from gross margin, flagging creative fatigue and saturation, and producing a ranked action list with a dollar impact per item. Use when someone asks "where is my ad budget being wasted", "should I scale this campaign", "why did ROAS drop", or is preparing a budget review or planning to increase paid spend. Do NOT use for designing new creative experiments - use ad-creative-testing instead; for assigning cross-channel conversion credit, use marketing-attribution.
---

# Paid Acquisition Audit

A paid acquisition audit surfaces where money is bleeding and where the ceiling on profitable spend has not been reached yet. The costly mistake it prevents is acting on blended numbers: a "healthy" account average routinely hides campaigns burning cash below break-even next to starved winners that could absorb double the budget. The output is a ranked action list with a dollar impact per item - never a general report.

## Operating procedure

Work the steps in order. Break-even math (Step 2) must precede waste hunting (Step 3), because "waste" is undefined until the break-even line is drawn for this specific business.

### Step 1: Gather inputs

Collect before forming any opinion. Label any estimate a guess and revisit it in the findings.

1. Gross margin on the product sold through ads. This sets break-even ROAS. If unknown, default to 60% for DTC ecommerce or 80% for SaaS and label it a guess.
2. A 90-day export of spend, impressions, clicks, conversions, and revenue, broken down by channel, campaign, ad set, and ad. Never audit fewer than 30 days - short windows hide weekly patterns.
3. Search term reports for Google; placement and frequency reports for Meta and display.
4. Current daily budgets and impression-share-lost-to-budget per campaign.
5. Organic rank for the brand terms being bid on, and whether competitors bid on the brand.
6. Target CAC or payback period, if the business has one.

### Step 2: Draw the break-even line

Break-even ROAS = 1 / gross margin. A 60% gross-margin product breaks even at 1.67; anything below that is a cash drain, not "top of funnel." Set target ROAS at least 20% above break-even (2.0 for the 60%-margin case). Then:

- Flag every campaign below break-even for more than 14 consecutive days. Shorter dips can be auction noise; 14+ days is structural.
- Flag every campaign above target ROAS but spending less than 20% of its daily budget - it is under-delivering and needs bid or audience expansion, not applause.

Compute blended CAC and ROAS at the channel level first, then drill to campaign, ad set, ad. Blended-only analysis is the audit failing at its one job.

### Step 3: Hunt waste in spend order

Audit line items from highest spend down - a 10% fix on the biggest campaign beats a 50% fix on the smallest. Check these five patterns:

1. Branded keywords being bid on where organic rank is position 1 and no competitor bids on the brand - cut unless an incrementality test proves value.
2. Broad-match keywords with negative ROI in the search term report - add negatives immediately.
3. Placements or audience segments with CTR below 0.3% on display or Meta - pause them.
4. Ad sets with no clear winner after 500 impressions per ad - kill the losers; they are taxing the auction.
5. Dayparting gaps where CPA spikes in specific hours or days but budget does not throttle.

### Step 4: Check creative fatigue and saturation

Before recommending any budget increase, verify the winner is not already dying:

- Creative fatigue: CTR decay greater than 20% versus the creative's own first-two-weeks average, or Meta frequency above 3.5. A fatigued creative fails harder at higher spend - rotate creative first, then scale. Route new concept development to ad-creative-testing.
- Channel saturation: Google impression share above 70% means the auction is nearly topped out; incremental spend buys expensive marginal impressions.

### Step 5: Score scaling opportunities

A campaign is scalable only if all three hold: ROAS at least 20% above break-even, impression share lost to budget above 15%, and no saturation signal from Step 4 (frequency below 3.5 on Meta, impression share below 70% on Google). Rank qualifying campaigns by expected incremental revenue at 2x current spend using current ROAS as the proxy, then haircut the projection 15-25% for diminishing returns before presenting it.

### Step 6: Produce the ranked action list

Deliver findings in three buckets: (1) immediate cuts - negative-ROI spend to pause today; (2) optimizations - bid, budget, or targeting changes within 7 days; (3) scaling bets - budget increases with the expected outcome stated. Every item gets an estimated monthly dollar impact. Items without a quantified impact are noise - drop them.

## Audit checklist (ordered by spend impact)

Copy and work top to bottom; each line is a pass/fail with a dollar consequence.

```
PAID ACQUISITION AUDIT - [FILL: account] - [FILL: 90-day window]
Gross margin: [FILL]%  ->  Break-even ROAS: [FILL]  Target ROAS: [FILL]

[ ] 1. Any campaign below break-even ROAS 14+ consecutive days?   Cut/fix: $[FILL]/mo
[ ] 2. Branded spend where organic is #1 and no competitor bids?  Cut: $[FILL]/mo
[ ] 3. Broad-match terms with negative ROI in search term report? Negatives: $[FILL]/mo
[ ] 4. Placements/audiences with CTR < 0.3%?                      Pause: $[FILL]/mo
[ ] 5. Ad sets with no winner after 500 impressions/ad?           Kill losers: $[FILL]/mo
[ ] 6. CPA spikes by hour/day with no budget throttle?            Daypart: $[FILL]/mo
[ ] 7. Winners fatigued (CTR decay > 20% or frequency > 3.5)?     Rotate before scaling
[ ] 8. Campaigns passing all three scaling gates?                 Scale: +$[FILL]/mo revenue
```

Worked line: a Meta prospecting campaign spending $9,000/mo at 1.4 ROAS against a 1.67 break-even, below the line for 22 straight days, loses roughly $0.27 of contribution per revenue dollar - pausing or restructuring it is worth about $1,500/mo, and it goes in bucket 1.

## Deliverable

Produce a one-page audit brief containing: the top three actions first (never methodology first), the full three-bucket action list with monthly dollar impact per item, the break-even and target ROAS used, and the data window audited. Run the full audit quarterly; run a light version monthly covering only campaigns that changed materially.

## Do NOT

- Do not audit fewer than 30 days of data - weekly seasonality will masquerade as trend.
- Do not apply a universal "good ROAS" number - break-even is a function of gross margin, and a 2.5 ROAS can be unprofitable at thin margins.
- Do not recommend scaling a campaign whose creative is fatiguing; higher budget accelerates the decay.
- Do not cut branded spend reflexively when competitors bid on the brand - that spend is defensive, and the test is incrementality, not organic rank alone.
- Do not report blended averages as findings; the action lives at campaign and ad-set level.
- Do not bury the lead in methodology - the action list goes first.

## Quality bar

Before the brief ships, verify: every flagged item cites the specific threshold it violated; every action has a monthly dollar impact; the scaling bets each pass all three gates (ROAS margin, budget-limited impression share, no saturation); and the total of bucket 1 cuts is stated as a single reclaimable monthly figure. If budget pacing across the month is the real problem rather than allocation, route to budget-pacing.
