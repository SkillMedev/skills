---
name: Funnel Analysis
description: Builds conversion funnels from raw event data with drop-off attribution, segment comparison, and significance testing, and delivers a filled funnel table with a diagnosed bottleneck and next action. Use when someone asks "where are users dropping off", "why did checkout conversion fall this week", "build a signup-to-activation funnel", or "is mobile converting worse than desktop". Do NOT use for rewriting page copy or layout to lift a known weak step — use landing-page-cro instead; for decomposing active-user growth into new, retained, resurrected, and churned — use growth-accounting instead; for choosing which events to instrument and which product metrics to track — use product-analytics instead; for designing and reading a controlled experiment — use ab-test-analyzer instead.
---

# Funnel Analysis

A funnel tells you where money leaks out of a multi-step flow, but only if the definition is precise and the differences you report are statistically real. The costly mistake this skill prevents is shipping a "mobile checkout is broken" diagnosis built on an ambiguous funnel definition and a 40-user sample — teams reorganize roadmaps around noise. The output is a filled funnel table, one named bottleneck step, and the evidence that the gap is real.

## Operating procedure

Follow the steps in order: the funnel definition (Step 1) determines every count downstream, and significance testing (Step 5) must come before any diagnosis leaves the room.

### Step 1: Gather inputs and pin the definition

Collect these before touching SQL. If the user cannot answer, apply the default and label it a guess.

1. Ordered steps, as event names (e.g. view -> add_to_cart -> checkout -> purchase). Write them down verbatim; ambiguous definitions produce misleading funnels.
2. Conversion window: how long after step 1 a user has to finish. Default: the 90th percentile of observed time-to-convert, rounded up to a clean unit. Typical defaults if no data yet: 1 day for e-commerce checkout, 7 days for signup-to-activation, 14-30 days for B2B trial-to-paid.
3. Ordered vs unordered: must steps happen in sequence, or just all occur within the window? Default: ordered.
4. Counting unit: unique users (default) or sessions. Never mix.
5. Segments to compare: device, channel, plan, acquisition cohort. Cap at the 2-3 the team can act on.

### Step 2: Compute the funnel in SQL

Take each user's first qualifying timestamp per step and enforce ordering by requiring each timestamp to exceed the prior one. Deduplicate events first.

```sql
with steps as (
  select user_id,
         min(case when event = 'view' then event_time end)        as t_view,
         min(case when event = 'add_to_cart' then event_time end) as t_cart,
         min(case when event = 'checkout' then event_time end)    as t_checkout,
         min(case when event = 'purchase' then event_time end)    as t_purchase
  from events
  group by user_id
)
select
  count(t_view)                                              as viewed,
  count(case when t_cart > t_view then t_cart end)           as carted,
  count(case when t_checkout > t_cart then t_checkout end)   as checked_out,
  count(case when t_purchase > t_checkout then t_purchase end) as purchased
from steps;
```

Exclude users whose window has not yet elapsed (right-censoring), or the newest cohort will understate conversion and look like a regression that is not there.

### Step 3: Attribute the drop-off

For each adjacent pair compute step conversion = stage_n / stage_n-1, and report overall conversion = final / first. Compare each step against a benchmark band, not against zero — the biggest raw drop is not automatically the biggest opportunity if that drop is normal for the industry. Defensible e-commerce reference bands: view -> cart 8-12%, cart -> checkout 40-60%, checkout -> payment 70-85%, payment -> purchase 80-90%, overall visit -> purchase 2-3%. SaaS signup -> activation commonly lands at 20-40%. The bottleneck is the step furthest below its band, not the smallest percentage.

### Step 4: Segment comparison

Recompute the funnel per segment. A segment gap at one specific step points to a localized problem (a broken payment form on mobile); a uniform gap across all steps points to traffic quality, which is an acquisition problem, not a funnel problem — route that to paid-acquisition-audit or channel strategy work.

### Step 5: Test significance before diagnosing

Do not eyeball rate differences. Compare two conversion rates with a two-proportion z-test:

```python
from statsmodels.stats.proportion import proportions_ztest
stat, p = proportions_ztest([conversions_a, conversions_b], [n_a, n_b])
```

Sizing intuition: detecting a 2-percentage-point difference on a 20% baseline at alpha 0.05 and 80% power needs roughly 6,000-6,500 users per segment. Below roughly 1,000 users per segment, only very large gaps (10+ points) are detectable — say so explicitly rather than reporting point estimates as findings. When comparing many segments, control the false discovery rate with Benjamini-Hochberg. Report confidence intervals on each rate, not just point estimates.

### Step 6: Check time-to-convert and trend

Beyond whether users convert, analyze how long each step takes. A rising median time at a step signals friction even when conversion looks flat. Track the funnel weekly: a step conversion drop of more than 10% relative week-over-week warrants investigation; a same-day drop of more than 20% relative is a release-bug signal — check the deploy log before the analytics.

## Worked artifact: filled funnel with diagnosis

```
FUNNEL: e-commerce purchase, unique users, ordered, 1-day window, week of analysis
Step              Users     Step conv   Benchmark    Verdict
View product      48,200    —           —            —
Add to cart        4,340    9.0%        8-12%        in band
Begin checkout     2,120    48.8%       40-60%       in band
Enter payment      1,020    48.1%       70-85%       RED FLAG (-22 pts below band)
Purchase             890    87.3%       80-90%       in band
Overall: 890 / 48,200 = 1.85%  (band 2-3%: below, explained by payment step)

Segment split at "Enter payment":
  Desktop: 61.4% (n=1,180)   Mobile: 35.2% (n=940)
  Two-proportion z-test: p < 0.001 — gap is real.

DIAGNOSIS: payment-entry step on mobile is the bottleneck. Fixing mobile to
desktop parity is worth ~246 extra purchases/week at current traffic.
NEXT ACTION: session recordings on mobile payment page; hand the fix to landing-page-cro.
```

## Deliverable

Produce a funnel table containing: step names and definitions, user counts, step and overall conversion with benchmark bands, the named bottleneck step, segment comparison with p-values and sample sizes, the estimated volume recoverable from closing the gap, and one recommended next action.

## Do NOT

- Do not present a funnel without writing down the window, ordering rule, and counting unit — two teams computing "the same funnel" differently is the most common source of dashboard wars.
- Do not diagnose from segments under ~1,000 users without a significance test; small-sample gaps reverse week to week.
- Do not treat the largest raw drop as the opportunity; compare each step to its benchmark band.
- Do not include users still inside the conversion window; right-censoring makes healthy funnels look broken.
- Do not stop at the number. Pair quantitative drop-off with session recordings or user surveys to learn why, or the fix will be a guess.

## Quality bar

- Funnel definition (steps, window, ordering, unit) is stated on the artifact itself.
- Events were deduplicated and ordering enforced in the query.
- Every reported segment gap carries a sample size and a p-value or confidence interval, with Benjamini-Hochberg applied when more than ~5 segments were compared.
- Exactly one bottleneck is named, with the recoverable volume quantified.
- The trend view exists — a single-snapshot funnel cannot distinguish a bug from a baseline.
