---
name: Data Drift Monitor
description: Designs a production drift-monitoring plan for ML systems - statistical tests per feature type, PSI and KS alert thresholds, monitoring cadence, and evidence-based retraining triggers - including a runnable PSI calculator. Use when someone asks "how do I know if my model is drifting", "set up drift monitoring for this model", "should we retrain", or is investigating unexplained model performance degradation. Do NOT use for judging whether a model is good enough to ship in the first place - use model-evaluation-report instead; for tracking training runs use experiment-tracking; for documenting the model for consumers use model-card-writer.
---

# Data Drift Monitor

Models trained on historical data degrade when the world changes. Drift monitoring is the early-warning system that distinguishes a model aging gracefully from one silently failing - and without thresholds tied to actions, it degenerates into dashboards nobody reads and alerts everybody mutes. This skill produces a monitoring plan where every alert has a named owner and a defined next step.

## Operating procedure

Work in order: drift types (Step 2) determine which tests apply (Step 3), and thresholds (Step 5) are meaningless until cadence and coverage (Step 4) define what is measured.

### Step 1: Gather inputs

1. The model's top features by importance (at minimum the top 10) and their types (continuous vs categorical).
2. Prediction volume and frequency - this sets monitoring cadence.
3. Ground-truth label lag: how long after a prediction the true label arrives. If unknown, label the estimate a guess.
4. The primary business metric the model serves and its current 30-day baseline.
5. Retraining cost in engineering time and compute, so triggers can be calibrated against it.

### Step 2: Classify what can drift

Not all drift requires the same response.

- Data drift (covariate shift): input feature distributions change; the model may still work if the feature-label relationship holds.
- Concept drift: the feature-label relationship itself changes; requires retraining regardless of input distributions.
- Label drift: the ground-truth label distribution shifts; matters for classification thresholds.
- Upstream drift: schema changes or pipeline failures masquerading as real drift. Rule this out first on every alert - a column silently going all-null "drifts" spectacularly.

### Step 3: Match statistical tests to feature types

- Continuous features: Population Stability Index (PSI) or Kolmogorov-Smirnov test.
- Categorical features: chi-squared test or Jensen-Shannon divergence.
- PSI interpretation: below 0.1 stable; 0.1 to 0.25 moderate - investigate; above 0.25 significant - act. Teams wanting a conservative action line trigger at 0.2.
- Reference window: use a rolling reference (the last 30 days of training-representative data), not a fixed baseline frozen at training time - seasonality against a stale baseline manufactures false alarms.

### Step 4: Set cadence and coverage

Monitoring everything at the same frequency is wasteful.

- Always monitor the top 10 features by model importance at maximum frequency.
- High-frequency inputs: daily. Low-frequency inputs: weekly.
- Monitor the prediction (score) distribution itself daily - it is the cheapest single drift signal because it aggregates all inputs.
- Concept-drift checks run on the label-lag delay: if labels arrive 14 days late, today's concept-drift verdict describes two weeks ago. Say so on the dashboard.

### Step 5: Define alert thresholds and escalation

Alerts with no action plan create alert fatigue.

- Warning: PSI above 0.1 on any monitored feature, or KS p-value below 0.05 - investigate, do not retrain yet. Check upstream pipeline first.
- Critical: PSI above 0.25 (or 0.2 conservative), or the primary business metric drops more than 5 percent from its 30-day baseline - initiate the retraining evaluation.
- Every alert routes to a named owner. Suppress alerts during known events (marketing pushes, holidays) with documented suppression windows, never silent mutes.

### Step 6: Gate retraining on evidence

Retraining has a cost; trigger it on evidence, not on schedule alone.

- Triggers: a critical drift alert, primary-metric degradation past threshold, or a quarterly review as the minimum floor.
- Before retraining: verify the new training window does not encode the drift as label leakage (for example, an outage period teaching the model that a feature predicts the outage).
- After retraining: shadow-deploy and compare against the incumbent on recent production data before promotion.
- Document every retraining event: trigger reason, data window, metric delta, promotion decision. Feed the run into experiment-tracking.

## Worked artifact: PSI calculator with output

Self-contained Python; edit the two distributions (bucket proportions over the same 10 bins) and run.

```python
import math

def psi(ref, cur, eps=1e-6):
    return sum((max(c, eps) - max(r, eps)) * math.log(max(c, eps) / max(r, eps))
               for r, c in zip(ref, cur))

ref = [0.10] * 10  # reference: deciles of the rolling 30-day window
features = {
    "age_days":      [0.06,0.07,0.08,0.09,0.10,0.10,0.11,0.12,0.13,0.14],
    "session_count": [0.03,0.05,0.06,0.08,0.10,0.11,0.12,0.13,0.15,0.17],
    "spend_30d":     [0.02,0.04,0.05,0.07,0.09,0.11,0.13,0.14,0.16,0.19],
}
for name, cur in features.items():
    v = psi(ref, cur)
    verdict = "stable" if v < 0.1 else "investigate" if v < 0.25 else "ACT"
    print(f"{name:14s} PSI = {v:.3f}  -> {verdict}")
```

Expected output:

```
age_days       PSI = 0.063  -> stable
session_count  PSI = 0.214  -> investigate
spend_30d      PSI = 0.338  -> ACT
```

Read it: age_days is noise. session_count crossed the 0.1 warning line - investigate upstream before touching the model. spend_30d exceeds 0.25 - open the retraining evaluation, starting with the upstream-drift rule-out.

## Deliverable

Produce a monitoring plan containing: the monitored feature list with test and cadence per feature, the warning and critical thresholds with named owners, the reference-window definition, the label-lag statement for concept-drift checks, the retraining trigger criteria and post-retraining shadow-deploy gate, and the retraining event log template.

## Do NOT

- Do not alert on drift without first ruling out upstream schema or pipeline failure - it is the most common cause and the cheapest fix.
- Do not freeze the reference window at training time; seasonality will page you forever.
- Do not retrain on a calendar schedule alone while ignoring evidence in either direction - it wastes compute when stable and reacts too slowly when not.
- Do not treat a warning-level PSI as a retraining trigger; investigation and retraining are different responses with different costs.
- Do not report concept-drift verdicts without stating the label lag; a "healthy" verdict on 14-day-old labels is not a statement about today.

## Quality bar

The plan ships only when: every monitored feature has a test, a cadence, a threshold, and an owner; the two-tier warning/critical distinction maps to two different documented responses; the upstream-drift rule-out is the first step of every alert runbook; and the retraining gate includes both the leakage check and the shadow-deploy comparison.
