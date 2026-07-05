---
name: EDA Playbook
description: Runs a structured exploratory data analysis on a new or suspect dataset - schema audit, target analysis, feature profiling, missingness patterns, and leakage checks - ending in a written decision log. Use when someone says "I just got this dataset, where do I start", "my model metrics look too good", "audit this data before we model it", or is debugging unexpected model behavior. Do NOT use for writing the transformation code itself - use pandas-expert instead; for ongoing production data monitoring use data-quality; for constructing model features after EDA use ml-feature-engineering; for answering a one-off business question from a database use sql-to-insights.
---

# EDA Playbook

Skipping EDA before modeling is the single most common source of silent failures in ML pipelines: leakage that makes offline metrics fictional, structural missingness treated as random, a target definition nobody actually agreed on. This playbook is a fixed-order audit that surfaces those problems while they cost hours instead of quarters - and its output is a decision log, not a pile of plots.

## Operating procedure

Run the sections in order: schema problems invalidate the target analysis, and the target definition must be pinned before any feature-target relationship means anything.

### Step 1: gather inputs

- The dataset, its supposed grain (one row = ?), and the expected row count. If the provider cannot state either, derive them and label the result a guess.
- The target column and its **written definition** - precisely what event, over what window, measured when. Ambiguity here cascades everywhere; do not proceed on a verbal shrug.
- What information will exist **at prediction time**. This single fact powers every leakage check later.
- Any known collection changes (new form fields, pipeline migrations) that could create structural breaks.

### Step 2: shape and schema audit

- Confirm row count is in the expected range; flag if off by orders of magnitude.
- Check every column dtype; coerce-or-drop mismatches before anything downstream (hand the coercion work to pandas-expert).
- List every column's null rate. Threshold: any column above 20% null gets an explicit handling decision in the log - no default treatment.

A compact audit that produces the whole table at once:

```python
audit = pd.DataFrame({
    "dtype": df.dtypes.astype(str),
    "null_pct": (df.isna().mean() * 100).round(1),
    "nunique": df.nunique(),
    "modal_pct": (df.apply(lambda c: c.value_counts(normalize=True).iloc[0]) * 100).round(1),
})
print(audit.to_string())
```

Example output on a 5,000-row churn extract:

```
                    dtype  null_pct  nunique  modal_pct
age               float64       0.0       62        2.1
plan                  str       0.0        3       84.7
last_invoice_amt  float64      30.5     3475        0.0
acct_flag         float64       0.0        1      100.0
churned             int64       0.0        2       92.4
```

Read it: `last_invoice_amt` at 30.5% null crosses the 20% line - decision required; `acct_flag` is a single constant value - near-zero variance, flag it; `plan` is 84.7% one value - usable but weak.

### Step 3: target variable analysis

- Classification: class frequencies and the imbalance ratio. Above roughly 10:1, record now that accuracy is a meaningless headline metric and stratified splits are mandatory.
- Regression: plot the full distribution; compute skew and kurtosis; heavy right skew (skewness above ~2) puts a log/transform decision in the log.
- Identify implausible or out-of-range label values (negative durations, ages above 120) and trace them to source before excluding.
- Write the target definition into the log verbatim.

### Step 4: univariate feature profiling

Profile independently before studying interactions:

- Numeric: min, max, mean, median, std, and the 1st/99th percentiles - the percentile gap versus min/max exposes outliers the std hides.
- Categorical: cardinality, top-5 values, modal frequency.
- Flag near-zero-variance features: std/mean below 0.01 for numerics; a single value covering more than 98% for categoricals.
- Do NOT drop anything yet - document and defer. Dropping during profiling destroys evidence the missingness analysis needs.

### Step 5: missingness patterns

Random and structural missingness require different treatment, so diagnose before imputing:

- Compute a missingness correlation matrix (`df.isna().corr()`). Columns missing **together** (correlation near 1.0) indicate a pipeline branch or a form version - investigate the source, do not impute across the break.
- Test whether missingness correlates with the target; if it does, the missing indicator is itself a feature and MNAR is likely.
- MCAR vs MNAR usually cannot be proven - record the determination as an explicit assumption in the log when unverifiable.

### Step 6: bivariate and leakage checks

Correlation with the target is useful; near-perfect correlation is an alarm:

- Any feature with Pearson or Spearman |r| above 0.95 against the target gets a leakage investigation before celebration - it is almost always a post-outcome column (refund amount predicting churn) or a target transform.
- Every timestamp-derived feature must be verified against the prediction-time inventory from Step 1: computed only from information available when the prediction is made.
- Identifiers (IDs, keys, hashes) are excluded from modeling features unless deliberately encoded - high-cardinality IDs memorize rows.
- Check duplicate rows and near-duplicates across a would-be train/test boundary; duplicates spanning the split inflate metrics.

### Step 7: write the decision log

EDA output is only valuable if it drives decisions. For each flagged issue record: the finding, the decision (drop / impute / transform / flag / investigate upstream), and the rationale - not just the outcome. Treat the log as living; update it when modeling reveals new issues.

## Worked artifact: decision log template

```
EDA DECISION LOG - [FILL: dataset] - [FILL: analyst]
Grain: one row = [FILL]        Rows: [FILL] (expected: [FILL])
Target: [FILL: verbatim definition, window, measurement time]
Prediction-time inventory: [FILL: columns known at prediction time]

| # | Finding                              | Decision            | Rationale                        |
|---|--------------------------------------|---------------------|----------------------------------|
| 1 | last_invoice_amt 30.5% null,         | Impute 0 + missing  | Nulls = never invoiced (free     |
|   | missing only where plan = free       | indicator column    | plan); structural, not random    |
| 2 | acct_flag constant (1 value)         | Drop                | Zero variance, no signal         |
| 3 | refund_amt r = 0.97 with churn       | Drop - LEAKAGE      | Populated after churn event      |
| 4 | churned imbalance 12:1               | Stratify; use PR-AUC| Accuracy meaningless at 12:1     |
```

## Deliverable

Produce a written EDA summary containing: dataset health at a glance (rows, grain, schema surprises), the verbatim target definition, the top risks ranked, and the decision log with one row per flagged issue - finding, decision, rationale. This document, not the notebook, is what the modeling work builds on.

## Do NOT

- Do not start modeling with the EDA "mostly done" - the leakage check is the part that invalidates results, and it is always the part skipped.
- Do not impute before diagnosing missingness structure; imputing across a pipeline branch fabricates data.
- Do not drop features during profiling; defer every drop to the decision log.
- Do not accept a target definition by conversation; write it down and get it confirmed.
- Do not treat |r| > 0.95 with the target as a great feature; treat it as leakage until proven otherwise.

## Quality bar

The EDA ships only when: every column appears in the audit table; every issue crossing a threshold (20% null, 98% modal, |r| > 0.95, 10:1 imbalance) has a logged decision with rationale; the target definition is written and confirmed; every timestamp feature is cleared against the prediction-time inventory; and a second analyst could act on the log without opening the notebook.
