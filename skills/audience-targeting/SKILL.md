---
name: Audience Targeting
description: Builds and refines audience and segment targeting for paid campaigns - first-party audience construction, lookalike expansion, exclusion lists, platform sizing floors, and saturation diagnosis - and delivers a filled targeting matrix per campaign. Use when someone asks "who should I target with these ads", "why is my ad set stuck in learning", "my CPMs keep rising but conversions are flat", "should I use a 1% or 5% lookalike", or is launching a campaign, diagnosing poor reach quality, or expanding into a new customer segment. Do NOT use for auditing an entire paid program's structure and spend efficiency - use paid-acquisition-audit instead; do NOT use for defining the ideal customer profile itself - use icp-persona-builder instead; do NOT use for market segmentation strategy outside paid media - use segmentation-strategy instead.
---

# Audience Targeting

A perfect ad shown to the wrong audience converts poorly, and the most common budget leak in paid media is not bad creative - it is unexcluded customers, oversized lookalikes, and ad sets too small to ever exit the learning phase. This skill defines who sees the ad, in what order audiences get built, and when an audience is exhausted. Targeting work begins before creative work and gets revisited monthly as audiences saturate.

## Operating procedure

### Step 1: Gather inputs

Collect before recommending any audience. Label estimates as guesses.

- Platform(s) and monthly budget per platform.
- Campaign objective: acquisition, retargeting, or retention.
- First-party assets available: CRM customer list (with LTV field if it exists), site traffic volume by page type, email engagement data.
- Target CAC or CPA, and the conversion event being optimized.
- Prior campaign learnings: known bad segments, past winning audiences.

### Step 2: Build first-party audiences before anything else

First-party audiences consistently outperform interest and demographic targeting, and they are owned rather than rented. Build in this order and do not launch interest-based targeting until they exist:

1. Customer list - CRM data uploaded and matched to platform users.
2. High-value segment - the customer list filtered to the top 20% by LTV.
3. Website visitors by page - product viewers separated from checkout abandoners.
4. Email engagers - opened or clicked in the last 90 days.

### Step 3: Check sizing floors before launch

An audience below the platform floor either cannot serve or never exits the learning phase, burning budget on unstable delivery.

- Meta: custom audiences need at least 100 matched users per country to serve; a lookalike seed needs 100 minimum but performs meaningfully better from 1,000 to 50,000 high-quality members. An ad set exits learning at roughly 50 optimization events in 7 days - if budget ÷ target CPA × 7 days cannot plausibly produce 50 conversions, consolidate ad sets or optimize a higher-funnel event.
- LinkedIn: hard floor of 300 members; below roughly 50,000 for sponsored content, expect high CPMs and slow delivery.
- Google: Customer Match needs several thousand matched records to serve reliably; similar/expansion segments serve the lookalike function.

### Step 4: Expand with lookalikes, seeded from the best signal

Seed lookalikes from the highest-quality first-party signal - the top-LTV customer segment, not the full customer list. On Meta, start at 1-2% for precision; expand to 3-5% only after CAC is proven at the tighter level. Lookalike quality degrades as the percentage grows, so monitor CAC at each expansion step.

### Step 5: Layer or stack deliberately

Layering narrows (interest AND behavior AND demographic); stacking broadens (more options within a category). For prospecting with volume goals, avoid over-layering - give the algorithm a broad-but-relevant audience to optimize within, and keep it above the learning-phase math from Step 3. For retargeting, layer aggressively to isolate the strongest intent signals.

### Step 6: Define exclusions - as important as inclusions

Always exclude: current customers from acquisition campaigns; recent converters from retargeting for a defined post-purchase window (default 30 days unless the repurchase cycle is shorter); and low-quality segments documented in prior campaigns. Unexcluded customers waste budget and inflate reported conversion rates with purchases that were organic anyway.

### Step 7: Test segments in isolation

Entering a new market or angle, run separate ad sets per segment rather than combining them, so you learn which segment responds to which message. Minimum read: 7 days and 1,000 impressions per segment before drawing conclusions. Consolidate budget into winners; pause and document losers in a segment log, the same way creative teams keep a creative log.

### Step 8: Monitor saturation monthly

Saturation shows up as three signals together: frequency above 3.5 in a 7-day window on Meta, declining CTR at stable bids, and rising CPMs with flat or falling conversions. Remedies in order: refresh creative first, then expand the audience, then find a new segment. Never raise bids into a saturated audience - it accelerates spend without improving results.

## Worked artifact: targeting matrix

Fill one row per ad set before launch. If any sizing check fails, restructure before spending.

```
TARGETING MATRIX - [FILL: campaign name] - [FILL: platform] - [FILL: monthly budget]

| Ad set | Objective | Audience definition | Est. size | Passes floor? | Exclusions | Learning math (budget/CPA*7 >= 50?) |
|--------|-----------|--------------------|-----------|---------------|------------|--------------------------------------|
| [FILL] | Prospect  | 1% LAL of top-20%-LTV customers | [FILL] | [FILL y/n] | customers, 30d converters | [FILL] |
| [FILL] | Prospect  | [FILL interest/broad definition] | [FILL] | [FILL y/n] | customers, 30d converters, LAL above | [FILL] |
| [FILL] | Retarget  | product viewers 14d, NOT purchasers | [FILL] | [FILL y/n] | 30d converters | [FILL] |

SEGMENT LOG ENTRY (after 7 days / 1,000+ impressions per segment)
  Winner: [FILL segment + CAC]     Action: consolidate budget
  Loser:  [FILL segment + CAC]     Action: pause, record reason
```

## Deliverable

Produce a completed targeting matrix for the campaign - every ad set with audience definition, estimated size against the platform floor, learning-phase math, and exclusion list - plus a segment log template and a monthly saturation check listing the three signals to watch.

## Do NOT

- Do not launch interest targeting before first-party audiences are built - you are paying rented-audience prices for worse performance.
- Do not seed lookalikes from the full customer list when a top-LTV segment exists; the seed quality caps the lookalike quality.
- Do not split budget across so many ad sets that none can reach ~50 weekly conversions - permanent learning phase is the most common self-inflicted wound.
- Do not skip exclusions; converting existing customers through paid acquisition is buying revenue you already had.
- Do not raise bids into a saturated audience - refresh creative, then expand, in that order.
- Do not judge a segment on fewer than 7 days and 1,000 impressions.

## Quality bar

- Every ad set in the matrix passes its platform sizing floor and the learning-phase math, or carries an explicit restructure note.
- Exclusion lists named per ad set, not assumed at campaign level.
- Lookalike seeds documented (source segment, size, percentage tier).
- Saturation thresholds (frequency 3.5, CTR trend, CPM trend) written into a recurring monthly check with an owner.

## Escalation and neighbors

For a full-program spend and structure audit, use paid-acquisition-audit. For defining who the ideal customer is in the first place, use icp-persona-builder. For pacing the budget the matrix implies, pair with budget-pacing; for testing the creative that fills these ad sets, ad-creative-testing.
