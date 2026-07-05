---
name: Customer Analytics
description: Turns transaction data into cohort retention, lifetime value, RFM segments, and churn predictions tied to concrete actions. Use when someone asks "what's our customer LTV", "build a cohort retention table", "which customers are about to churn", "how do repeat purchase rates look by cohort", or "who should we target with a win-back offer". Do NOT use for event-level product usage and feature adoption analysis — use product-analytics instead; for defining market segments for positioning and messaging — use segmentation-strategy instead; for designing the interventions that reduce churn once at-risk customers are identified — use churn-reduction instead; for step-by-step conversion drop-off — use funnel-analysis instead.
---

# Customer Analytics

Transaction logs already contain the answers to "who stays, who spends, who is about to leave" — the failure mode is computing them wrong: cohort charts that include the current incomplete month, LTV built on revenue instead of margin, and churn models validated on shuffled data that collapse in production. This skill produces cohort, LTV, and churn outputs a finance or lifecycle team can act on directly.

## Operating procedure

Work the steps in order: cohorts establish the retention reality that LTV math depends on, and churn modeling is only worth doing after the churn definition survives the cohort view.

### Step 1: Gather inputs

Collect these first; apply defaults and label guesses as guesses.

1. Transaction table: customer_id, order_date, revenue, and cost or margin if available.
2. Business model: contractual (subscription) or non-contractual (e-commerce/retail). This decides the LTV method in Step 3.
3. Cohort granularity. Default: monthly cohorts when the natural purchase cycle is 2 weeks or longer; weekly cohorts for high-frequency products (food delivery, media). Granularity finer than the purchase cycle produces jagged, unreadable triangles.
4. Gross margin, as a fraction. If unknown, get finance's blended number and mark it a guess — never silently assume 100%.
5. Churn definition. Contractual: subscription cancelled or lapsed. Non-contractual default: no purchase in 90 days, but check it against the observed inter-purchase distribution — set the cutoff near the 90th percentile of gaps between orders, not at a round number chosen by habit.

### Step 2: Build cohorts and retention curves

Group customers by acquisition period and track activity across subsequent periods:

```sql
with first_order as (
  select customer_id,
         date_trunc('month', min(order_date)) as cohort_month
  from orders group by 1
),
activity as (
  select o.customer_id,
         f.cohort_month,
         date_trunc('month', o.order_date) as active_month
  from orders o join first_order f using (customer_id)
)
select cohort_month,
       date_diff('month', cohort_month, active_month) as month_number,
       count(distinct customer_id) as active_customers
from activity group by 1, 2 order by 1, 2;
```

Render as a retention triangle: cohorts on rows, months-since-acquisition on columns, each cell as a percent of cohort size. Always exclude the most recent incomplete period — it reads as a retention cliff that is not there. Retention usually drops fast then flattens; the flattening level is the durable base, and it is the single most important number on the chart. Reference points: e-commerce cohorts commonly flatten with 20-30% of customers making a repeat purchase; healthy subscription monthly logo churn runs under 5% for SMB, under 2% for mid-market, under 1% for enterprise. A cohort curve that never flattens means the product has no retained core — fix that before spending on acquisition.

### Step 3: Lifetime value

Two approaches; label which one each number came from.

1. Historical: sum realized margin per customer to date. Ground truth, but backward-looking.
2. Predictive: model expected future value.

Contractual LTV:

```
LTV = ARPU * gross_margin / churn_rate
```

Use margin, never revenue — revenue LTV flatters the business by the full cost of goods. For non-contractual businesses the churn-rate formula is invalid (there is no observable churn event); use a BG/NBD model for purchase frequency and a Gamma-Gamma model for monetary value:

```python
from lifetimes import BetaGeoFitter, GammaGammaFitter
bgf = BetaGeoFitter(penalizer_coef=0.01)
bgf.fit(rfm['frequency'], rfm['recency'], rfm['T'])
```

Cap the projection horizon at roughly the data you have: with 12 months of history, projecting beyond 24 months is extrapolation — present it as a range, not a number. Report LTV by cohort and by acquisition channel, because the blended average hides the channels that are underwater.

### Step 4: RFM segmentation

Score customers on Recency, Frequency, and Monetary value into quintiles (1-5 each), then group into named, actionable segments: Champions (R5 F4-5), Loyal, At Risk (R1-2 F4-5 — used to buy often, went quiet), and Hibernating (R1-2 F1-2). Each segment must map to one campaign: Champions get referral asks, At Risk gets the win-back offer (pair with win-back-campaign), Hibernating gets suppressed from paid retargeting to save spend.

### Step 5: Churn prediction

Only after the churn definition is fixed. Build features per customer as of a snapshot date:

- Recency, frequency, monetary, tenure.
- Trend features: change in order rate quarter over quarter — trend beats level for early warning.
- Engagement: logins, support tickets, feature usage where available.

Train a gradient boosting classifier. Evaluate with AUC and, more importantly, precision at the top decile — that is the list the retention team will actually work. Calibrate probabilities so "70% churn risk" means 70%. Validate on a held-out future window, never a random shuffle: shuffled validation leaks the future and overstates AUC. Use SHAP to explain drivers so the business can act on causes, not just scores.

## Worked artifact: retention triangle excerpt with reading

```
Cohort     Size    M0     M1     M2     M3     M6
2024-01    2,140   100%   34%    27%    24%    22%
2024-02    2,380   100%   31%    25%    23%    21%
2024-03    2,910   100%   26%    19%    16%    —
Reading: Jan and Feb flatten at 21-22% — a durable base. The March cohort is
tracking 5-7 points below at every age: acquisition mix shifted (paid social
spike). LTV for March-acquired customers will run ~25% below plan; flag the
channel before scaling it further.
```

## Deliverable

Produce a customer analytics pack containing: the retention triangle with the flattening level called out, LTV by cohort and channel (method labeled historical or predictive, margin-based), RFM segment sizes with one mapped action each, and a scored churn list with top-decile precision and the top three SHAP drivers.

## Do NOT

- Do not include the current incomplete period in cohort charts; it fabricates a retention cliff.
- Do not compute LTV on revenue; use contribution margin.
- Do not apply the ARPU/churn formula to non-contractual businesses; use BG/NBD + Gamma-Gamma.
- Do not validate a churn model with a random split; use a held-out future window or the production model will disappoint.
- Do not ship a metric without an owner and an action — "who do we contact, with what offer" is part of the analysis, not a follow-up.

## Quality bar

- Cohort definition, churn definition, and margin assumption are written on the artifact.
- Every LTV number states its method and horizon; projections beyond ~2x the observed history are presented as ranges.
- Churn model reports AUC and top-decile precision on a future window, with calibrated probabilities.
- Each RFM segment has exactly one attached action and owner.
