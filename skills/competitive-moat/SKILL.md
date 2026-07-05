---
name: Competitive Moat Analysis
description: Identifies which of the seven structural moats a business actually has, pressure-tests each claim against funded copycats and incumbent pivots, and delivers a moat scorecard with one moat to invest in deliberately. Use when someone asks "what's our moat", "is our product defensible", "a well-funded competitor just launched — what protects us", "investors keep asking about defensibility", or "which moat should we build first". Do NOT use for tracking what competitors are shipping and saying — use competitive-intelligence instead — or for finding unserved market segments — use white-space-analysis instead.
---

# Competitive Moat Analysis

A moat is a structural reason an advantage compounds rather than erodes. Most "moats" founders cite — features, a head start, proprietary data — are things a funded competitor copies in a quarter, and the costly mistake is building a fundraise or a strategy on one of them. This skill separates real moats from wishful thinking and names the one moat worth building deliberately.

## Operating procedure

Run taxonomy before testing, and testing before measurement — you cannot stress-test a moat you have not precisely named, and you should not bother measuring one that fails the adversary test.

### Step 1: Gather inputs

1. The claimed advantages — everything the founder or team believes protects them, verbatim.
2. Business model and stage — who pays, how much, how customers onboard and leave.
3. Competitive set — the funded startups and the incumbents, with rough war chests.
4. Retention data if available — gross retention by cohort age; label estimates as guesses.
5. Pricing power evidence — has a price increase ever stuck without churn spiking?

### Step 2: Map each claim to the seven real moats

Every claimed advantage must land in exactly one of these buckets or be reclassified as "feature, not moat":

1. Network effects — each user makes the product more valuable to others. Defensibility test: does value scale with n, with n-squared, or not with n at all? A social graph is n-squared; a leaderboard is barely n.
2. Switching costs — leaving is expensive in time, data, or risk. Test: what, concretely, would a customer lose by churning on Monday morning?
3. Scale economies — unit costs fall with volume to a level a smaller rival structurally cannot reach.
4. Counter-positioning — incumbents cannot copy the model without damaging their existing business. The strongest moat against giants. Test: name the specific revenue line the incumbent protects by NOT copying you; if you cannot name it, the counter-positioning is imagined.
5. Cornered resource — exclusive access to talent, IP, data, or supply. Exclusivity must be contractual or structural, not just current.
6. Brand — customers pay more or choose faster because of trust, not features. Test: demonstrated pricing power, not awareness metrics.
7. Process power — an organizational capability rivals cannot replicate even knowing how it works. Rare; Toyota-class. Default skepticism.

### Step 3: Run the adversary tests

For each surviving claim, run all three:

- The funded copycat: a competitor with $50M and your full feature list launches tomorrow. What still protects you? If the answer is nothing, it was a feature, not a moat.
- The incumbent pivot: the market leader decides you are a threat. Which moats survive their distribution advantage? (Usually only counter-positioning and genuine network effects.)
- The time test: does the moat strengthen or weaken with each passing quarter? Real moats compound; a head start decays.

### Step 4: Measure what survived

- Network effects: correlation between cohort size (or network density) and retention. Retention that is flat regardless of network size means no network effect.
- Switching costs: gross retention of multi-year cohorts. Multi-year gross retention above ~90% annually is consistent with real switching costs; below 80%, the switching-cost claim is dead on arrival.
- Scale: model your cost curve against a sub-scale competitor's at their volume, not yours.
- Counter-positioning: the named incumbent revenue at risk, in dollars.
- Brand: a price increase that stuck. Anything else is marketing spend, not brand.

### Step 5: Score and choose

Rate each of the seven types absent / emerging / strong with the evidence and the adversary-test result. Then pick the single moat to invest in over the next year. If every row reads "absent," say so plainly — that is the most important strategic finding, not a failure of the analysis. Sequence the model to create network effects or switching costs early, before competition arrives.

## Worked artifact: scorecard contrast

Bad:

```
Moats: first mover, great team, AI-powered, proprietary data, strong brand.
```

Every item fails Step 2: none is one of the seven, "proprietary data" has no flywheel stated, and "brand" has no pricing-power evidence.

Good:

```
MOAT SCORECARD — B2B data platform, Series A
Network effects      EMERGING  Shared benchmarks improve with each customer;
                               retention +6pts for customers in 3+ benchmark
                               pools vs solo. Survives copycat (data can't be
                               bootstrapped fast) — weak vs incumbent w/ install base.
Switching costs      STRONG    3yr cohorts at 94% gross retention; migration
                               = 6 months of pipeline history lost. Survives all three tests.
Scale economies      ABSENT    Cloud costs linear with usage.
Counter-positioning  EMERGING  Incumbent charges per-seat ($240M line); our
                               usage pricing cannibalizes it. Named revenue: yes.
Cornered resource    ABSENT    No exclusive contracts.
Brand                ABSENT    No demonstrated pricing power yet.
Process power        ABSENT    Default.
INVEST: deepen switching costs — ship workflow embeds that make Monday-morning
churn cost a quarter of rework. Re-test in 12 months.
```

## Deliverable

Produce a moat scorecard: each of the seven types rated absent / emerging / strong, with the evidence, the metric from Step 4, and the result of all three adversary tests — concluding with the one moat to invest in deliberately over the next year and the specific mechanism for deepening it.

## Do NOT

- Do not accept a head start as a moat; being first only matters if it is being converted into one of the seven.
- Do not claim brand without pricing power to prove it — awareness is not a moat.
- Do not treat proprietary data as a moat by default; it only qualifies if the data improves the product in a flywheel competitors cannot bootstrap.
- Do not let more than two moats be rated "strong" without exceptional evidence; real strong moats are rare, and a scorecard full of them means the tests were soft.
- Do not skip the "none yet" verdict to spare feelings — a false moat in a board deck or fundraise narrative is worse than an honest gap.

## Quality bar

- Every claimed advantage is either mapped to one of the seven types or explicitly reclassified as a feature.
- Every non-absent rating cites evidence a skeptic could check, and every "strong" rating survived all three adversary tests.
- Counter-positioning claims name the specific incumbent revenue protected by not copying.
- The scorecard ends with exactly one moat to invest in and the concrete mechanism.
- Guessed numbers are labeled as guesses.

## Escalation

For monitoring what competitors actually ship and say, route to competitive-intelligence; if the moat verdict feeds a fundraise story, pair with fundraising-narrative.
