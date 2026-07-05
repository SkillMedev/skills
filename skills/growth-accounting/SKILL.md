---
name: Growth Accounting
description: Decomposes active-user or revenue growth into new, retained, resurrected, expansion, contraction, and churned flows, reconciles the identity exactly, and reads the quick ratio to diagnose whether growth is an acquisition, retention, or resurrection problem. Use when someone asks "why did our MAU change", "our top line grew but I don't trust it", "what's our quick ratio", "build an MRR bridge", or "is our growth acquisition-driven or retention-driven". Do NOT use for forecasting future growth from loops and assumptions — use growth-model instead — for general event instrumentation and funnel metrics — use product-analytics instead — or for churn interventions themselves — use churn-reduction instead.
---

# Growth Accounting

A top-line active-user or revenue chart can rise while the business underneath rots: heavy acquisition masking accelerating churn is the classic failure, and it is invisible until the paid channel stalls. Growth accounting decomposes every period's change into its flows so the chart cannot lie, and the decomposition tells you which lever — acquisition, retention, or resurrection — actually needs work.

## Operating procedure

Reconcile before you interpret: an identity that does not balance means the flags are wrong, and every downstream conclusion with them.

### Step 1: Gather inputs

1. The activity definition — what user action counts as "active." Require a meaningful action, not just a login; default to the product's core value event.
2. The period — daily, weekly, or monthly. Match it to the natural usage cadence and keep it consistent; a monthly product measured weekly will show fake churn.
3. The event table — one row per user per active period (or MRR per account per month for revenue).
4. Whether the user wants user-count accounting, revenue accounting (the MRR bridge), or both. Default: both if MRR exists.
5. Segmentation dimensions available — cohort, channel, plan.

### Step 2: Compute the user flows

Comparing the set of active users this period versus last:

- New: active this period, never active before.
- Retained: active this period and last period.
- Resurrected: active this period, inactive last period, but active sometime earlier.
- Churned: active last period, not active this period (a negative contribution).

The core identity must reconcile exactly:

```
Active(t) = Active(t-1) + New + Resurrected - Churned
```

Net new users = New + Resurrected - Churned, and it must equal Active(t) - Active(t-1). If it does not, stop and fix the flags before charting anything.

Reference SQL (monthly periods, window functions):

```sql
with activity as (
  select user_id, date_trunc('month', activity_date) as period
  from events group by 1, 2
),
flags as (
  select user_id, period,
    lag(period) over (partition by user_id order by period) as prev_period,
    min(period) over (partition by user_id) as first_period
  from activity
)
select period,
  count(*) filter (where period = first_period) as new_users,
  count(*) filter (where prev_period = period - interval '1 month') as retained,
  count(*) filter (where period <> first_period
                   and (prev_period is null
                        or prev_period < period - interval '1 month')) as resurrected
from flags group by period order by period;
```

Compute churned separately as users active last period but absent this period.

### Step 3: Compute the revenue flows (MRR bridge)

The same framework applies to dollars, with two extra flows because accounts resize:

```
Net new MRR = New + Expansion - Contraction - Churned
MRR(t) = MRR(t-1) + New + Expansion + Resurrected - Contraction - Churned
```

Expansion is existing accounts paying more; contraction is existing accounts paying less. This bridge connects user-level growth accounting to financial outcomes.

### Step 4: Compute and read the quick ratio

```
Quick Ratio (users)   = (New + Resurrected) / Churned
Quick Ratio (revenue) = (New + Expansion + Resurrected) / (Contraction + Churned)
```

Reading it: below 1 means shrinking regardless of what the top line looks like. Near 1 means treading water — growth is stalling even if the line still rises on acquisition timing. For early-stage SaaS, above 4 is healthy; mature consumer products often live in the 1.2-1.5 range, so judge against the product's category, not one universal bar.

### Step 5: Diagnose and segment

- If growth slowed and New fell: acquisition problem — route to channel-strategy.
- If Churned is rising while New holds: retention problem — route to churn-reduction; pair the churn flow with reason analysis.
- If Resurrected is a large share of "growth": you have a leaky product being bailed out by win-backs; treat as retention.
- Segment every flow by cohort, channel, and plan to localize the problem — an aggregate quick ratio of 3 can hide one channel at 6 and another at 0.8.

## Worked artifact: growth-accounting table

Monthly MRR bridge for an early-stage SaaS:

```
Month     Start MRR   New    Expansion  Contraction  Churned   Net New   End MRR   Quick Ratio
Mar        $80,000  $12,000    $3,000      $1,000     $2,500    $11,500   $91,500      5.0
Apr        $91,500  $11,000    $3,500      $1,500     $4,000    $9,000   $100,500      2.6
May       $100,500  $13,000    $3,000      $2,000     $6,500    $7,500   $108,000      1.9

Check: 80,000 + 11,500 = 91,500 ✓   91,500 + 9,000 = 100,500 ✓   100,500 + 7,500 = 108,000 ✓
```

Read it: the top line grew every month, but the quick ratio fell from a healthy 5.0 to 1.9 because churned MRR rose 2.6x in two months while new MRR held flat. This is the acquisition-masking-churn pattern — the correct response is retention work, not more spend, and the top-line chart alone would never have shown it.

## Deliverable

Produce a growth-accounting report: the flow table per period (users and/or MRR) with the reconciliation check shown, the quick ratio trend with a category-appropriate reading, the diagnosis naming which flow drives the change, and the segmented breakdown that localizes it to a cohort, channel, or plan.

## Do NOT

- Do not chart flows before the identity reconciles — an unbalanced identity means misclassified users, and every insight downstream is wrong.
- Do not define "active" as login; a vanity activity definition inflates retained and hides churn.
- Do not mix periods (weekly actives, monthly churn) — flows are only comparable within one consistent period.
- Do not celebrate a flat or rising active count without decomposing it; rising acquisition offsetting rising churn is fragile, not stable.
- Do not apply one quick-ratio benchmark across categories — 4 is the early-SaaS health bar, not a consumer one.
- Do not count resurrected users as proof of product love; heavy resurrection usually flags a retention leak.

## Quality bar

- Every period's identity reconciles exactly, and the check is shown, not asserted.
- The activity definition is stated and is a meaningful action.
- Quick ratio appears with its trend and a category-calibrated interpretation, never as a bare number.
- The report names one primary driver flow and routes to the fix (acquisition vs retention vs resurrection).
- At least one segmentation cut is included; aggregates alone do not ship.
