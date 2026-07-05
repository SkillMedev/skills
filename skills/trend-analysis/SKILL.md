---
name: Trend Analysis
description: Separates durable trends from noise using a baseline, seasonal decomposition, and a signal test requiring persistence, magnitude beyond 2 standard deviations, 3+ independent corroborating data points, and a plausible mechanism - then characterizes the trend's stage and forecasts trajectory with a confidence range. Use when someone asks "is this a real trend or a blip", "where is this metric or market heading", "should we bet on this shift", or must defend a forecast to stakeholders. Do NOT use for scouting what content formats are trending on Instagram - use instagram-trend-scout instead; for tracking brand mentions and sentiment shifts in news and social - use media-monitor instead.
---

# Trend Analysis

Distinguish durable trends from temporary fluctuations and project where they are headed. The hardest part is not spotting movement - it is deciding whether movement matters. The costly failure this skill prevents is the strategy built on a spike: a seasonal bump or a hype-cycle blip read as a secular shift, funded for three quarters, then quietly unwound.

## Inputs to collect

1. **The series or phenomenon** under study, and the metric that operationalizes it.
2. **History**: at least 2 full seasonal cycles of data where cycles exist (2 years for annual seasonality); with less, say the seasonality adjustment is unreliable and lower confidence.
3. **Candidate corroborating sources**: related metrics, independent datasets, external indicators.
4. **The decision at stake** and its horizon - a 6-month product bet and a 5-year infrastructure bet tolerate very different forecast uncertainty.
5. Defaults where the user has none: baseline window of 8+ periods, forecast horizon no longer than one-third of the observed history. Label assumed values as guesses.

## Operating procedure

### Step 1: Establish the baseline

Before calling anything a trend, define what "normal" looks like: compute a baseline level and expected variance over a relevant window. A data point is only notable relative to this. No baseline, no trend claim.

### Step 2: Decompose the series

Separate the data into components:

- **Trend**: the long-run direction.
- **Seasonality**: predictable recurring cycles (weekly, quarterly, yearly).
- **Noise**: random, unexplained fluctuation.

Many "trends" are just seasonality or noise. Adjust for seasonality before concluding anything - a December spike in a retail metric is not a trend, it is December.

### Step 3: Apply the signal-vs-noise test

Treat a movement as signal only if it passes the gate. The first three criteria are mandatory; mechanism is required before the trend can drive a decision:

- **Persistence**: sustained across multiple consecutive periods (3+ for the direction to be meaningful), not a single spike.
- **Magnitude**: exceeds normal variance - more than 2 standard deviations from the baseline.
- **Corroboration**: visible in **at least 3 independent data points or sources** before the movement is called a trend. Independence matters: three dashboards fed by the same pipeline are one source; a usage metric, a survey result, and an external market indicator are three.
- **Mechanism**: a plausible causal story for why the movement exists. A statistical pattern with no mechanism is a candidate, not a conclusion - it may still be watched, but not bet on.

A movement that fails persistence and corroboration is noise. Log it and move on; do not promote it because it is exciting.

### Step 4: Characterize confirmed trends

For each survivor of the gate, describe:

- Direction and rate of change - and whether the rate is linear or exponential (plot on a log scale to check; eyeballing raw exponentials misleads).
- Acceleration or deceleration.
- Stage: **emerging** (few adopters, high variance), **growing** (accelerating adoption), **maturing** (decelerating growth), **declining**. The stage sets the strategic clock - an emerging trend rewards early positioning; a maturing one rewards efficiency plays.
- Underlying drivers, and whether each driver is strengthening or weakening.

### Step 5: Forecast the trajectory

- Project forward with the simplest model that fits the decomposed trend; do not over-fit - a model with more parameters than the data supports predicts the past, not the future.
- Give a **range, not a point estimate**, and state confidence.
- Identify inflection risks that could bend the curve: saturation (adoption ceilings), substitution, regulation, backlash.
- Distinguish interpolation (safe) from extrapolation far beyond the observed data (risky). Rule: do not extrapolate further forward than one-third of the history observed without flagging the forecast as speculative.
- State the horizon explicitly - confidence decays sharply with distance.

### Step 6: Report with the invalidators

Deliver the trends found, the evidence per trend, the forecast range, the key assumptions, and - critically - the observable events that would invalidate the forecast, so the reader knows what to watch rather than when to blindly trust.

## Worked artifact: trend-evidence table

Claim under test: "Developer adoption of AI coding assistants is a durable growth trend, not hype."

| Test | Evidence | Independent? | Pass |
|---|---|---|---|
| Persistence | Weekly-active usage up 8 consecutive quarters in vendor telemetry | - | Yes |
| Magnitude | Growth rate 4.1 SD above the pre-period baseline for dev-tool adoption | - | Yes |
| Corroboration 1 | Vendor-reported paid-seat growth across 3 competing products | Yes - commercial data | Yes |
| Corroboration 2 | Independent developer survey: majority report weekly use, up from a small minority two waves earlier | Yes - survey, different collection method | Yes |
| Corroboration 3 | Job postings requiring assistant proficiency rising across two job boards | Yes - labor-market exhaust data | Yes |
| Mechanism | Falling inference cost + measured task-time savings give users and buyers a sustained reason to adopt | - | Yes |

Verdict: signal - 3 independent corroborations from different data-generating processes, persistent, large, with a mechanism. Stage: growing. Invalidators to watch: plateau in survey wave-over-wave usage, seat-growth deceleration across 2+ consecutive quarters, or a cost/regulation shock to the mechanism.

## Deliverable

Produce a trend report containing: the baseline and decomposition summary, a filled trend-evidence table per candidate trend, the verdict (signal / noise / watch) with the failed criteria named for rejects, stage and drivers for confirmed trends, a forecast range with horizon and confidence, and the explicit list of events that would invalidate each forecast.

## Do NOT

- Do not call a trend from fewer than 3 independent data points - one source repeated is one data point, and independence is what kills most trend claims.
- Do not skip seasonal adjustment; a recurring cycle read as a trend is the most common false positive.
- Do not treat a co-moving metric as a driver - correlation is not causation, and a trend "explained" by a fellow-traveler has no mechanism.
- Do not issue point forecasts; ranges with stated confidence, always.
- Do not extrapolate past one-third of the observed history without labeling it speculative.
- Do not ignore survivorship and selection bias in the underlying data - trends measured only on survivors always look better.
- Do not let hype cycles inflate early signals; discount novelty-driven spikes and demand the corroboration anyway.

## Quality bar

- Every trend claim shows a filled evidence table with all four tests, and the corroboration row counts genuinely independent sources.
- Rejected candidates are listed with the criterion they failed - silent drops hide the base rate.
- The forecast has a range, a horizon, a confidence statement, and named invalidators.
- A skeptical reader could re-run the signal test from the evidence cited.
