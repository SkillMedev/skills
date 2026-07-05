---
name: A/B Test Analyzer
description: Analyzes an A/B experiment to a defensible verdict - sample-size and minimum-detectable-effect math, two-proportion significance testing with confidence intervals, and checks for the traps that fake a win (peeking, sample ratio mismatch, multiple comparisons). Use when someone asks "is this test significant", "did variant B win", "how long should I run this experiment", "what sample size do I need", or shows experiment results and asks whether to ship. Do NOT use for logging and organizing ML training runs - use experiment-tracking instead; for causal questions without a randomized experiment use causal-inference; for general funnel or metric investigation use funnel-analysis or product-analytics.
---

# A/B Test Analyzer

Read experiments like a skeptic: most "wins" are noise until proven otherwise. The costly mistake this skill prevents is shipping on a peeked, underpowered, or mis-split test - a false positive that ships is worse than no test, because the team now defends a change that does nothing. Every verdict here comes with the interval, the checks that passed, and the checks that did not.

## Operating procedure

Order is mandatory: validity checks (Step 3) come before the significance math (Step 4), because a significant result on an invalid test is still invalid.

### Step 1: Gather inputs

Collect before computing anything; label unknowns as guesses.

1. The single primary metric and its type (conversion rate, revenue per user, retention).
2. The hypothesis and the minimum effect that would matter to the business - the smallest lift worth the cost of shipping and maintaining the change. If the requester has no number, help set one; without it "significant" is uninterpretable.
3. Per-arm sample sizes and metric values (successes and trials for proportions).
4. Whether the sample size and stopping rule were fixed before the test started, and whether anyone looked at results mid-flight.
5. Test duration and any events during it (marketing pushes, holidays, releases).
6. How many metrics and variants were compared.

### Step 2: Verify the test was designed to detect anything (power and MDE)

Defaults: significance level alpha = 0.05 (two-sided), power = 0.8. For a proportion metric with baseline rate p, the per-arm sample size to detect an absolute lift delta at those defaults is approximately:

n per arm = 16 * p * (1 - p) / delta^2

Worked instances at a 5 percent baseline conversion rate:

- Detect +1.0pp (5.0 to 6.0 percent): about 7,600 per arm.
- Detect +0.5pp: about 30,400 per arm.
- Detect +0.25pp: about 121,600 per arm - halving the MDE quadruples the sample.

Invert it to read a finished test: with n per arm known, MDE = sqrt(16 * p * (1 - p) / n). If the observed effect is well below the test's MDE, the test could never have confirmed it - verdict is inconclusive by design, not "no effect".

### Step 3: Run the validity checks

Any failure here caps the verdict at inconclusive regardless of the p-value.

- Sample ratio mismatch (SRM): for a 50/50 design, chi-squared test on the split; investigate whenever p < 0.001 (chi-squared above roughly 10.8 for two arms). A split of 31,890 vs 32,110 gives chi-squared 0.76 - fine. 31,000 vs 33,000 does not - the randomization or logging is broken, and no downstream number can be trusted.
- Peeking: if the team checked significance repeatedly and stopped when it looked good, the real false-positive rate is far above 5 percent. Only two clean designs exist: fix the sample size (or duration) in advance and evaluate once, or use a sequential method (for example, always-valid inference or group-sequential boundaries) designed for continuous monitoring. "We hit significance on day 4 so we stopped" is a peeked test.
- Duration and seasonality: run at least one full business cycle (typically 1-2 full weeks) even if the sample target is hit sooner; day-of-week mix and novelty effects distort short tests.
- Multiple comparisons: with k independent looks at alpha = 0.05, the chance of at least one false positive is 1 - 0.95^k - about 40 percent at k = 10. Designate one primary metric before the test; treat the rest as exploratory or apply a correction (Bonferroni: alpha / k) to any metric promoted to decision-making.

### Step 4: Compute the effect with uncertainty

Compute the difference with a confidence interval, never just a point estimate. For two proportions:

```python
import math
n_a, x_a = 32000, 1600   # control: trials, successes
n_b, x_b = 32000, 1760   # variant
p_a, p_b = x_a / n_a, x_b / n_b
diff = p_b - p_a
se = math.sqrt(p_a*(1-p_a)/n_a + p_b*(1-p_b)/n_b)
z = diff / se
p_val = 2 * (1 - 0.5 * (1 + math.erf(abs(z) / math.sqrt(2))))
lo, hi = diff - 1.96 * se, diff + 1.96 * se
print(f"control {p_a:.2%}  variant {p_b:.2%}")
print(f"diff {diff*100:+.2f}pp  95% CI [{lo*100:+.2f}, {hi*100:+.2f}]pp")
print(f"z = {z:.2f}  p = {p_val:.4f}")
print(f"relative lift {diff/p_a:+.1%}  CI [{lo/p_a:+.1%}, {hi/p_a:+.1%}]")
```

Expected output:

```
control 5.00%  variant 5.50%
diff +0.50pp  95% CI [+0.15, +0.85]pp
z = 2.84  p = 0.0046
relative lift +10.0%  CI [+3.1%, +16.9%]
```

A Bayesian posterior on the lift is an acceptable alternative; either way, report the interval.

### Step 5: Separate statistical from practical significance

Statistical significance says the effect is probably not zero. Practical significance says it clears the minimum effect from Step 1. A +0.05pp lift with p = 0.01 on a 200-million-user test is real and possibly still not worth shipping. Compare the entire confidence interval against the minimum-effect bar, not just the point estimate.

### Step 6: Issue the verdict

Exactly one of three, with the reasoning attached:

- Ship: validity checks pass, p < 0.05 on the pre-designated primary metric, and the interval's lower bound is at or above (or credibly near) the minimum effect that matters.
- Don't ship: validity checks pass and the interval excludes the minimum effect, or the point estimate is negative with a well-powered test.
- Inconclusive: any validity check failed, or the interval spans both "worth it" and "not worth it". Be explicit when the honest answer is "we need more data", and say how much more using the Step 2 formula.

## Worked artifact: readout with the correct interpretation

```
EXPERIMENT: checkout-button-copy-v2      PRIMARY METRIC: checkout conversion
DESIGN: fixed n = 32,000/arm (MDE 0.49pp at baseline 5%), one evaluation,
        14-day run, no mid-flight peeks logged
VALIDITY: SRM chi2 = 0.76 (pass) | duration 2 full weeks (pass)
          | 1 primary metric, 4 exploratory (pass)
RESULT: control 5.00% -> variant 5.50%
        diff +0.50pp, 95% CI [+0.15, +0.85]pp, p = 0.0046
        relative lift +10.0%, CI [+3.1%, +16.9%]
PRACTICAL BAR: minimum effect that matters = +0.20pp (set pre-test)
VERDICT: SHIP. The effect is statistically significant and the interval's
lower bound (+0.15pp) sits just under the +0.20pp bar while the point
estimate clears it 2.5x; expected value strongly favors shipping. Note
honestly: the true lift could be as low as +0.15pp - monitor the metric
post-launch rather than re-litigating the test.
```

The wrong interpretation of the same numbers: "10 percent lift, p < 0.01, huge win." The relative lift's own interval runs from +3.1 to +16.9 percent - quoting only the point estimate overstates certainty, and quoting relative lift without the absolute base (0.5pp on 5 percent) inflates perceived impact.

## Deliverable

Produce a one-page readout containing: the pre-registered primary metric and minimum effect, the design (n per arm, MDE, stopping rule), each validity check with pass/fail, the absolute and relative effect with 95 percent intervals and p-value, and a ship / don't ship / inconclusive verdict whose reasoning cites the interval against the practical bar - plus, when inconclusive, the additional sample required.

## Do NOT

- Do not evaluate significance repeatedly and stop at the first p < 0.05; peeking inflates the false-positive rate several-fold. Pre-commit the sample size or use a sequential method.
- Do not trust any result with sample ratio mismatch; a broken split means broken randomization, and the bias direction is unknowable.
- Do not scan ten metrics and celebrate the one that hit p < 0.05 - at ten looks, a spurious winner appears about 40 percent of the time.
- Do not report a point estimate without its interval, or a relative lift without the absolute base rate.
- Do not conclude "no effect" from an underpowered test; a test with MDE 1pp saying nothing about a 0.3pp effect is silence, not evidence of absence.
- Do not end a test mid-week or during an anomaly window and treat the traffic as representative.

## Quality bar

A readout ships only when: the primary metric and minimum effect were fixed before results were seen; every validity check appears with an explicit pass/fail; the interval - not the point estimate - is compared against the practical bar; the verdict is one of the three allowed words with its reasoning; and an inconclusive verdict quantifies the data still needed.

Route neighbors correctly: organizing ML training runs belongs to experiment-tracking; estimating effects without randomization belongs to causal-inference; diagnosing where a funnel leaks belongs to funnel-analysis.
