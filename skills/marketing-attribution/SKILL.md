---
name: Marketing Attribution
description: Guides selection and honest application of a marketing attribution model, surfacing common measurement traps. Use when evaluating channel ROI, auditing tracking, or choosing between attribution models.
---

# Marketing Attribution

Attribution does not reveal truth — it reveals a model's assumptions about truth. Choosing a model is choosing a set of tradeoffs. The goal is to pick the model that creates the least-distorted decisions for the business, not the one that flatters the most channels.

## Understand What Each Model Assumes

First-touch attribution credits the first interaction entirely — useful for understanding discovery channels, but it ignores everything that closed the sale. Last-touch does the opposite and over-credits closers like brand search. Linear attribution spreads credit evenly — neutral but often meaningless. Time-decay weights recent touches more heavily — reasonable for short sales cycles. Data-driven attribution uses conversion path modeling — best in theory but requires high conversion volume (typically 1,000+ monthly conversions) and complete tracking. Position-based (U-shaped) splits 40/20/40 between first touch, middle, and last touch — a practical default for teams that lack volume for data-driven models.

## Choose Based on Sales Cycle and Data Volume

For sales cycles under 7 days with fewer than 500 monthly conversions: use last-touch with branded/direct excluded. For cycles 7-30 days: use position-based. For cycles over 30 days or complex B2B: use time-decay or a CRM-based opportunity model. For teams with 1,000+ conversions and clean tracking: test data-driven and compare decisions it produces versus current model. Never use first-touch as a primary model for budget allocation — it systematically over-invests in awareness and starves conversion channels.

## Avoid the Three Most Common Traps

Trap 1 — View-through inflation: Meta and display platforms count view-through conversions by default, crediting an impression even when the user converted organically. Set view-through window to 1 day maximum or disable it, and compare platform-reported conversions to actual revenue. Trap 2 — Cross-device gaps: a user who sees an ad on mobile and converts on desktop appears as two separate paths. Use a unified identity layer or accept that mobile spend will be systematically undercredited. Trap 3 — Sampling and consent loss: iOS privacy changes and cookie deprecation mean 20-40% of conversions may be untracked. Use aggregated event measurement, first-party data enrichment, and model-based estimation — do not treat reported numbers as complete.

## Triangulate, Do Not Depend on One Signal

No single attribution model is sufficient. Use platform-reported data alongside three additional signals: (1) geo holdout tests — pause a channel in a test market and measure revenue impact; (2) incrementality tests — meta-analysis tools or platform lift studies; (3) MTA modeled data from a third-party tool. When all three signals agree, act with confidence. When they conflict, investigate before making budget decisions.

## Worked Example: Same Data, Two Models, Two Decisions

A DTC brand with a 14-day sales cycle and 800 monthly conversions reviews channel performance.

Bad: The team reads last-touch platform reports as-is. Brand search shows a 6x ROAS, Meta prospecting shows 0.9x. They cut Meta prospecting 50%. Six weeks later, brand search volume falls — the prospecting they cut had been filling the top of the funnel, and last-touch had been handing its credit to the branded query that closed each sale. Total revenue drops while every remaining channel's reported ROAS looks better than ever.

Good: The team applies the cycle-length rule above (14 days → position-based), excludes branded search from performance evaluation, and runs a geo holdout: Meta prospecting paused in two matched test markets for 4 weeks. Revenue in test markets falls 12% versus control — proof the channel is incremental despite its ugly last-touch numbers. Budget stays, and the position-based report becomes the shared reference, with a one-line disclaimer naming the model.

The data never changed. The model — and the validation — changed the decision.

## Document and Communicate the Model

Every stakeholder who sees attribution data must understand which model produced it and what it does not capture. Include a one-sentence model disclaimer on every attribution report. Change models infrequently — switching mid-year makes year-over-year comparisons meaningless. When a model change is necessary, run both models in parallel for at least one quarter before switching fully.

## Deliverable

Produce an attribution decision memo containing: the recommended model with the sales-cycle and volume evidence behind it, the channels excluded from performance credit (branded search, direct), the platform settings to correct (view-through windows, consent-loss adjustments), the triangulation plan (which holdout or incrementality test validates the model, and when), and the one-sentence disclaimer that ships on every report the model produces.

## Quality bar

- The recommended model matches the documented sales cycle and monthly conversion volume — not a default or a platform's preference.
- Branded and direct traffic are explicitly handled, not silently credited.
- At least one independent validation signal (geo holdout, lift study, or MTA comparison) is scheduled before major budget moves.
- Every number presented to stakeholders names the model that produced it.
- No budget reallocation rests on a single platform's self-reported conversions.
