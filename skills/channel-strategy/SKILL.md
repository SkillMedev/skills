---
name: Channel Strategy
description: Picks, sequences, and scales the distribution channels that fit a company's economics, producing a scored channel matrix and a 12-month sequencing plan. Use when someone asks "which marketing channel should we focus on", "should we do paid or content or outbound", "our growth is stalling across channels", "when do we add a second channel", or "is this channel worth scaling". Do NOT use for auditing an existing paid-ads account - use paid-acquisition-audit instead - for the full go-to-market plan around the channels - use go-to-market-planner instead - or for modeling how channels compound into a growth model - use growth-model instead.
---

# Channel Strategy

Most startups die from lack of distribution, not lack of product, and the most common self-inflicted wound is spreading effort across ten channels before any single one works. At any given stage, one channel drives the majority of growth; the job is to find it, saturate it, and only then add the next. This skill produces the scored matrix and the sequencing plan that enforce that focus.

## Operating procedure

Work the steps in order - scoring before sequencing, sequencing before spend - because a sequencing plan built on unscored channels is a wish list.

### Step 1: Gather inputs

Collect these before scoring anything. Label guesses as guesses.

1. ACV (annual contract value) or average order value - this alone eliminates channels.
2. Buyer behavior - do buyers search, scroll, get referred, or get sold to?
3. Current channels and their share of new pipeline or revenue.
4. Current CAC and payback per channel, if known (default: unknown, estimate and flag).
5. Gross margin and retention - payback tolerance depends on both.
6. Cash and team constraints - a channel you cannot staff is not a channel.

### Step 2: Inventory the candidate channels

Score all six families, even ones that feel wrong - the point is a defensible elimination:

- Paid acquisition (search, social, programmatic): fast feedback, scales with cash, margins compress as spend scales.
- Content / SEO: slow to start, compounding, defensible. Best when buyers actively search for solutions.
- Sales-led (inbound or outbound): essential for high ACV; expensive per rep; needs a repeatable motion.
- Product-led / virality: the product is the channel; requires built-in sharing or collaboration loops and short time-to-value.
- Partnerships / channel sales: someone else's distribution; slow to negotiate, high ceiling. Pair with partnership-strategy for deal structure.
- Community / marketplaces: high trust, hard to manufacture, slow to fake.

### Step 3: Score each channel on five criteria

Rate 1-5 on each; multiply nothing - read the pattern, not a fake composite:

1. Fit with ACV. Sales-led needs roughly >$5K ACV to pay for reps; PLG needs low friction and short time-to-value. Below ~$1K ACV, human-touch sales is structurally unprofitable.
2. Fit with buyer behavior. Match the channel to how the buyer already finds solutions.
3. CAC and payback. Estimate fully-loaded cost per customer and months to recover it. Payback over 12 months needs strong retention (net revenue retention above 100%) to justify; over 18 months is a red line for a cash-constrained company.
4. Saturation ceiling. How many customers can this channel plausibly deliver before it caps?
5. Defensibility. Paid is rented - a competitor can outbid you tomorrow; content, community, and product loops are owned.

### Step 4: Sequence

- Pick the channel with the fastest learning loop first: usually paid for testing demand, or founder-led sales for high ACV.
- Use early paid spend to learn messaging and ICP even if uneconomical - you are buying data, and you say so in the plan.
- Layer exactly one compounding channel (content or product loops) alongside the primary early, so growth is not rented forever.
- Add a third channel only after the primary shows stable-or-falling CAC at 2-3x current spend.

### Step 5: Enforce economics discipline and concentration limits

- Track CAC, payback, and the trend as spend scales, per channel, monthly.
- Kill or cap any channel where CAC rises faster than LTV.
- Reallocate to the best marginal return, not the best average return - the last dollar in a saturating channel underperforms the first dollar in a fresh one.
- Concentration red line: no single channel should exceed 60% of pipeline once past early stage. Above that, a platform policy change or auction shift can halve growth overnight; the mitigation is the compounding channel from Step 4, not panic diversification.

## Worked artifact: channel matrix

Bad:

```
Plan: post on LinkedIn, run some Google ads, start a blog, try cold email,
launch on Product Hunt, and look into partnerships. Revisit in Q3.
```

Six channels, no scores, no primary, no kill criteria - this is how growth stalls.

Good:

```
CHANNEL MATRIX - B2B SaaS, $8K ACV, buyers search + get referred
                    ACV-fit  Buyer-fit  CAC/payback  Ceiling  Defensible
Paid search            4        5        $1,900 / 7mo    3        2
Outbound sales         4        3        $3,200 / 11mo   4        3
Content/SEO            4        5        low / slow      4        5
PLG loops              1        2        n/a             2        4
Partnerships           3        3        unknown (guess) 4        3
Community              2        3        n/a             2        5

PRIMARY: paid search (fastest loop, 7mo payback clears the 12mo bar)
COMPOUNDING: content/SEO started now, expected to carry share by month 9-12
KILL RULE: cap paid if CAC exceeds $2,800 or payback exceeds 12mo
CONCENTRATION: paid at 70% of pipeline today - over the 60% line;
content share must reach 25%+ within 12 months to de-risk
```

## Deliverable

Produce a channel matrix scoring every candidate channel on ACV-fit, buyer-fit, CAC and payback, ceiling, and defensibility, then a 12-month sequencing plan naming the single primary channel, the one compounding channel built alongside it, the kill/cap rule per channel, and the concentration check against the 60% line.

## Do NOT

- Do not do "a bit of everything" - focus beats breadth at every early stage, and split budgets learn nothing.
- Do not copy a competitor's channel without their economics; their ACV may support what yours cannot.
- Do not scale spend before unit economics work - you only multiply losses. Pair with unit-economics to verify first.
- Do not read average CAC when deciding where the next dollar goes; marginal CAC is the decision number.
- Do not let one channel pass 60% of pipeline without a named compounding hedge already in motion.

## Quality bar

- Every channel family is scored, including the ones eliminated, with the elimination reason stated.
- Exactly one primary channel and one compounding channel are named - not two primaries.
- Each active channel has a numeric kill or cap rule (CAC ceiling or payback limit).
- The plan states current channel concentration and flags anything over 60%.
- Every estimated number is labeled a guess where it is one.
