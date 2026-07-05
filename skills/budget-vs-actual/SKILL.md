---
name: Budget vs. Actual Variance Analysis
description: Structures a budget-vs-actual variance analysis that isolates root causes — price/volume/mix decomposition, timing vs structural expense buckets, a materiality screen, and reforecast flags — instead of restating numbers. Use when someone asks "why did we miss budget", "write the variance commentary for the board deck", "explain this expense overrun", or is closing the month and owes narrative on the P&L. Do NOT use to build the budget or plan itself — use budget-builder instead; do NOT use for a full driver-based forecast model — use fpa-model instead; do NOT use for cash timing and runway questions — use cash-flow-forecast instead.
---

# Budget vs. Actual Variance Analysis

Variance analysis is diagnostic, not descriptive. A report that says revenue was 8% below budget is not analysis. Analysis says why, which team or product drove it, whether it is recoverable, and what it means for the full-year forecast. The costly failure is a wall of explanations for immaterial noise that buries the one structural miss the leadership team needed to act on.

## Operating procedure

### Step 1: Gather inputs

1. The actuals and budget at the same level of account granularity, for the period and YTD.
2. Total monthly revenue budget (it sets the materiality floor).
3. For revenue lines: budgeted and actual units and price by segment, if available. If not, note that decomposition will be directional and label it as such.
4. Known one-offs and timing shifts already identified by the accounting close (pair with month-end-close for the close itself).
5. The audience: internal ops review or board deck. Default: board-ready.

### Step 2: Set up the right columns

The base table has five columns: Actual, Budget, Variance (Actual minus Budget), Variance Percent, and a one-line explanation. Add a YTD Actual and YTD Budget pair if the review is mid-year. Do not add a Prior Year column to the variance table — that belongs in a separate trend view. Mixing budget variance and year-over-year in one table confuses the reader.

### Step 3: Apply the materiality threshold before writing

Define a materiality floor before writing any explanations. The default is the greater of 5% variance or a fixed dollar amount tied to total budget — 0.5% of the monthly revenue budget is a sound default. Variances below the threshold get no narrative; mark them "—". This discipline keeps the report focused on decisions, not accounting noise.

### Step 4: Decompose revenue variances first

Revenue variances decompose into price, volume, and mix:

- Volume variance = (actual units − budgeted units) × budgeted price.
- Price variance = (actual price − budgeted price) × actual units.
- Mix variance = the residual when segment composition differs from plan.

Identify which driver is largest before writing the explanation. A volume miss and a price miss have different remedies — one is a pipeline problem, the other is a discounting problem.

### Step 5: Bucket expense variances

Group each material expense variance into one of three buckets:

- **Timing**: the spend happened, just not in this period. Resolves itself; needs a one-line note.
- **Volume-driven**: spend moved with a revenue or headcount driver. Needs a rate check — is cost per unit in line?
- **Structural**: the cost base differs from plan regardless of volume. Requires a decision.

### Step 6: Write each explanation in one sentence

Format: what happened, why it happened, and whether it is expected to continue. Example: "Revenue was 120k below budget due to a three-week delay in a contracted implementation project; the project began in the following period and the shortfall is not expected to recur." Avoid passive voice and avoid blaming external factors without evidence.

### Step 7: Flag reforecast lines

After completing the table, flag any line where the YTD variance is structural and requires a full-year reforecast. Do not carry a known miss forward in the budget without an updated number. A budget that no longer reflects reality is not a management tool.

## Worked example

Monthly revenue budget $2,000k → materiality floor = greater of 5% or $10k (0.5% × $2,000k).

| Line | Actual | Budget | Var $ | Var % | Explanation |
|---|---|---|---|---|---|
| Revenue | $1,880k | $2,000k | ($120k) | (6.0%) | Volume: 376 units closed vs 400 planned at plan price of $5.0k; two enterprise deals slipped to next month with signed orders in hand. Timing, expected to recover in-quarter. |
| COGS | $648k | $620k | $28k | 4.5% | Volume-driven; gross margin 65.5% vs 69.0% plan — rate check shows a one-time hosting true-up of $22k. Not recurring. |
| S&M | $410k | $380k | $30k | 7.9% | Timing: annual conference sponsorship paid this month vs next in plan. Nets to zero YTD. |
| R&D | $505k | $540k | ($35k) | (6.5%) | Structural: three open backend reqs unfilled for a second consecutive month. Flagged for reforecast — full-year R&D will underrun ~$140k unless hiring recovers. |
| G&A | $182k | $175k | $7k | 4.0% | — (below materiality floor) |

Revenue decomposition: volume variance = (376 − 400) × $5.0k = **($120k)**; price variance = ($5.0k − $5.0k) × 376 = $0; mix immaterial. The entire miss is volume/timing, not pricing — no discounting action needed.

Reforecast flags: R&D (structural underrun). Revenue slip is timing, not reforecast.

## Deliverable

Produce the five-column variance table (plus YTD pair if mid-year) with one-sentence explanations only on lines above the stated materiality floor, the price/volume/mix decomposition for any material revenue variance, a timing/volume/structural tag on every material expense variance, and a closing list of reforecast flags with the estimated full-year impact of each.

## Do NOT

- Do NOT narrate immaterial variances — every explanation below the floor dilutes attention from the ones that matter.
- Do NOT mix prior-year comparison into the budget variance table; they answer different questions.
- Do NOT report a revenue miss without decomposing it — "revenue down 6%" prescribes nothing; "volume down 6%, price flat" does.
- Do NOT label a variance "timing" without naming the period it lands in; unverifiable timing claims are how misses get hidden.
- Do NOT let a structurally-missed line ride the original budget; flag it for reforecast with a number.

## Quality bar

A finished analysis passes only when: the materiality floor is stated up front and applied consistently; every material line has exactly one sentence in the what/why/continues format; every material revenue variance shows the price/volume/mix math; every material expense variance carries a timing/volume/structural tag; and the reforecast section either lists flagged lines with full-year impact or explicitly states there are none.

## Escalation

This is management reporting structure, not accounting or audit guidance. Route questions of revenue recognition, accrual correctness, or restatement to the controller or external accountant; the close mechanics themselves belong to month-end-close, and rebuilding the plan belongs to budget-builder or fpa-model.
