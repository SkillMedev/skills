---
name: Financial Statement Builder
description: Builds and interprets the income statement, balance sheet, and cash flow statement as one linked system — construction order, the three mechanical ties between statements, and the review checks that catch errors. Use when someone asks "build me a three-statement model", "why doesn't my balance sheet balance", "how does net income flow into the cash flow statement", "check this financial package for errors", or is explaining statements to stakeholders. Do NOT use for spreadsheet layout and formula hygiene — use spreadsheet-model-builder instead — for forward-looking 13-week or 12-month cash projections — use cash-flow-forecast instead — or for the monthly process that produces the trial balance — use month-end-close instead.
---

# Financial Statement Builder

The three core statements are one system, not three documents: an error in one surfaces in another, and a package whose statements do not tie is wrong somewhere even if each statement looks plausible alone. The costly mistake this skill prevents is shipping a package where the balance sheet is forced to balance with a plug — the plug hides a real error that compounds every period until someone has to unwind months of statements.

## Operating procedure

### Step 1: Gather inputs

1. The trial balance or account-level data for the period (if the books are not closed, run month-end-close first).
2. Opening balance sheet — the prior period's closing balance sheet. Without it, the cash flow statement and retained earnings cannot tie; if it does not exist, state that the first period's linkages cannot be verified.
3. Accounting basis (accrual assumed; flag if cash-basis) and the effective tax rate.
4. Dividends or distributions declared in the period.
5. Any non-cash items: depreciation, amortization, stock-based compensation.

Label any estimated figure (tax rate, accrual) as an estimate in the package notes.

### Step 2: Build in construction order — always

Income statement first, then balance sheet, then cash flow statement. The reason is mechanical: net income from the income statement flows into retained earnings on the balance sheet and into the top of the indirect cash flow statement. Building out of order breaks the linkages and forces reconciliation loops.

### Step 3: Income statement

Start with revenue recognized in the period — not cash received. Deduct cost of goods sold (or cost of revenue) for gross profit. Deduct operating expenses by natural category — compensation, software, marketing, facilities, D&A — for operating income (EBIT). Deduct interest expense, add interest income, for pre-tax income. Apply the effective tax rate for net income. Compute gross margin percent and operating margin percent on every draft as sanity checks; a margin that jumps 10+ points period-over-period without a known cause is a classification error until proven otherwise.

### Step 4: Balance sheet

Assets equal liabilities plus equity — always, to the dollar, with no plug. Build assets top to bottom: current (cash, AR, inventory, prepaid), then non-current (PP&E net of accumulated depreciation, intangibles, investments). Build liabilities top to bottom: current (AP, accrued liabilities, deferred revenue, current portion of debt), then non-current (long-term debt, deferred tax). Retained earnings closes the loop: prior retained earnings plus net income minus dividends equals current retained earnings.

### Step 5: Cash flow statement (indirect method)

Start with net income. Add back non-cash charges: depreciation, amortization, stock-based compensation. Adjust for working capital changes — an increase in AR is a use of cash, an increase in AP is a source. That section is operating cash flow. Investing activities capture capex and acquisitions. Financing activities capture debt draws and repayments, equity issuances, and dividends. Ending cash must equal the cash line on the closing balance sheet.

### Step 6: Verify the three links before shipping

1. Net income on the income statement equals the net income line at the top of the cash flow statement.
2. The change in cash on the cash flow statement equals the change in cash between opening and closing balance sheets.
3. Closing retained earnings equals opening retained earnings plus net income minus dividends declared.

If any link fails, the package does not ship. Find the break; do not plug it.

## Worked artifact: how the three statements tie

One period, small SaaS company, all figures in $000s:

```
INCOME STATEMENT
  Revenue                          500
  COGS                            (150)
  Gross profit                     350   (70% gross margin)
  Opex (incl. D&A of 20, SBC 10)  (300)
  Operating income                  50
  Interest expense                  (5)
  Pre-tax income                    45
  Tax @ 22%                        (10)
  NET INCOME                        35  ──────────────┐
                                                      │
CASH FLOW STATEMENT (indirect)                        │
  Net income                        35  ◄─── link 1 ──┘
  + Depreciation & amortization     20
  + Stock-based compensation        10
  − Increase in AR                 (25)
  + Increase in AP                   8
  Operating cash flow               48
  Capex                            (30)
  Debt repayment                   (10)
  NET CHANGE IN CASH                 8  ──────────────┐
                                                      │
BALANCE SHEET            OPENING   CLOSING            │
  Cash                       100       108  ◄ link 2 ─┘ (100 + 8)
  AR                          60        85    (+25, matches CF)
  PP&E net                   200       210    (+30 capex − 20 D&A)
  Total assets               ...       ...
  AP                          40        48    (+8, matches CF)
  Debt                       100        90    (−10, matches CF)
  Retained earnings          150       185  ◄ link 3 (150 + 35 − 0 div)
  APIC/SBC reserve            ..       +10    (SBC lands in equity)
  Assets = Liabilities + Equity: holds, no plug
```

Read it: net income of 35 appears in all three statements — as the P&L bottom line (link 1), the top of operating cash flow, and the retained-earnings build (link 3). Cash walks 100 to 108 exactly by the cash flow statement's net change (link 2). Every working-capital delta on the cash flow statement matches the balance-sheet movement it came from.

**Bad vs. good when the sheet is off by 10:**

- Bad: add a "Other liabilities — reconciling item: 10" line so assets equal liabilities plus equity. The package ships, the 10 was actually a missed accrual, and next quarter the plug is 24 and nobody remembers why.
- Good: hold the package, difference the closing sheet against the cash flow statement line by line, find that AP increased 8 on the balance sheet but 18 was recorded on the cash flow statement — a transposition. Fix the source entry; the sheet balances with no plug.

## Review thresholds

Variance floors for reviewing a period before it ships — investigate anything that trips one; explain or fix before delivery:

- Any balance-sheet account that moves more than the greater of 5% and a fixed dollar floor period-over-period (set the floor to roughly 0.5-1% of total assets for a small company) gets a one-line explanation in the notes.
- Gross or operating margin moving 10+ points without a known cause is a classification error until proven otherwise.
- AR growing more than ~1.5x the revenue growth rate for two consecutive periods, or days sales outstanding rising more than ~15%, means collections or revenue-recognition timing needs a look before the package ships.
- The three links tie to the dollar. There is no "close enough" tolerance — a $1 difference is a real error somewhere, just a small one today.

## Red flags on review

- Balance sheet does not balance: find the first period where it broke and trace backward from there — the error is in that period, not the latest one.
- Operating cash flow negative while net income is positive for two or more consecutive periods: check revenue recognition timing and AR growth; if AR is growing meaningfully faster than revenue, the company may be booking revenue it is not collecting.
- Financing is the largest positive cash flow line every period: the business is not self-funding — a strategic flag, not an accounting error, and worth saying out loud in the package notes.
- Depreciation on the cash flow statement does not match the D&A embedded in the income statement, or SBC is added back on the cash flow statement but never expensed on the P&L.

## Deliverable

Produce the three-statement package: income statement with margin percentages, classified balance sheet that balances without a plug, indirect-method cash flow statement, a linkage check showing all three ties verified with the actual figures, and notes flagging estimates and any red flags found.

## Do NOT

- Do not force the balance sheet with a plug line — it converts a findable error into a hidden one that compounds.
- Do not build the statements out of order; the downstream statements consume the upstream ones.
- Do not confuse revenue with cash received or expenses with cash paid; the accrual/cash distinction is the entire reason the cash flow statement exists.
- Do not get the working-capital signs backward: AR up is cash out, AP up is cash in.
- Do not skip the opening balance sheet; without it, links 2 and 3 are unverifiable and the package is unaudited by construction.

## Quality bar

- All three links verified numerically and shown in the package, not asserted.
- Balance sheet balances to the dollar with no plug, every period presented.
- Gross and operating margins computed and sane vs. prior period, or the variance is explained.
- Non-cash add-backs on the cash flow statement each trace to a P&L or equity line.
- Estimates are labeled as estimates.

For spreadsheet architecture — assumptions sheets, formula hygiene, auditability — pair with spreadsheet-model-builder. For projecting cash forward rather than reporting it, use cash-flow-forecast. For the close process that produces clean inputs, use month-end-close.
