---
name: Model Evaluation Report
description: Produces a rigorous, honest evaluation report for an ML model - real baselines, business-matched metrics with confidence intervals, slice-level error analysis, calibration checks, and a go/no-go recommendation. Use when someone asks "is this model good enough to ship", "write up the eval for this model", "did we actually beat the old model", or before any model promotion, stakeholder demo, or retraining decision. Do NOT use for evaluating LLM or prompt outputs - use llm-evaluation instead; for analyzing randomized product experiments use ab-test-analyzer; for documenting a shipped model for consumers use model-card-writer.
---

# Model Evaluation Report

A model that beats a naive baseline on aggregate accuracy can still be worse than useless in production. Honest evaluation means choosing the right metric before looking at results, comparing against baselines that actually matter, and finding the slices where the model fails - because those slices are where the incident reports will come from.

## Operating procedure

Order matters: the primary metric and baselines are chosen before results are examined, or the report is p-hacked by construction.

### Step 1: Gather inputs

Collect from the requester before computing anything; label unknowns as guesses.

1. Task type: classification, regression, or ranking.
2. The business decision the model drives, and the asymmetry of errors (is a false positive or a false negative more expensive, and roughly by how much).
3. The evaluation dataset: source, size, and confirmation it is disjoint from training data and from any data used for model selection.
4. What the model replaces: prior production model, rule-based system, or nothing.
5. Class balance (classification) or target distribution (regression).

### Step 2: Fix the primary metric before looking at results

Accuracy is rarely the right primary metric. Match the metric to the problem:

- Imbalanced classification (minority class under ~20 percent): F1, precision-recall AUC, or Matthews Correlation Coefficient. ROC AUC flatters imbalanced problems because true negatives are cheap.
- Cost-asymmetric classification: precision at a fixed recall (or vice versa), pinned to the operating point the business will actually use.
- Ranking: NDCG or MAP, never accuracy.
- Regression: report both MAE and RMSE. An RMSE-to-MAE ratio above roughly 1.5 signals outlier sensitivity that MAE alone hides.

State exactly one primary metric in writing before results are computed. Post-hoc metric selection is p-hacking.

### Step 3: Establish baselines

No model result is meaningful without a comparison point. Report all of these in the same table as the candidate:

- Floor: majority-class classifier (classification) or mean predictor (regression).
- Real bar: the prior production model or rule-based system it replaces.
- Human-level performance, when measurable.

A candidate that beats the floor but not the incumbent is a no-go regardless of how impressive the absolute number looks.

### Step 4: Quantify uncertainty

- Bootstrap confidence intervals (minimum 1000 resamples) for every primary-metric value, candidate and baselines alike.
- For candidate-vs-incumbent comparisons, report the p-value and the effect size, not just "significant".
- Flag any evaluation set under 500 samples as underpowered; the report may still ship, but the go/no-go must say the estimate is unstable.

### Step 5: Run slice-level error analysis

Aggregate metrics hide the groups where the model fails.

- Segment performance by every categorical feature, by buckets of key numeric features, and over time if the data spans it.
- Flag any slice performing more than 10 percentage points below the overall metric.
- Report slice sample sizes - a slice with 12 examples is noise, not a finding.
- Equity audit: check performance parity across demographic-correlated features whenever the data permits.

### Step 6: Check calibration (probabilistic classifiers)

- Plot a reliability diagram and report Expected Calibration Error (ECE).
- A well-discriminating but poorly calibrated model needs Platt scaling or isotonic regression before deployment - its probabilities will otherwise be misread as confidence.

### Step 7: Write the report

The report is the artifact, not the notebook. Structure: one-page executive summary (task, primary metric, baseline, result, recommendation), the full metric table with confidence intervals, the slice analysis table with flagged underperformers, known failure modes with mitigations, and a go/no-go recommendation citing explicit criteria set in Steps 2-3.

## Worked artifact: executive summary and metric table

```
EVALUATION: churn-classifier v3        DECISION: retention-offer targeting
PRIMARY METRIC (fixed before results): PR-AUC on 30-day holdout (n = 8,412)

Model                       PR-AUC (95% CI)        Recall @ P=0.60
Majority class (floor)      0.11  (-)              0.00
Rules v1 (incumbent)        0.34  (0.31-0.37)      0.22
Candidate (GBT v3)          0.47  (0.44-0.50)      0.41

SLICES FLAGGED (>10pp below overall):
  tenure < 30 days     PR-AUC 0.29   n = 1,050   → known cold-start gap
  plan = enterprise    PR-AUC 0.35   n = 96      → sample too small to act on

CALIBRATION: ECE 0.031 after isotonic regression (0.09 raw - do not deploy raw scores)
RECOMMENDATION: GO, gated on excluding tenure < 30 days from automated offers
  until the cold-start slice reaches parity or a fallback rule covers it.
```

Bad recommendation: "Model achieves 91% accuracy, ship it." (Accuracy on an 11-percent-positive dataset; the floor is 89 percent.)
Good recommendation: the block above - primary metric fixed in advance, incumbent beaten with non-overlapping intervals, failing slice named with a mitigation.

## Deliverable

Produce an evaluation report containing: the pre-registered primary metric and rationale, a baseline-inclusive metric table with bootstrap confidence intervals, a slice analysis table with flagged slices and sample sizes, calibration results for probabilistic models, known failure modes with mitigations, and a go/no-go recommendation tied to the criteria stated before results were seen.

## Do NOT

- Do not select or switch the primary metric after seeing results - that converts evaluation into leaderboard gaming.
- Do not report accuracy as primary on imbalanced data; the majority-class floor makes it meaningless.
- Do not compare against the floor baseline only; the incumbent is the bar that decides shipping.
- Do not present point estimates without intervals; two models 0.5 points apart with overlapping CIs are tied.
- Do not average away failing slices - a model 10 points worse on a protected or high-value group is a launch blocker, not a footnote.
- Do not evaluate on data the model or its hyperparameters ever touched.

## Quality bar

The report ships only when: the primary metric was documented before results; every table row carries a confidence interval and a sample size; every flagged slice has either a mitigation or an explicit accepted-risk statement; calibration is reported for any model whose scores will be read as probabilities; and the recommendation cites the pre-set criteria rather than adjectives.

Route neighbors correctly: prompt and LLM output quality belongs to llm-evaluation, live randomized experiments to ab-test-analyzer, and the consumer-facing documentation of the shipped model to model-card-writer.
