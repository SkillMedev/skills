---
name: Revenue Modeling
description: Builds SaaS revenue models - a reconciling MRR/ARR bridge, cohort retention forecasting, a driver tree from leads to new ARR, and base/upside/downside scenarios. Use when someone asks "model my ARR for next year", "what will MRR be if churn doubles", "build a revenue forecast for the board", "why doesn't my MRR bridge tie out", or "what NRR do we need to hit our plan". Do NOT use for sizing the market opportunity - use market-sizing instead; for CAC, LTV, and payback analysis use unit-economics; for a full P&L or headcount-driven operating model use fpa-model; for cash timing and runway use cash-flow-forecast.
---

# Revenue Modeling

A revenue model that does not reconcile is a story, not a model. The costly failure mode is a forecast built from a single blended growth rate: it hides which lever (new logos, expansion, churn) is actually moving, so the plan cannot be managed and the miss cannot be diagnosed. Build the model from a bridge that must tie out and drivers that someone owns.

## Operating procedure

Steps run in this order because the bridge defines the accounting, the drivers feed the bridge, and scenarios only mean something once base-case drivers are pinned.

### Step 1: gather inputs

Collect, and label every unsourced number a guess:

- Beginning MRR (or ARR) and customer count, tied to the billing system or GL - not a spreadsheet someone remembers.
- Trailing 6-12 months of the bridge components: new, expansion, reactivation, contraction, churned MRR.
- Funnel history: leads (or pipeline created), lead→opportunity rate, opportunity→win rate, average deal size (ACV).
- Cohort retention: revenue retained by acquisition month at months 1, 3, 6, 12. If unavailable, start with logo churn and note the model is weaker for it.
- Billing mix (annual prepay vs monthly) - needed later to separate bookings, billings, and recognized revenue.

Defaults when history is thin: mid-market SaaS commonly sees 15-30% lead→opp, 15-25% opp→win, and 2-4% monthly gross revenue churn for SMB versus 0.5-1.5% for enterprise. Use these only as placeholders and say so.

### Step 2: build the MRR bridge

Every period:

```
Ending MRR = Beginning MRR
           + New MRR          (newly acquired customers)
           + Expansion MRR    (upgrades, seat adds, cross-sell)
           + Reactivation MRR (returning churned customers)
           - Contraction MRR  (downgrades)
           - Churned MRR      (cancellations)
```

Validate every period: components must sum exactly to the change in MRR, and the bridge must reconcile against the general ledger. If it is off by even 1%, find the leak before forecasting - the usual suspects are mid-period upgrades booked as new, or reactivations netted into churn.

### Step 3: compute the key rates

- Gross revenue churn = (churned + contraction MRR) / beginning MRR.
- Net revenue retention (NRR) = (beginning + expansion − contraction − churned) / beginning. NRR above 100% means the existing base grows with zero new logos; durable public-SaaS benchmarks cluster at 100-120%, with under 90% a red flag for the model's whole premise.
- Logo churn = customers lost / customers at start. Track it separately from revenue churn - losing many small logos and one big logo look identical in revenue terms but demand different fixes.

### Step 4: build the driver tree for new revenue

Model New MRR bottom-up, never as a single growth rate. Decompose top-down until every leaf is a number one team owns:

```
New ARR
├── New logos won
│   ├── Leads (marketing owns)
│   ├── Lead → Opportunity rate (marketing/SDR owns)
│   └── Opportunity → Win rate (sales owns)
└── Average deal size / ACV (sales + pricing own)

New MRR = leads × lead_to_opp × opp_to_win × avg_deal_size / 12
```

Procedure: (1) list the leaves; (2) assign each an owner and a historical baseline; (3) check the tree multiplies back to actual new MRR for the trailing two quarters - if it misses by more than 10%, a driver is mis-measured; (4) forecast each leaf, not the product. This is what connects marketing spend and sales capacity to the revenue line.

### Step 5: forecast the existing base with cohorts

Forecast retained revenue per acquisition cohort, then layer new cohorts on top:

```
For each acquisition cohort:
  revenue_t = cohort_initial_mrr × retention_curve[t] × expansion_factor[t]
Total MRR_t = Σ cohort revenue_t + new cohorts from the driver tree
```

Fit the retention curve from historical cohorts. Do not assume linear decay - SaaS retention curves flatten after months 3-6, and a linear assumption understates long-run revenue badly.

### Step 6: run scenarios

Build base, upside, and downside by flexing win rate, churn, expansion, and ACV - one inputs sheet, scenarios one toggle away (structure it with spreadsheet-model-builder). Add a 2D sensitivity grid: NRR on one axis, new-logo growth on the other, ending ARR in the cells. Stress-test the downside explicitly: what happens to the plan if churn doubles.

### Step 7: connect to cash and economics

- ARR = MRR × 12.
- Keep bookings, billings, and recognized revenue separate - annual-prepay bookings inflate cash today but recognize monthly. Hand cash timing to cash-flow-forecast.
- CAC payback = CAC / (ARPA × gross margin); LTV:CAC should exceed 3. The full treatment lives in unit-economics - reference it, do not rebuild it here.

## Worked example: one quarter of the ARR model

Self-contained Python. Save as `arr_model.py`, edit the inputs, run `python3 arr_model.py`.

```python
inputs = {
    "beginning_arr": 2_400_000,
    "beginning_customers": 200,
    "leads_per_quarter": 900,
    "lead_to_opp": 0.20,
    "opp_to_win": 0.22,
    "avg_deal_acv": 14_000,
    "quarterly_gross_churn": 0.03,    # churned + contraction, % of beginning ARR
    "quarterly_expansion": 0.045,
    "quarterly_logo_churn": 0.025,
}

def quarter(i):
    wins = i["leads_per_quarter"] * i["lead_to_opp"] * i["opp_to_win"]
    new_arr = wins * i["avg_deal_acv"]
    expansion = i["beginning_arr"] * i["quarterly_expansion"]
    churn = i["beginning_arr"] * i["quarterly_gross_churn"]
    ending_arr = i["beginning_arr"] + new_arr + expansion - churn
    nrr_annualized = ((i["beginning_arr"] + expansion - churn) / i["beginning_arr"]) ** 4
    customers = i["beginning_customers"] * (1 - i["quarterly_logo_churn"]) + wins
    return wins, new_arr, expansion, churn, ending_arr, nrr_annualized, customers

wins, new_arr, exp, churn, end, nrr, cust = quarter(inputs)
print(f"New logos won:        {wins:.1f}")
print(f"New ARR:              ${new_arr:,.0f}")
print(f"Expansion ARR:        ${exp:,.0f}")
print(f"Churned+contraction:  ${churn:,.0f}")
print(f"Ending ARR:           ${end:,.0f}")
print(f"NRR (annualized):     {nrr:.1%}")
print(f"Ending customers:     {cust:.0f}")
```

Output:

```
New logos won:        39.6
New ARR:              $554,400
Expansion ARR:        $108,000
Churned+contraction:  $72,000
Ending ARR:           $2,990,400
NRR (annualized):     106.1%
Ending customers:     235
```

Read it: 900 leads at 20% and 22% conversion yield ~40 logos at $14k ACV - $554k of new ARR. The base contributes $108k expansion against $72k churn, an annualized NRR of 106%. Ending ARR of $2.99M is 25% quarterly growth, and the bridge decomposition shows 84% of it comes from new logos - so the plan lives or dies on the funnel, not retention.

## Deliverable

Produce a revenue model workbook (or script) containing: a monthly or quarterly MRR bridge that reconciles to the ledger, the driver tree with an owner and baseline per leaf, cohort-based retained-revenue forecast, base/upside/downside scenarios with a single inputs sheet, an NRR × new-logo-growth sensitivity grid, and an assumptions page documenting every number's source. Show actuals vs forecast each period and track forecast accuracy over time.

## Do NOT

- Do not forecast with a single blended growth rate - it cannot be diagnosed when it misses and no one owns it.
- Do not let the bridge fail to reconcile "for now"; every downstream number inherits the error.
- Do not blend contraction into churn or reactivation into new - the fixes for each are different teams.
- Do not treat bookings as revenue; annual prepay makes them wildly different in-quarter.
- Do not assume linear retention decay; fit the curve from real cohorts.
- Do not bury assumptions inside formulas; every input lives on the inputs sheet with a source note.

## Quality bar

Ship only when: the bridge ties to the GL within rounding for every historical period; the driver tree reproduces trailing actual new MRR within 10%; every assumption has a source or an explicit "guess" label; the downside scenario is genuinely uncomfortable (churn doubled, win rate down a third), not base-minus-5%; and a reader can trace ending ARR back to leads without opening a hidden tab.
