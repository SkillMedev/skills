---
name: Feature Engineering
description: Designs ML features with leakage-safe pipelines, correct categorical encoding by cardinality, numeric transforms, and validation that a feature earns its place. Use when someone asks "how should I encode this high-cardinality column", "why does my model score great offline and fail in production", "what features should I build from this table", or "should I scale these inputs". Do NOT use for building the serving infrastructure that stores and versions features across models — use feature-store-design instead; for detecting when live feature distributions shift after deployment — use data-drift-monitor instead; for initial dataset exploration and profiling — use eda-playbook instead.
---

# Feature Engineering

Most "amazing offline, useless in production" models are not modeling failures — they are leakage failures introduced during feature engineering. This skill builds features that improve cross-validated performance without smuggling the target into the inputs, and rejects features that only look good because they cheated.

## Operating procedure

Leakage prevention comes first because every later step (encoding, aggregation, imputation) is a leakage opportunity; validation comes last because a feature that does not move the cross-validated metric does not ship.

### Step 1: Gather inputs

Collect before writing transforms; label unknowns as guesses.

1. Prediction task and the exact prediction time: what is known at the moment the model must predict? Everything else is off-limits.
2. Target definition and how it was produced — features derived from the same process as the label are proxy-leakage suspects.
3. Data shape: row count, column list with dtypes, entity keys, and whether rows have a time dimension.
4. Model family (linear, tree ensemble, neural) — it determines which transforms are necessary and which are wasted work.
5. Serving constraints: will these features be computable at inference time with the same code and latency budget?

### Step 2: Lock the leakage rules

1. Fit all transformers (scalers, encoders, imputers) on the training fold only, then apply to validation and test.
2. Never use information from after the prediction time.
3. Exclude features that are proxies for the label or only known after the outcome (e.g. "account_closed_reason" in a churn model).
4. For time series, split by time and never shuffle.

Enforce mechanically with scikit-learn Pipelines and ColumnTransformer so fitting always happens inside cross-validation:

```python
from sklearn.pipeline import Pipeline
from sklearn.compose import ColumnTransformer
pre = ColumnTransformer([
    ("num", StandardScaler(), num_cols),
    ("cat", OneHotEncoder(handle_unknown="ignore"), cat_cols),
])
model = Pipeline([("pre", pre), ("clf", GradientBoostingClassifier())])
```

If any transform lives outside the pipeline, assume it leaks until proven otherwise.

### Step 3: Encode categoricals by cardinality

Pick the encoder from the category count — this is a lookup, not a debate:

- Up to ~15 categories: one-hot. Safe for any model.
- ~15-50 categories: one-hot still works for tree ensembles; for linear models watch the coefficient count.
- 50+ categories: target/mean encoding, computed strictly with out-of-fold means — in-fold target encoding is the single most common leakage bug in tabular ML. Add smoothing toward the global mean for categories with fewer than ~20 rows.
- 10,000+ categories (user IDs, URLs): hashing encoder when memory matters, or entity embeddings.
- Ordinal encoding only when categories have a true order (S < M < L), never as a memory shortcut.

### Step 4: Transform numerics

- Scale (standardize or min-max) for distance- and gradient-based models; tree ensembles do not need it — skip the work.
- Apply log or Box-Cox to skewed positive features; a rule of thumb trigger is |skewness| > 1.
- Bin into quantiles when the relationship is non-monotonic and the model is linear.

### Step 5: Build derived features

- Ratios and differences encode domain meaning cheaply: price per unit, days since last event.
- Polynomial or explicit interaction terms help linear models capture combined effects.
- Group-by aggregations (mean, count, std per entity) are the highest-value tabular features — and must be computed out-of-fold or over past-only windows, or they leak.
- Datetime: extract hour, day of week, month, is_weekend, plus cyclical sine/cosine encodings for periodic values, and elapsed-time features like days since signup.

### Step 6: Handle missing values

- Add a binary missing-indicator before imputing when the null rate exceeds ~5% — missingness is often informative.
- Impute numerics with median, categoricals with a dedicated "missing" category.
- Avoid mean imputation on skewed data.
- A feature over ~60% missing rarely earns its place unless the missingness itself predicts; test the indicator alone first.

### Step 7: Select and validate

- Remove near-constant features (over ~99% a single value) and one of any pair with |correlation| > 0.95.
- Use permutation importance or SHAP rather than raw model importances, which inflate high-cardinality features.
- A feature ships only if it improves the cross-validated metric, not training fit. Suspicious rule: any single new feature that jumps AUC by more than ~0.05 is a leakage suspect first and a triumph second — audit its availability at prediction time before celebrating.
- Keep a feature catalog documenting source, transformation, and refresh cadence; when features are shared across models, graduate to feature-store-design.

## Worked artifact: leakage contrast pair

Bad — target encoding fit on the full dataset before the split:

```python
means = df.groupby("merchant_id")["is_fraud"].mean()   # sees test-set labels
df["merchant_te"] = df["merchant_id"].map(means)
X_train, X_test = train_test_split(df)                  # too late: already leaked
```

Good — out-of-fold target encoding, so each row's encoding never saw its own label:

```python
from sklearn.model_selection import KFold
df["merchant_te"] = np.nan
for tr_idx, val_idx in KFold(5, shuffle=True, random_state=0).split(df):
    means = df.iloc[tr_idx].groupby("merchant_id")["is_fraud"].mean()
    global_mean = df.iloc[tr_idx]["is_fraud"].mean()
    df.loc[df.index[val_idx], "merchant_te"] = (
        df.iloc[val_idx]["merchant_id"].map(means).fillna(global_mean)
    )
```

The bad version typically shows a large offline lift that evaporates in production; the good version shows a smaller, honest lift that survives.

## Deliverable

Produce a feature specification containing: the prediction-time definition, the feature list with source, transform, encoder choice and rationale, the leakage audit (how each feature is available at prediction time), imputation and indicator decisions, and the cross-validated before/after metric for each added feature group.

## Do NOT

- Do not fit any transformer outside the cross-validation loop; the pipeline is the guardrail, not a style preference.
- Do not target-encode with in-fold means — it is leakage even when the code looks innocent.
- Do not scale features for tree ensembles or skip scaling for linear/kNN/neural models; match transform to model family.
- Do not trust raw impurity-based importances; they overrate high-cardinality columns.
- Do not keep a feature because it is clever; keep it because the cross-validated metric moved.

## Quality bar

- Every transform is inside a Pipeline/ColumnTransformer and fit on training folds only.
- Every feature has a documented answer to "is this value knowable at prediction time?"
- Encoder choices follow the cardinality ladder, with out-of-fold means for target encoding.
- Time-dimensioned data uses time-based splits.
- Each shipped feature shows a cross-validated improvement, and any outsized single-feature jump has been leakage-audited.
