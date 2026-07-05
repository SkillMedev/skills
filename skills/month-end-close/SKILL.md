---
name: Month-End Close
description: Guides finance teams through a controlled month-end close - sub-ledger cutoffs, journal entries in dependency order, full balance-sheet reconciliation, flux analysis, and sign-off - targeting a locked close by business day 5-10. Use when someone asks "help me close the books", "build a close checklist", "our close takes three weeks, how do we shorten it", "what order do the journal entries go in", or is preparing for an audit. Do NOT use for constructing or interpreting the financial statements themselves - use financial-statement-builder instead - or for writing the budget-variance narrative that follows the close - use budget-vs-actual instead.
---

# Month-End Close

A fast, clean close is the result of consistent preparation, not heroics at deadline. The failure this skill prevents is the restatement: a late invoice posted after reconciliation, an unreconciled variance carried forward "to fix next month," a flux swing nobody explained that turns out to be a misposted entry. The sequence below has hard checkpoints so nothing slips and the books reflect economic reality.

Target: sign-off and lock by business day 5 for a well-run close; day 10 is the outer acceptable bound. A close consistently past day 10 means the pre-close phase is being skipped - fix the calendar, not the deadline.

## Operating procedure

The order is dependency order, and it is not negotiable: journals before sub-ledger lock, lock before reconciliation, reconciliation before flux, flux before sign-off. Reconciling before the lock guarantees rework; running flux before reconciliation means explaining errors instead of catching them.

### Step 1: Gather inputs (once, when adopting this process)

1. Chart of accounts and entity structure (any intercompany?).
2. Sub-ledger systems for AP, AR, payroll, inventory, and who owns each cutoff.
3. Materiality threshold for reconciliation variances and flux explanations - a common working floor is the greater of 10% line variance or a fixed dollar amount scaled to the business (for example 0.5% of monthly revenue). If none exists, set one now and label it a first guess to be calibrated after two closes.
4. Recurring journal list: depreciation, prepaid amortization, deferred revenue release, payroll accruals.
5. The reporting deadline the close feeds (board package, lender covenant, audit).

### Step 2: Pre-close, days -3 to -1

- Confirm sub-ledger cutoffs with AP, AR, payroll, and inventory owners.
- Distribute the close calendar with a hard deadline per team per task.
- Pre-build recurring journal entry templates so Day 1 is execution, not drafting.
- Flag unusual transactions from the period for senior review now, not at flux time.

### Step 3: Day 1 - journals, then sub-ledger lock

Post recurring and standard journals first, in this order: payroll accruals, then depreciation, then prepaid releases, then deferred revenue, then one-time items. Every entry carries a preparer, a reviewer, and a source-document reference - no exceptions, this is the audit trail. Then lock the sub-ledgers (AP, AR, inventory) before any G/L reconciliation begins. Accruals are estimated from actuals - vendor invoices in transit, payroll days actually worked - never rounded guesses.

### Step 4: Days 2-3 - reconcile every balance-sheet account

Every account, not just cash. Priority order: bank accounts (tie to statement), intercompany balances (must zero-sum across entities), AR aging to G/L control, AP aging to G/L control, fixed-asset roll-forward, prepaid schedule, accrued-liabilities schedule. Any variance over the materiality threshold gets explained and corrected same-day before the close proceeds. Carrying a variance forward with a note is prohibited - that note becomes next month's mystery.

### Step 5: Days 3-4 - flux analysis

Run a P&L flux: each line vs. prior month and vs. prior year same month. Any variance above 10% or the materiality floor gets a one-line explanation. This is error detection, not a memo exercise - unexplained swings almost always indicate a misposted entry or a missed accrual. If an explanation cannot be written, the number is wrong; go find the entry.

### Step 6: Day 5 - sign-off and lock

Controller or CFO reviews the flux pack and the reconciliation sign-off sheet. Lock the period. After lock, no adjustment without a documented correcting entry in the next open period. Post the trial balance to the reporting tool and distribute the close package with a summary of significant items.

## Worked artifact: close checklist ordered by dependency

```
MONTH-END CLOSE CHECKLIST - [FILL: entity] - [FILL: period]
Materiality floor: greater of 10% / $[FILL]

PRE-CLOSE (days -3 to -1)
[ ] AP cutoff confirmed          owner: [FILL]   due: day -2
[ ] AR / billing cutoff confirmed owner: [FILL]  due: day -2
[ ] Payroll cutoff confirmed     owner: [FILL]   due: day -2
[ ] Inventory count/cutoff       owner: [FILL]   due: day -1
[ ] Recurring JE templates ready owner: [FILL]   due: day -1
[ ] Unusual transactions flagged owner: [FILL]   due: day -1

DAY 1 - JOURNALS THEN LOCK (order is mandatory)
[ ] 1. Payroll accruals    prep: [FILL]  review: [FILL]  src doc: [FILL]
[ ] 2. Depreciation        prep: [FILL]  review: [FILL]  src doc: [FILL]
[ ] 3. Prepaid releases    prep: [FILL]  review: [FILL]  src doc: [FILL]
[ ] 4. Deferred rev release prep: [FILL] review: [FILL]  src doc: [FILL]
[ ] 5. One-time items      prep: [FILL]  review: [FILL]  src doc: [FILL]
[ ] SUB-LEDGERS LOCKED (AP/AR/inventory) - gate for Day 2

DAYS 2-3 - RECONCILIATIONS (all balance-sheet accounts)
[ ] Bank accounts tie to statements
[ ] Intercompany zero-sums across entities
[ ] AR aging = G/L control
[ ] AP aging = G/L control
[ ] Fixed-asset roll-forward
[ ] Prepaid schedule
[ ] Accrued-liabilities schedule
    Variances > floor: explained AND corrected same-day. None carried.

DAYS 3-4 - FLUX
[ ] P&L flux vs prior month and prior-year month
[ ] One-line explanation for every line > 10% or floor

DAY 5 - SIGN-OFF
[ ] Controller/CFO review of flux pack + recon sign-off sheet
[ ] Period locked; trial balance posted; close package distributed
```

## Deliverable

Produce the filled close checklist above with named owners and dates, the journal-entry log (entry, preparer, reviewer, source reference), the reconciliation sign-off sheet, and the flux pack with explanations for every material variance - the set a controller signs and an auditor can walk without asking where anything came from.

## Do NOT

- Do not skip the sub-ledger lock - late invoices posted after reconciliation are the leading cause of restatements.
- Do not carry unreconciled variances forward with a note to fix next month; require same-day correction.
- Do not book accruals as rounded guesses when actuals (invoices in transit, days worked) are obtainable.
- Do not post journals out of order; deferred revenue and prepaid releases depend on balances the earlier entries establish.
- Do not treat flux as a formality to be worded nicely - an inexplicable variance is an error you have not found yet.
- Do not adjust a locked period silently; every post-lock change is a documented correcting entry.

## Quality bar

- Every journal entry has preparer, reviewer, and source document - spot-check five at random and all five pass.
- Every balance-sheet account is reconciled, with zero variances above the floor carried forward.
- Every flux line above threshold has a specific explanation naming the driver, not "timing."
- Close locked by day 5, or by day 10 with a written reason and a fix for next month.
- The package is reproducible: a new hire could rerun the close from the checklist alone.

The close produces the trial balance; assembling and interpreting the statement package from it is financial-statement-builder, and the variance narrative for management is budget-vs-actual.
