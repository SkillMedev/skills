---
name: Revenue Operations
description: Unifies marketing, sales, and customer success around one funnel with org-wide stage definitions, hand-off SLAs, a single source of truth, shared metrics, and a pipeline-weighted forecast. Use when someone asks "set up RevOps", "marketing and sales disagree on what an MQL is", "our forecast is always wrong", "deals fall through the cracks between teams", or "what should our pipeline coverage be". Do NOT use for diagnosing conversion drop-off inside a product or marketing funnel — use funnel-analysis instead — for building the revenue financial model — use revenue-modeling instead — or for outbound activity math per rep — use prospecting-metrics instead.
---

# Revenue Operations

Marketing, sales, and customer success each optimize their own metric while the customer experience and the revenue number fall through the seams between them: marketing is measured on leads so it sends volume, sales is measured on closes so it cherry-picks, CS is measured on renewals so it inherits bad-fit customers. Each team is locally optimal and globally broken. RevOps fixes the seams — and the single highest-leverage fix is one funnel with one set of definitions the whole org signs.

## Operating procedure

Definitions come first because every metric, SLA, and forecast downstream inherits them; instrumenting an undefined funnel just automates the disagreement.

### Step 1: Gather inputs

1. The current funnel stages as each team describes them — collect all three versions; the deltas are the diagnosis.
2. The CRM and data stack, and where shadow spreadsheets live.
3. Current targets: revenue, pipeline, renewal.
4. Known seam failures — lead disputes, stalled handoffs, surprise churn. Label anecdotes as anecdotes.

### Step 2: Define one funnel, org-wide

Write the full funnel end to end and get all three leaders to sign the same document:

```
Lead -> MQL -> SQL -> Opportunity -> Closed-Won -> Onboarded -> Renewed/Expanded
```

The funnel-definition rule: exactly one definition of each stage exists org-wide, written in observable criteria, owned by RevOps, and changed only by agreement of all three leaders. Most pipeline disputes are definitional, not real — two teams counting different things and arguing about the difference. A definition that requires judgment ("seems ready to buy") is not a definition; require observable facts (fields filled, actions taken, meetings held).

### Step 3: Set the hand-off SLAs

Each seam gets a written SLA — what the sending team owes, what the receiving team owes, and the clock:

- Marketing -> sales: an MQL arrives with required fields complete; sales touches it within 24 hours (speed-to-lead decays fast — same-day contact converts several times better than next-week).
- Sales -> sales: an SQL is accepted or rejected with a reason within 48 hours; rejects route back with the reason coded, so definitions can be tuned on data.
- Sales -> CS: a closed deal arrives with a completed handoff doc (use-case, promises made, stakeholders) before kickoff; no doc, no kickoff scheduled.

### Step 4: Establish one source of truth

- A single CRM/data model all teams use; shadow spreadsheets are found and retired, not tolerated.
- Governed data: deduped accounts, consistent required fields, enforced hygiene — bad data quietly corrupts every report and forecast built on it.
- Instrument the funnel so every stage conversion and stage velocity is measurable without manual assembly.

### Step 5: Install shared metrics

Replace siloed vanity metrics with numbers that cross the seams:

- Pipeline coverage: open pipeline versus target. Healthy default is 3-4x for the current quarter (calibrate to your actual win rate: coverage needed ≈ 1 / win rate). Below 3x, escalate top-of-funnel now; padding above 5x usually means junk pipeline, not safety.
- Stage conversion rates and velocity — where deals stall and for how long.
- CAC and payback — ties marketing and sales to efficiency, not volume.
- Net revenue retention — ties CS and sales to expansion and to deal quality.
- Shared goals at the seams: marketing compensated partly on pipeline-sourced revenue, not just leads; sales partly on the quality of accounts passed to CS (e.g., 90-day retention of new logos).

### Step 6: Build the weighted forecast

- Forecast from pipeline data weighted by historical stage-conversion rates, not from rep optimism.
- Run one revenue forecast that all three leaders sign — not three forecasts reconciled in the CFO's inbox.
- Track forecast accuracy every quarter and tighten the weights; a mature RevOps forecast lands within ±10% of actuals. Persistent misses in one direction mean a stage definition or weight is wrong — fix the model, not the narrative.

### Step 7: Remove friction and run the cadence

- Map lead-to-cash and remove manual handoffs; automate routing, enrichment, and alerts so reps sell instead of data-entering.
- Keep the stack consolidated — tool sprawl recreates the silos RevOps exists to remove.
- Operating cadence: weekly cross-functional pipeline review; monthly funnel-health review (conversion, velocity, leakage); quarterly planning that allocates targets across the unified funnel.

## Worked artifact: stage-definition table

Copy and fill; this one page ends most pipeline arguments.

```
STAGE DEFINITIONS — signed by [FILL: CMO] / [FILL: CRO] / [FILL: VP CS] — v[FILL]

Stage        Entry criteria (observable)                       Owner      Exit SLA
Lead         Known email + company, source recorded            Marketing  —
MQL          Fit score >= [FILL] AND [FILL: action, e.g.       Marketing  Sales touch
             demo request or pricing-page visit x2]                       within 24h
SQL          Discovery call held; need + timeline + budget     Sales      Accept/reject
             fields complete in CRM                                       within 48h
Opportunity  Named buyer engaged; next step with date in CRM   Sales      —
Closed-Won   Signature received; handoff doc complete          Sales      CS kickoff <=5
                                                                          business days
Onboarded    Activation milestone hit: [FILL: product event]   CS         —
Renewed      Renewal signed / expansion booked                 CS         —

Change control: definitions change only at quarterly planning, all three signatures.
```

## Deliverable

Produce a RevOps blueprint: the unified funnel with the signed stage-definition table, hand-off SLAs with clocks, the single-source-of-truth data model and hygiene rules, the shared-metric set with the 3-4x coverage bar, the stage-weighted forecasting method with an accuracy target, and the weekly/monthly/quarterly operating cadence.

## Do NOT

- Do not let any team keep a private metric or spreadsheet that contradicts the shared dashboard — one contradiction re-legitimizes all the silos.
- Do not optimize lead volume over lead quality; volume compensation is how marketing floods sales with junk MQLs.
- Do not forecast from rep gut; weight the pipeline by measured stage conversion.
- Do not write stage definitions that require judgment calls — "feels qualified" guarantees the dispute continues under a new name.
- Do not celebrate coverage above 5x; audit it for zombie deals instead.
- Do not add tools to fix process problems; sprawl recreates the seams.

## Quality bar

- Every stage has observable entry criteria, one owner, and all three leaders' signatures.
- Every seam has an SLA with a clock (24h MQL touch, 48h SQL disposition, 5-day CS kickoff) and a coded reject path.
- One forecast exists, stage-weighted, with a tracked accuracy target of ±10%.
- Pipeline coverage is calibrated to actual win rate, not a borrowed multiple.
- The cadence calendar names owners for the weekly, monthly, and quarterly reviews.
