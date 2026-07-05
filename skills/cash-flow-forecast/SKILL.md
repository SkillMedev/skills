---
name: Cash Flow Forecast
description: Builds a rolling 13-week and 12-month cash flow forecast with runway view. Use when managing liquidity, planning for fundraising, preparing board materials, or stress-testing the business under downside scenarios.
---

# Cash Flow Forecast

A cash forecast is not a P&L on a different template. It tracks when cash actually moves, not when revenue is recognized or expenses are accrued. Precision on timing is the entire point.

## Two Horizons, Two Approaches

Run two forecasts in parallel. The 13-week forecast is a bottom-up, week-by-week view of actual cash in and out. It is operational. The 12-month forecast is a top-down driver-based model used for planning and runway. They should agree in the overlapping weeks - if they diverge, the 13-week is right and the 12-month needs recalibration.

## 13-Week Forecast Structure

For each week, project: collections from customers (use AR aging plus expected new bookings converted with your average days-to-collect), payroll runs (use the exact pay schedule), vendor payments (use AP aging plus expected new invoices), recurring SaaS and fixed costs (pull from your vendor contracts), and any scheduled debt or tax payments. Sum to net cash change per week, accumulate to ending cash balance. Flag any week where ending cash falls below your minimum operating reserve (typically one to two months of fixed costs).

## 12-Month Forecast Structure

Drive revenue collections from a bookings or ARR model. Drive COGS from a gross margin assumption. Drive opex from a headcount plan (compensation is usually 60 to 75% of opex) plus a non-headcount opex line per department. Capex from the asset plan. Working capital changes from DSO, DPO, and inventory turn assumptions. The 12-month model does not need weekly precision - monthly is sufficient.

## Runway Calculation

Runway equals current cash divided by average monthly net cash burn. Use the trailing three-month average burn, not the worst month or the best month. Show three scenarios: base (current trajectory), upside (20% better collections, 10% lower spend), and downside (20% worse collections, flat spend). Board materials should always show the downside runway. If downside runway is under 12 months, that is a fundraising trigger.

## Working Capital Levers

Before assuming a fundraise, model the working capital levers: accelerate collections (incentivize early payment, tighten credit terms), extend payables (negotiate net-60 with key vendors), reduce inventory (if applicable), or defer non-essential capex. Each lever should show a dollar and week impact on the 13-week view.

## Worked example: forecasting collections

Bad - flat percentage of revenue, no timing:

> "October revenue is $400k; assume 90% collects, so book $360k of October collections."

This ignores when invoices were issued and when customers actually pay, so the weekly cash line is fiction.

Good - aging-based, timed to actual payment behavior:

> Open AR: $250k current, $120k at 31-60 days, $40k at 61-90 days. Historical collection: current pays in the invoice week +4 weeks on average (DSO 34), 31-60 bucket pays 85% over the next 6 weeks, 61-90 bucket pays 60% over 8 weeks. New October bookings of $400k invoice mid-month and follow the same DSO. Spread each bucket across the 13 weeks accordingly: week 1 collects $45k, week 2 $62k, week 3 $58k… and the model flags week 7, where a payroll run plus a quarterly tax payment drop ending cash to $310k - under the $350k minimum reserve - five weeks before it happens.

The good version predicts a specific danger week; the bad version averages it away.

## Deliverable

Produce a forecast workbook (or sheet set) containing: the 13-week tab with weekly collections, payroll, vendor, fixed-cost, and debt/tax lines summing to net change and ending cash, with reserve-breach weeks flagged; the 12-month driver-based tab with its assumptions listed explicitly (gross margin, DSO, DPO, headcount plan); a runway summary showing base, upside, and downside scenarios with the downside number headlined; and a working-capital-levers table quantifying the dollar and week impact of each lever. Every assumption must be a labeled input cell, not a number buried in a formula.

## Quality bar

- The 13-week and 12-month forecasts agree in overlapping weeks, or the divergence is explained.
- Collections are aging-based with documented payment-timing assumptions, never a flat revenue percentage.
- Three scenarios are shown, and downside runway is stated explicitly.
- Any week breaching the minimum operating reserve is flagged with the driver named.
- All assumptions are visible, labeled inputs a reader can change.

## Common Mistakes

Do not use net income as a proxy for cash flow - revenue recognition timing and non-cash charges make this unreliable. Do not forecast collections as a flat percentage of revenue without aging-based validation. Do not show a single-scenario forecast to a board - it signals overconfidence. Do not compute burn from a single month - one good collections month hides the trend; use the trailing three-month average. Do not let the 12-month model override the 13-week view where they overlap - the bottom-up view is the one anchored to real invoices and pay dates.
