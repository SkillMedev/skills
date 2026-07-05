---
name: Spreadsheet Model Builder
description: Structures spreadsheet models a second analyst can audit - separated input/calc/output sheets, one-formula-per-row hygiene, built-in error checks, and a documented cover sheet. Use when someone asks "how should I structure this model", "clean up this spreadsheet before the board sees it", "why does my model break when I insert a column", or is building any forecast or calculator others will review. Do NOT use for the SaaS revenue logic itself - use revenue-modeling instead; for a full FP&A operating model use fpa-model; for personal or department budgets use budget-builder; for cash timing use cash-flow-forecast; for GAAP statement construction use financial-statement-builder.
---

# Spreadsheet Model Builder

A spreadsheet model is judged by whether a second analyst can verify it without a guided tour. The costly failure is not a wrong formula - it is a *hidden* wrong formula: a hardcoded number buried in cell F47, an inconsistent row that Ctrl+\ would have caught, a scenario someone ran by overtyping inputs and never restoring. This skill builds models where errors have nowhere to hide.

## Operating procedure

Structure comes before formulas: retrofitting sheet separation onto a finished tangle costs more than the model did.

### Step 1: gather inputs

- What question the model answers and who reviews it. A model only the builder reads can cut corners; a reviewed model cannot.
- The time axis: granularity (monthly/quarterly), horizon, and history vs forecast boundary.
- Every assumption the user already holds, each with a source. Anything unsourced gets written down anyway - labeled a guess in the note column, never silently blended with sourced numbers.
- How many scenarios are needed. Decide now: each scenario is a column on the Inputs sheet, never a separate file.

### Step 2: lay out the sheet structure

Separate sheets with single roles:

- **Inputs** (or Assumptions) - every editable variable lives here, and only here.
- **Calcs** - derived values only; no raw inputs, nothing typed by hand.
- **Output** (or Summary) - the numbers a reader cares about, linked from Calcs.
- **Data** - raw imported or pasted data, never edited by hand.
- **Cover** - what the model does, key assumptions, what changed in the last update.

The iron rule: no hardcoded number inside a formula, anywhere. If a value might ever change, it is an input. `=D12*1.05` is a bug even when 5% is correct today, because nobody will find it when it stops being correct.

### Step 3: build the Inputs sheet

- Group assumptions by theme: revenue, costs, headcount, timing.
- Every row carries a clear label, **units**, and the basis for the number in an adjacent note column ("per signed MSA", "mgmt guess", "trailing 6-mo actual").
- Color-code inputs distinctly (blue text on white is the convention) so an auditor identifies editable cells at a glance without a legend.
- Date the assumption set on the sheet.
- Scenarios (base / upside / downside) are side-by-side columns with one live selector cell; formulas read the selected column via INDEX, so switching scenarios is one edit, and no one ever overtypes the base case.

### Step 4: write formulas to hygiene rules

- **One formula per row, consistent across all columns.** If row 5 computes differently in column D than in column E, something is wrong. Audit with Ctrl+\ (Windows) / Cmd+\ (Mac), which highlights row inconsistencies instantly.
- Nested IFs at most two levels deep; a third level becomes a lookup table or a helper row.
- No INDIRECT - it hides dependencies from the audit trail and breaks refactoring.
- No volatile functions (NOW, RAND, OFFSET, TODAY) in calculation paths - they recalculate on every edit and make "what changed" undetectable.
- INDEX+MATCH (or XLOOKUP) over VLOOKUP - it survives column insertion; VLOOKUP's hardcoded column index is a deferred error.
- Flow left-to-right and top-to-bottom; a formula should reference cells above or to its left, not reach forward.

### Step 5: add an error-check block

At the top of the Output sheet, build explicit checks that read PASS/FAIL: balance items tie out, bridges reconcile, percentages sum to 100%, no negative headcount. Format FAIL in red. A model with zero checks is not conservative - it is unverified.

### Step 6: format for navigation

- Freeze the top row and leftmost label column on every sheet.
- One date format everywhere (YYYY-MM or MMM-YY).
- Text labels left-aligned, numbers right-aligned, column headers centered; consistent column widths within a section.
- Never merge cells in data ranges - merging breaks sorting, filtering, and fill-down formulas.

### Step 7: write the Cover sheet

Three things, before sharing: what the model does, what the key assumptions are, and what changed in the last update. A model without a cover note is incomplete.

## Worked artifact: model skeleton with real numbers

A 12-month subscription forecast, showing the layout (revenue logic itself belongs to revenue-modeling):

```
INPUTS                          Base     Upside   Note
  Starting customers            400      400      billing system, Jan
  New customers / month         30       40       trailing 3-mo avg / stretch
  Monthly churn rate            2.5%     2.0%     trailing 6-mo actual
  ARPA ($/mo)                   90       95       price list / planned increase
  Scenario selector: [Base]              <- one cell drives everything

CALCS (monthly columns Jan..Dec, one formula per row)
  Customers BOP      = prior month EOP                (Jan = starting input)
  New customers      = Inputs new/month
  Churned            = Customers BOP * churn rate
  Customers EOP      = BOP + New - Churned            Jan: 400+30-10 = 420
  MRR                = Customers EOP * ARPA           Jan: 420*90 = $37,800

OUTPUT
  Dec EOP customers: 617        Dec MRR: $55,530      ARR run-rate: $666,360
  CHECKS:  EOP - BOP - New + Churned = 0 each month   -> PASS
           Customer count never negative              -> PASS
```

Every number in CALCS traces to a blue input; changing churn to 3.0% requires editing exactly one cell.

## Deliverable

Produce a workbook with the five-sheet structure, a scenario-column Inputs sheet with units and source notes on every assumption, a Calcs section passing the Ctrl+\ consistency audit, an error-check block reading PASS on all checks, and a dated Cover sheet stating purpose, key assumptions, and last change.

## Do NOT

- Do not hardcode any number in a formula - the model dies by a thousand buried constants.
- Do not run scenarios by overtyping inputs; the base case never comes back intact.
- Do not use VLOOKUP with a literal column index, INDIRECT, or volatile functions in calculation paths.
- Do not merge cells in data ranges or vary a row's formula midway across columns.
- Do not ship without an error-check block; "it looked right" is not verification.
- Do not mix sourced numbers and guesses without labeling - the note column exists so reviewers can weight each assumption.

## Quality bar

The model ships only when: every editable cell is blue and lives on Inputs; Ctrl+\ finds zero inconsistent rows; every check in the error block reads PASS; every assumption carries units and a source or "guess" label; scenarios switch from one selector cell; and a second analyst can trace any Output number to its inputs in under a minute without asking the builder anything.
