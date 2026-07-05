---
name: Landing Page CRO
description: Improves an existing landing page's conversion rate through evidence-first CRO - audit heatmaps and recordings, write structured hypotheses, prioritize with ICE scoring, and run one-variable tests to statistical significance. Use when someone asks "why isn't my landing page converting", "audit this page before we scale spend", "which A/B test should we run first", or after a CVR drop or ahead of a paid-traffic push. Do NOT use for writing a new landing page from scratch - use landing-page-copy instead. Do NOT use for auditing the ad accounts sending the traffic - use paid-acquisition-audit instead. Do NOT use for reading the results of a finished experiment - use ab-test-analyzer instead.
---

# Landing Page CRO

Conversion rate optimization is not applying best practices - it is forming specific hypotheses from real user evidence, testing them, and iterating. The costly failure this skill prevents is the redesign roulette: shipping a batch of untested "improvements" that mixes winners with losers, moves nothing, and teaches nothing, while paid spend keeps landing on a leaky page.

## Operating procedure

Order is strict: audit before hypotheses (a hypothesis without an observation is a guess), hypotheses before prioritization, prioritization before tests, and significance before verdicts.

### Step 1: Gather inputs

Collect before auditing. Label anything assumed as a guess.

1. Page URL and its single conversion goal (form fill, purchase, signup). A page with two goals is already a finding.
2. Current CVR and traffic: unique visitors per week and conversions per week - these decide the testing method in Step 5.
3. Traffic sources and the ads or emails sending them (needed for the message-match check in Step 2).
4. Available tooling: heatmaps, session recordings, analytics funnel. Default: assume analytics only and flag the gap.
5. Constraints: brand rules, dev bandwidth, deadline.

### Step 2: Audit before hypothesizing

Collect two evidence types:

- Quantitative: heatmaps, scroll maps, click maps, session recordings, funnel drop-off data. Spend at least one hour in session recordings before forming opinions.
- Qualitative: user surveys, live-chat transcripts, sales call notes - objections in the users' own words.

The audit must answer three questions: where are users leaving, what are they clicking that does not help them convert, and what objections repeat verbatim.

Run the message-match check first - it is the single most common CRO failure: the ad makes a specific promise and the page delivers a generic brand statement. Every paid source must land on a page whose headline directly echoes the ad's offer. If one page serves multiple campaigns, prescribe dynamic text replacement or dedicated pages per major campaign theme before any other test.

Then audit the five highest-leverage elements, in order of typical impact: (1) headline - must match the ad's promise and speak to a specific pain; (2) primary CTA - text, position, contrast; (3) social proof - specificity beats volume; a named customer quote outperforms a star rating; (4) above-the-fold visual - must show the outcome, not the product; (5) form/checkout friction - every unnecessary field reduces conversion. Fix these before testing background colors or font sizes.

### Step 3: Write structured hypotheses

Every hypothesis uses this exact format: "Because [observation from audit], changing [element] to [proposed change] will [expected measurable outcome] for [audience]." No observation means it is a guess and does not enter the backlog. Write hypotheses before mockups - the thinking matters more than the execution.

### Step 4: Prioritize with ICE

Score each hypothesis 1-10 on Impact (how much it moves CVR if true), Confidence (strength of the evidence behind it), and Ease (speed to build and test), then multiply. Run the top score first. Run at most two simultaneous tests on the same page - more complicates interpretation. Change one variable per test; a test that changes headline and CTA together cannot attribute its own result.

### Step 5: Test to significance

- Standard: do not call a winner before 95% statistical significance with at least 100 conversions per variant.
- Low traffic (roughly under 1,000 conversions per month): switch to Bayesian testing with a 90% probability-to-beat threshold, or test bigger swings (full section rewrites) where the effect size is detectable.
- Never end a test early because one variant "looks better" - regression to the mean is real. Pre-commit the sample size or run duration before launch, and run at least one full business cycle (usually one to two weeks) so weekday/weekend mix is represented.
- Document every outcome including losses in a test log - a failed hypothesis is still a learning, and an undocumented one gets re-run in six months.

### Step 6: Ship, log, loop

Roll out the winner, record the result against the hypothesis, re-score the backlog (a win often changes Confidence on related hypotheses), and return to Step 4.

## Worked artifact: scored hypothesis backlog

```
PAGE: /demo-request     GOAL: demo form fill     BASELINE CVR: 2.1%   TRAFFIC: 9,400/wk

| # | Hypothesis (Because / changing / will / for)                              | I | C | E | ICE |
|---|---------------------------------------------------------------------------|---|---|---|-----|
| 1 | Because 62% of paid visitors bounce in <10s and the ad promises "cut      | 8 | 8 | 9 | 576 |
|   | invoice time 80%" while the headline says "The modern finance platform",  |   |   |   |     |
|   | changing the headline to echo the ad's offer will lift CVR for paid       |   |   |   |     |
|   | traffic.                                                                  |   |   |   |     |
| 2 | Because recordings show 40% of users open the 7-field form and abandon    | 7 | 7 | 6 | 294 |
|   | at "phone number", removing phone + company size will lift form           |   |   |   |     |
|   | completion for all traffic.                                               |   |   |   |     |
| 3 | Because chat transcripts repeat "does this work with NetSuite?",          | 6 | 6 | 7 | 252 |
|   | adding a NetSuite logo + one-line integration proof above the fold will   |   |   |   |     |
|   | lift CVR for ERP-sourced segments.                                        |   |   |   |     |

RUN ORDER: #1 first. One variable. Min sample: 100 conversions/variant, 95% sig,
full 2-week cycle. Result logged to test log regardless of outcome.
```

## Deliverable

Produce a CRO package containing: the audit findings (drop-off points, misleading clicks, verbatim objections, message-match verdict), a scored hypothesis backlog in the format above, the test plan for the top hypothesis (variable, sample size, significance standard, duration), and a test log structure for recording outcomes.

## Do NOT

- Do not test before auditing - hypotheses without observations are guesses, and testing guesses burns traffic.
- Do not change multiple variables in one test; the result becomes unattributable and the "learning" is noise.
- Do not peek and stop early when a variant leads - early leads regress, and calling them ships losers.
- Do not start with micro-elements (button shades, fonts) while the five high-leverage elements have unaddressed findings.
- Do not run frequentist 95%/100-conversion tests on a low-traffic page - the test will never conclude; use Bayesian thresholds or bigger swings.
- Do not skip documenting losing tests; undocumented losses get expensively re-run.

## Quality bar

Ship only when: every hypothesis cites a specific audit observation; ICE scores are justified, not vibes; each planned test isolates one variable; the significance standard and sample floor are pre-committed in writing; message match is verified for every paid source; and the test log exists before the first test launches.

## Escalation

Writing net-new page copy is landing-page-copy; diagnosing the ad side of poor performance is paid-acquisition-audit; deep statistical readout of a completed experiment is ab-test-analyzer; funnel-wide drop-off beyond one page is funnel-analysis.
