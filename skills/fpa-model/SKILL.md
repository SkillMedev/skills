---
name: FP&A Operating Model
description: Builds a driver-based FP&A operating model linking business inputs to P&L, balance sheet, and cash flow outputs. Use when building an annual plan, preparing investor materials, running scenario analysis, or stress-testing the business.
---

# FP&A Operating Model

A driver-based model is built on the belief that financial outputs are the result of measurable business actions. Revenue is not a number pulled from a goal — it is a function of pipeline, conversion rate, average contract value, and sales capacity. Every assumption should be traceable to a business driver, not a wish.

## Model Architecture

Structure the model in four layers. The Assumptions layer holds every input: growth rates, hiring plan, pricing, margins, DSO, and macro variables. The Revenue layer translates demand drivers into recognized revenue. The Expense layer translates the headcount plan and operational drivers into opex and COGS line items. The Output layer assembles the three financial statements automatically from the above. Never hardcode a number in the output layer — it must flow from the assumptions.

## Revenue Build

For SaaS or subscription: start with beginning ARR, add new ARR from new logos (pipeline times close rate times ACV), add expansion ARR, subtract churn ARR (beginning ARR times churn rate), to get ending ARR. Monthly revenue equals ending ARR divided by 12. For transaction businesses: units sold times ASP, by segment. For services: billable headcount times utilization rate times bill rate. Make the revenue build auditable — one row per driver. Sanity-check the drivers against benchmarks: a modeled close rate above 25-30% on cold pipeline, or gross churn assumptions below 5% annually for an SMB product, deserve a written justification because they are outside typical ranges.

## Expense Build

Headcount is the anchor. Build a headcount roster with role, department, start month, fully-loaded cost per head (salary plus benefits plus payroll tax, typically 1.2 to 1.3 times base). Derive compensation expense from the roster. Non-headcount opex should be modeled as either a fixed amount per month or a variable rate tied to a driver (e.g., hosting costs as a percentage of revenue — commonly 8-15% for a SaaS business, and a red flag above 20% — or sales commissions as a percentage of new ARR, typically in the 8-12% range). Avoid modeling opex as a flat year-over-year growth rate — it obscures the underlying drivers. Ramp new hires realistically: a sales hire typically reaches full productivity in month 4-6, not month one.

## Scenario Framework

Build three scenarios into the assumptions layer: Base (most likely), Bull (upside on revenue drivers, flat costs), Bear (revenue miss, cost reduction response). The model outputs should change automatically when the scenario selector changes. The gap between Bull and Bear operating cash flow in month 18 is your planning uncertainty band. If that band is wider than your cash on hand, you need either a larger reserve or a tighter cost structure. In the Bear case, runway dropping below 12 months is the trigger line for action — either a raise or a cost plan — because a fundraise itself consumes 6+ months.

## Model Hygiene

One formula per row — do not mix hardcoded numbers and formulas in the same column range. Color-code inputs (blue) versus calculated cells (black) so any reviewer can spot manual overrides. Date the version and lock the prior actuals columns. A model that is easy to audit will be used; one that is not will be replaced with a spreadsheet in someone's desktop.

## Key Outputs to Surface

Gross margin percentage by period. Operating margin. Headcount by department end of period. Net new ARR (for SaaS). Operating cash flow. Runway in months. These six metrics should appear on a summary dashboard tab that updates from the model automatically.

## Do NOT

- Do not back into revenue from a target ("we need $10M so the model says $10M") — the drivers must produce the number, or the plan is a wish with formatting.
- Do not model opex as last year plus a growth percentage; it hides the headcount and unit-cost decisions the model exists to expose.
- Do not hardcode values in calculated ranges to "fix" a bad output — the override survives the review, poisons every scenario, and is the most common way models silently rot.
- Do not let headcount live outside the roster (a "misc hires" line); every head needs a role, start month, and loaded cost or the expense build is fiction.
- Do not present a single-scenario model to a board or investor; without a Bear case the model cannot answer the only question that matters — what happens if revenue misses.
- Do not update assumptions without re-dating the version and noting what changed; an unlabeled model fork destroys trust in every number downstream.

## Quality bar

- Every output-layer number traces to an assumptions-layer input; zero hardcodes in calculated ranges.
- The three scenarios switch from one selector and the Bear case includes a costed response, not just lower revenue.
- The headcount roster fully explains compensation expense, with loaded costs at 1.2-1.3x base and realistic ramp.
- The six dashboard metrics update automatically and runway is visible in every scenario.
- Drivers outside typical benchmark ranges carry a written justification.
