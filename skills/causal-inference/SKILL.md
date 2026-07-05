---
name: Causal Inference
description: Selects and executes a credible causal identification strategy — RCT, natural experiment, difference-in-differences, regression discontinuity, instrumental variables, or matching — ranked by assumption strength, with a confounder checklist, falsification tests, and a defensible effect estimate. Use when someone asks "did X actually cause Y", "how do I measure impact without an A/B test", "is this correlation causal", or needs to defend an effect estimate from observational data. Do NOT use for designing the data-collection study itself (sampling, instruments, power) — use primary-research instead; for reading out an already-run A/B test, use ab-test-analyzer instead.
---

# Causal Inference

A confident effect estimate built on a broken identification strategy is worse than no estimate: it triggers real decisions with fake evidence. This skill forces the choice of design before any regression is run, states the assumptions each design buys, and requires falsification tests before an effect is reported as causal.

## Inputs to collect

Gather these before choosing a design. If the user cannot supply one, propose a default and label it a guess.

1. **Treatment**: the intervention, precisely defined (who received it, when, at what intensity).
2. **Outcome**: the measured variable and its timing relative to treatment.
3. **Unit of analysis**: person, account, store, region — and how many units exist on each side.
4. **Assignment mechanism**: how units ended up treated — randomized, threshold rule, policy rollout, self-selection. This single fact determines which designs are available.
5. **Data availability**: pre-treatment periods (how many), untreated comparison units, candidate instruments, covariates.
6. **Decision at stake**: what action the estimate will drive, and how costly a wrong sign or 2x-off magnitude would be.

## Operating procedure

### Step 1: Frame the estimand and draw the DAG

Define treatment, outcome, unit, and the counterfactual in one sentence ("What would treated units' outcome have been absent treatment?"). Sketch a DAG listing confounders (cause both treatment and outcome), mediators (on the causal path), and colliders (caused by both). Never condition on a collider or any post-treatment variable; controlling for a mediator absorbs the effect you are trying to measure.

Run the confounder checklist — for each candidate variable ask:

- Does it plausibly influence who gets treated?
- Does it plausibly influence the outcome independent of treatment?
- Is it measured before treatment? (If measured after, it may be a mediator or collider — exclude it.)
- If it is unmeasured and answers yes to the first two, no amount of regression control fixes the problem — you need a design from higher in the ladder below.

### Step 2: Choose the strongest feasible design

Work down this ladder and stop at the first design the assignment mechanism supports. Order matters: each rung down requires strictly stronger untestable assumptions.

1. **RCT / randomized assignment** — assumptions are guaranteed by design. If randomization is feasible and ethical, run it (design the experiment with primary-research); everything below is a fallback.
2. **Natural experiment** (lottery, arbitrary cutoff in time, policy shock) — as-good-as-random assignment must be argued, not assumed. Document why units near the shock could not manipulate their exposure.
3. **Regression discontinuity** — treatment assigned by a threshold on a running variable. Local comparability near the cutoff is credible but the estimate is local, not global.
4. **Difference-in-differences** — requires parallel trends: absent treatment, treated and control groups would have moved together. Needs 3+ pre-periods to test it; with fewer, say so and downgrade confidence.
5. **Instrumental variables** — requires relevance (first-stage F ≥ 10; below that, weak-instrument bias) and exclusion (instrument affects the outcome only through treatment). Exclusion is untestable — argue it explicitly.
6. **Matching / propensity scores** — only removes bias from observed confounders. Say this in the report.
7. **Regression control** — the weakest rung. Acceptable for descriptive adjustment; label any causal language as conditional on "no unmeasured confounding," which is rarely believable.

### Step 3: Execute the chosen design

**Difference-in-differences.** The interaction coefficient `treated:post` is the ATT. Cluster standard errors at the unit of treatment assignment.

```python
import statsmodels.formula.api as smf
model = smf.ols("y ~ treated * post", data=df).fit(
    cov_type="cluster", cov_kwds={"groups": df["unit"]}
)
```

Validate parallel trends by plotting pre-period trends and running an event study with leads and lags; leads significantly different from zero kill the design.

**Regression discontinuity.** Local linear regression within a bandwidth around the cutoff:

```python
band = df[(df.x > cutoff - h) & (df.x < cutoff + h)].copy()
band["above"] = (band.x >= cutoff).astype(int)
smf.ols("y ~ above * I(x - @cutoff)", data=band).fit()
```

Choose the bandwidth with a data-driven method (Imbens-Kalyanaraman or similar), run a McCrary density test for manipulation of the running variable, and plot the discontinuity — the jump at the cutoff is the local effect.

**Instrumental variables.**

```python
from linearmodels.iv import IV2SLS
res = IV2SLS.from_formula("y ~ 1 + controls + [treatment ~ instrument]", df).fit()
```

Report the first-stage F-statistic. F < 10 means weak instruments and biased estimates — do not report the IV point estimate as the headline number.

### Step 4: Run falsification tests before believing the number

A design that passes none of these is not reportable as causal:

- **Placebo treatment**: apply the design to a period or cutoff where no treatment occurred; the estimated "effect" should be near zero.
- **Placebo outcome**: an outcome the treatment could not plausibly affect should show no effect.
- **Robustness sweep**: alternative bandwidths (RDD), alternative control groups (DiD), alternative covariate sets. If the estimate's sign or rough magnitude flips, report the fragility.
- **Sensitivity to unobserved confounding**: state how strong an unmeasured confounder would need to be to erase the effect.

### Step 5: Report

State, in this order: the estimand; the design chosen and its rung on the ladder; the identifying assumption in plain language; the estimate with clustered standard errors; the falsification tests run and their results; the threats that remain. Causal claims are only as strong as the design that supports them.

## Worked artifact: identification-strategy choice

**Question**: did a state's minimum-wage increase reduce fast-food employment?

- Assignment mechanism: policy change in one state at a known date — not randomized, no threshold rule, so RCT and RDD are out.
- Comparison units exist: neighboring state with no change, 8 quarters of pre-period data → **difference-in-differences is the strongest feasible rung**.
- Confounder checklist: regional economic shocks pass both tests (affect which state legislates, affect employment) — addressed by the control state sharing the regional economy; industry composition measured pre-treatment — include as covariate.
- Parallel trends: pre-period employment plotted for both states; event-study leads jointly insignificant (p = 0.62) → assumption survives.
- Falsification: placebo DiD on retail employment in an untreated earlier window estimates +0.4% (SE 1.1%) — consistent with zero.
- Verdict: report the DiD ATT with clustered SEs, labeled "credible under parallel trends; regional shock differentially hitting one state remains the main threat."

## Deliverable

Produce a causal analysis memo containing: the estimand, the design and why it beat the alternatives on the ladder, the DAG and confounder checklist results, the effect estimate with uncertainty, every falsification test with results, and the residual threats with a confidence grade.

## Do NOT

- Do not run regression control and call it causal because covariates were added — unmeasured confounders survive regression untouched.
- Do not condition on post-treatment variables, mediators, or colliders — each introduces bias in a different direction.
- Do not report an IV estimate with first-stage F < 10 as a headline number.
- Do not use naive standard errors on grouped data — cluster at the level of treatment assignment; serial correlation in DiD panels inflates significance dramatically.
- Do not select the sample on the dependent variable — it biases every estimate downstream.
- Do not present a local RDD estimate as an average effect for the whole population.

## Quality bar

Before shipping, the analysis must pass all of these:

- The identifying assumption is stated in one plain-language sentence a non-statistician can evaluate.
- The design is the highest feasible rung on the ladder, and the memo says why higher rungs were unavailable.
- At least one placebo test was run and reported, pass or fail.
- Standard errors are clustered correctly and the clustering level is stated.
- The report distinguishes what the data shows from what the design assumes.
