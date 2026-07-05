---
name: LLM Evaluation
description: Builds evaluation harnesses for LLM products - golden datasets, deterministic checks, calibrated LLM-as-judge rubrics, and CI regression gates that turn "seems good" into a tracked number. Use when someone asks "how do I know this prompt change didn't break anything", "set up evals for my RAG pipeline", "is LLM-as-judge reliable", "why did quality drop after the model swap", or is shipping an LLM feature with no quality measurement. Do NOT use for classical ML model reporting (precision/recall, ROC curves, confusion matrices on trained classifiers) - use model-evaluation-report instead; do NOT use for analyzing online A/B experiments - use ab-test-analyzer instead.
---

# LLM Evaluation

A prompt or model change that looks fine on the three examples you tried will break inputs you did not try, and without an eval you find out from users. The costly mistake this skill prevents is shipping on vibes: an uncalibrated judge or a ten-example "test" gives you a number that moves randomly, so real regressions hide inside noise. Build the dataset first, calibrate the judge before trusting it, and gate changes in CI.

## Operating procedure

Follow the order: dataset before metrics, calibration before judging, pipeline before iteration. A judge calibrated against nothing is a random-number generator with confidence.

### Step 1: Gather inputs

Collect these before building anything. Where the user cannot answer, use the default and label the value a guess.

1. Task definition - what the system produces, in one sentence.
2. What "good" means - 3-5 quality dimensions (correctness, groundedness, tone, format). Default: correctness plus format validity.
3. Real failure examples - at least 5 outputs the user considers bad, with why.
4. Traffic source - production logs, support tickets, or synthetic. Prefer production; label synthetic data as synthetic.
5. Human-label budget - how many examples a person will grade. Default: 50 for judge calibration.

### Step 2: Build the dataset

1. Collect 50-200 real, representative inputs. Below 50, differences under about 10 percentage points are indistinguishable from noise; treat any sub-5-point movement on a 100-example set as noise and grow the set before believing it.
2. Ensure at least 30 examples per slice you will report on (per intent, per language, per document type); prefer 100+ per slice that gates a launch.
3. Include hard cases, adversarial inputs, and out-of-distribution inputs deliberately - an eval of only easy cases certifies nothing.
4. Label expected outputs or acceptance criteria per example.
5. Freeze a held-out test set; iterate on a separate dev set. Never tune prompts against the test set - that is overfitting with extra steps.
6. Check for leakage: no eval input may appear in the prompt's few-shot examples.

### Step 3: Choose metrics, cheapest first

Work down this ladder and stop as early as the task allows:

1. Deterministic - exact match, regex, JSON-schema validity, contains/excludes. Cheap, reliable, zero drift. Use for anything with a checkable answer.
2. Reference-based similarity to a gold answer - use sparingly; surface form varies.
3. Faithfulness/groundedness - for RAG: does the answer stay within the provided context.
4. LLM-as-judge - for subjective quality only after steps 1-3 are exhausted.
5. Human preference - pairwise comparisons as the final arbiter.

Measure cost and latency alongside quality on every run; a 2-point quality win that doubles latency is a product decision, not a free win.

### Step 4: Write and calibrate the judge

1. Write a rubric with behavioral anchors per score (see the worked rubric below), not adjectives.
2. Prefer pairwise (A vs B) over absolute 1-5 scoring - it is more reliable.
3. Have 2+ humans label the same 50-example calibration sample. Compute inter-rater agreement: require Cohen's kappa ≥ 0.7 between humans before using the labels; if kappa lands below 0.7, the rubric is ambiguous - rewrite the anchors and relabel, do not average the disagreement away.
4. Run the judge on the calibration sample. Require ≥ 80% agreement with the human majority label before the judge gates anything. Below 80%, fix the rubric or the judge prompt, not the threshold.
5. Control known judge biases: position bias - run every pairwise comparison twice with A/B swapped and discard verdicts that flip; verbosity bias - judges favor longer answers, so state in the rubric that length is not quality; self-preference bias - judges rate their own model family higher, so use a judge from a different family than the generator when the comparison involves that family.

### Step 5: Wire the pipeline

1. Run the eval on every prompt or model change in CI; block merge on regression beyond the noise floor you measured in Step 2.
2. Pin model versions, temperature, and seeds. If temperature > 0, run 3+ samples per input and report the variance, not just the mean.
3. Tier judge cost: a cheap model screens, the strong model re-judges only close calls.
4. Track per-metric scores over time and alert on regressions.

### Step 6: Read results like a practitioner

Never report only the average. Every run's readout includes: score per slice, the 10 worst examples with outputs inline, and what changed since the last run. The worst cases are where the next fix lives.

## Worked artifact: calibrated judge rubric

```text
You are grading a support-bot answer against the retrieved context.
Score each dimension independently. Length is not quality.

GROUNDEDNESS
5 - Every factual claim is traceable to a sentence in the context.
3 - Claims are consistent with the context but at least one is not
    directly supported.
1 - Any claim contradicts the context, or the answer invents a
    policy, price, or entity not present in it.

CORRECTNESS
5 - Fully answers the user's actual question, including the edge the
    user raised.
3 - Answers the main question but misses a stated sub-question.
1 - Answers a different question, or refuses a answerable question.

Return JSON only: {"groundedness": n, "correctness": n,
"worst_quote": "<the single worst sentence, verbatim>"}
```

Calibration record to keep with the rubric: human-human kappa, judge-human agreement %, sample size, and the date-free version tag of the judge model. If any of those is missing, the rubric is not yet trusted.

## Deliverable

Produce an eval harness containing: (A) a frozen test set and a dev set with per-slice counts, (B) the metric ladder with deterministic checks implemented, (C) a calibrated judge rubric with its recorded kappa and judge-human agreement, (D) a CI job that runs on every change and fails on regression, and (E) a readout template showing per-slice scores, variance, cost, latency, and the 10 worst examples.

## Do NOT

- Do not trust a judge you never calibrated - an uncalibrated judge encodes its biases as your quality bar.
- Do not tune on the test set; the score becomes a measure of your tuning, not your system.
- Do not report a single averaged score - averages hide the slice that broke.
- Do not run pairwise comparisons in one order only; position bias alone can flip a verdict.
- Do not compare runs across unpinned model versions or temperatures - you are measuring the platform, not your change.
- Do not use this skill's rubrics to report classical ML metrics; that job belongs to model-evaluation-report.

## Quality bar

- Every gating slice has ≥ 30 examples and the noise floor is stated.
- Human inter-rater kappa ≥ 0.7 and judge-human agreement ≥ 80% are recorded next to the rubric.
- Deterministic checks cover everything checkable before any judge runs.
- The eval runs unattended in CI and a regression actually blocks a merge.
- The readout names the worst failures, not just the mean.

## Escalation

Pair with prompt-engineer when the fix for a failing eval is prompt structure. For monitoring input distribution shift in production rather than output quality, route to data-drift-monitor.
