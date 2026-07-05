---
name: Media Monitor
description: Turns raw brand mentions across news and social into an actionable comms picture — precise monitoring queries, grounded sentiment classification, numeric alert thresholds for narrative shifts, severity triage, and a daily digest a comms team can act on within minutes. Use when someone asks "set up brand monitoring", "are people talking about us and how", "is this negative story spreading", "design our mention alert thresholds", or "write our daily media digest". Do NOT use for spotting broader market or cultural trends beyond a tracked brand — use trend-analysis instead — or for gathering intelligence on competitors' moves — use competitive-intelligence instead.
---

# Media Monitor

Raw mention feeds are noise; the costly failures are symmetric — missing the forum post that becomes a national story eighteen hours later, and escalating a comms fire drill over three sarcastic tweets. This skill turns mentions into an actionable picture: queries precise enough to trust, sentiment grounded in text, numeric thresholds that separate shift from chatter, and a digest that lets a comms team act within minutes.

## Operating procedure

Query design comes first because every downstream number — volume, sentiment, spike detection — inherits the query's precision; a noisy query makes every alert a false one.

### Step 1: Gather inputs

1. Entities to track: brand names, products, executives, competitors, campaign hashtags.
2. Known ambiguities: homonyms, generic words in the name, celebrity name collisions.
3. Sources in scope and any platform access limits.
4. The team's alert appetite: who gets paged, and how many false alarms per week they will tolerate (default: tune for under 2).
5. Baseline history if it exists — 30 days of mention volume; without it, run 7 days silent to establish the baseline before alerting.

### Step 2: Design the queries

- Include common misspellings, abbreviations, and acronyms for every entity.
- Exclude ambiguous homonyms with negative keywords, and iterate until precision holds.

Bad: `Apple` — drowns in fruit, recipes, and idioms; every metric downstream is garbage.
Good: `("Apple" OR "AAPL" OR "@Apple") AND (iPhone OR Mac OR Cupertino OR earnings) -fruit -pie -recipe -crumble`

- Tier the sources: news (wire, national, trade), social (X, Reddit, forums), and owned channels. Weight by reach and credibility — one wire-service story outweighs fifty anonymous forum posts for severity, but the forum is where stories start, so it is watched, not ignored.
- Test each query on a sample of 50 recent hits; require at least 90% of results to be genuinely about the entity before the query goes live. Below that, alerts will be ignored within a week.

### Step 3: Classify each mention

Capture for every item: source, date, reach estimate, sentiment, and topic/theme. Sentiment rules:

- Three labels — positive / neutral / negative — plus a one-line rationale quoting the text.
- Ground sentiment in the body text, never the headline alone; headlines are written for clicks, not accuracy.
- Mark low-confidence calls explicitly, especially sarcasm and irony — an over-confident sentiment score is worse than an honest "unclear."

### Step 4: Detect narrative shifts with numeric thresholds

A single negative post is noise; a shift is signal. Alert on:

- Volume spike: mentions exceeding the rolling 7-day baseline by 2x or more. Use the rolling baseline, not a fixed number, so growth and seasonality do not fire false alarms.
- Sentiment swing: net sentiment (positive minus negative, as a share of total) moving more than ~20 points period-over-period.
- New framings: keywords or phrases prominent this period that were absent last period — the earliest shift signal, and the one volume metrics miss.
- Source migration: a story jumping tiers, forum to trade press to national. Migration is the strongest predictor that a story is about to get bigger; page on it even when volume is still small.

### Step 5: Triage severity

- Critical: legal or safety claim, an executive named, volume accelerating, credible source or migrating upward. Page now.
- Watch: rising volume, mixed sentiment, contained to one platform. Recheck within hours, note in the digest.
- Routine: normal chatter, stable sentiment. Digest only.

### Step 6: Report

Deliver the daily digest in this order:

1. Headline numbers: total mentions, net sentiment, both versus prior period.
2. Top 3 stories driving the conversation, each with reach and sentiment.
3. Narrative shifts detected, with the threshold that tripped and why it matters.
4. Recommended action per critical item: respond / monitor / escalate.

When citing any mention, link the primary source and quote verbatim — never paraphrase a quote in a way that changes its meaning.

## Worked artifact: daily digest template

```
MEDIA DIGEST — [FILL: brand] — [FILL: date]

HEADLINE NUMBERS
  Mentions: [FILL]  (7-day baseline: [FILL], ratio: [FILL]x)
  Net sentiment: [FILL] pts  (prior period: [FILL], change: [FILL] pts)

TOP 3 STORIES
  1. [FILL: story] — reach [FILL], sentiment [FILL], link [FILL]
  2. [FILL]
  3. [FILL]

SHIFTS DETECTED
  [FILL: e.g., "volume 2.4x baseline, driven by <framing>; migrated from
   r/<sub> to <trade outlet> at 14:00 — migration threshold tripped"]

ACTIONS
  CRITICAL: [FILL: item] -> [respond / monitor / escalate] — owner [FILL]
  WATCH:    [FILL: item] -> recheck at [FILL: time]
```

## Deliverable

Produce a monitoring package: the query set per entity with negative keywords and the 90% precision test result, the source tiers with weights, the alert thresholds (2x volume, 20-point sentiment, new-framing and migration triggers) tuned to the team's false-alarm tolerance, and the daily digest in the template above with a recommended action per critical item.

## Do NOT

- Do not alert off a raw mention count — always ratio against the rolling 7-day baseline, or growth guarantees permanent false alarms.
- Do not score sentiment from headlines; headlines are engineered for clicks and routinely invert the body's tone.
- Do not treat organic and coordinated activity as one signal — flag suspicious clusters (identical phrasing, synchronized timing, young accounts) and report them separately.
- Do not infer causation from a single correlated spike; note the correlation and keep watching.
- Do not paraphrase quotes; verbatim with a link, every time.
- Do not dox individuals — aggregate patterns, respect platform terms and privacy.
- Do not ignore small-volume stories that are migrating up source tiers; migration outranks volume as a warning sign.

## Quality bar

- Every live query passed the 90% precision sample test, and the test is recorded.
- Every sentiment label carries a text-grounded rationale, with low-confidence calls marked.
- Every alert in the digest names the numeric threshold that tripped it.
- Every critical item has a recommended action and an owner.
- Bot-suspect clusters are flagged and excluded from the organic numbers.
