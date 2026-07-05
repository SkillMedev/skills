---
name: Churn Reduction
description: Diagnoses SaaS churn root causes through cohort analysis and a seven-category taxonomy, then builds segmented intervention playbooks ranked by frequency, revenue at stake, and addressability - including save-offer economics. Use when someone asks "why is our churn so high", "build a churn reduction plan", "should we offer discounts to cancelling customers", or when NRR/GRR is slipping and the team needs a diagnosis before prescriptions. Do NOT use for gym or fitness-studio member retention - use retention-and-churn-killer instead. Do NOT use for re-acquiring customers who already left - use win-back-campaign instead. Do NOT use for growing existing accounts - use expansion-revenue instead.
---

# Churn Reduction

Churn is the silent killer of SaaS - 5% monthly churn caps the business no matter how fast acquisition runs, because it compounds into LTV, NRR, and valuation. The costly mistake this skill prevents is prescribing before diagnosing: launching a save-offer program or a CS hiring spree against a blended churn number, when the real leak is bad-fit acquisition or failed onboarding that no discount can fix.

## Operating procedure

Diagnosis strictly precedes intervention. Steps 1-3 establish what is actually leaking; only then do playbooks get built.

### Step 1: Gather inputs

1. Monthly logo churn and revenue churn for the trailing 12 months. Track both - a small customer leaving and a whale leaving are different problems.
2. GRR and NRR. Gross revenue retention (churn + contraction only) isolates the leak; net revenue retention (includes expansion) can hide it. An NRR of 105% with GRR of 82% is a leaky bucket propped up by a few expanding whales.
3. Customer list with segment, plan, signup month, acquisition channel, ACV.
4. Cancellation reasons, exit interviews, and support history where they exist. Default: sparse - plan interviews in Step 3.
5. Payment-failure data (involuntary churn is often invisible in "reasons").

Label any estimated figure as an estimate.

### Step 2: Cohort before concluding

Blended churn averages away the insight. Cut retention three ways minimum: by signup month (is the product getting stickier?), by segment (SMB vs mid-market behave differently), and by acquisition channel (a cheap channel with 2x churn is not cheap). Benchmarks to read against: SMB-serving SaaS commonly runs 3-7% monthly logo churn; mid-market/enterprise should be at or under 1-2% monthly (10-15% annually); GRR of 90%+ is healthy for SMB, 95%+ for enterprise. A cohort more than double its peer cohorts is where the diagnosis starts.

### Step 3: Categorize every churn - the seven-category taxonomy

Interview churned customers and read cancellation reasons; the categories shift the roadmap, not just CS scripts. Assign every churned account to exactly one primary category:

1. Bad fit - never the ICP. Fix: tighten qualification, not retention. Cheapest churn to eliminate (stop acquiring them).
2. Poor onboarding - never reached first value. Fix: time-to-value and activation. Often the single biggest lever.
3. Low adoption - bought it, never embedded it in workflow.
4. Missing value - used it, did not get the outcome. Fix: product or success motion.
5. Champion left - the sponsor changed jobs. Fix: multi-thread accounts.
6. Price/budget - economic, often macro-driven.
7. Competitor - lost head-to-head. Fix: product or positioning.

### Diagnosis decision tree

```
Churned account
├─ Did payment fail (card decline, expiry)?
│    → INVOLUNTARY: dunning/card-update/retry fix. Check share first -
│      often 20-40% of all churn and the easiest to recover.
├─ Did they ever hit the activation milestone?
│    NO → Did they match the ICP at signup?
│           NO  → (1) BAD FIT - fix qualification upstream
│           YES → (2) POOR ONBOARDING - fix time-to-value
│    YES → Was usage healthy in the last 90 days?
│           NO → Did the champion/sponsor leave or disengage?
│                  YES → (5) CHAMPION LEFT - multi-thread
│                  NO  → (3) LOW ADOPTION - never embedded in workflow
│           YES → Did they cite outcome shortfall?
│                  YES → (4) MISSING VALUE - product/success motion
│                  NO  → Did they move to a rival?
│                         YES → (7) COMPETITOR - product/positioning
│                         NO  → (6) PRICE/BUDGET - economic
```

### Step 4: Build the early-warning system

Churn is decided months before it happens. Score accounts on leading indicators and trigger intervention before renewal, not at it:

- Declining usage or login frequency
- Drop in active seats
- Champion disengagement (no QBR attendance, slow email replies)
- Support escalations or unresolved tickets
- Failure to hit the activation milestone in onboarding

An account flagging two or more indicators enters the at-risk queue.

### Step 5: Build segmented intervention playbooks

- Onboarding churn: structured first-30-day activation program with a clear "aha" milestone.
- Adoption churn: usage nudges, training, executive business reviews to re-anchor value (pair with customer-success-qbr).
- At-risk accounts: a save play - executive outreach, success plan reset, targeted offer if the driver is economic.
- Involuntary churn: dunning, card-update flows, retry logic. Often 20-40% of churn and the easiest to recover - fix this before anything heroic.

Save-offer economics - run the math before offering discounts. A save offer pays only when: (expected additional lifetime margin retained × save rate) > (discount cost + CS time). Rules of thumb: cap discounts at 20-30% for a maximum of 3 months; a healthy save play converts 15-30% of at-risk accounts; a "saved" account that churns within 90 days anyway was a deferral, not a save - track 90-day post-save retention separately. Never train customers that threatening to cancel earns a discount: gate offers to genuinely economic-category churn, never volunteer them in the cancel flow by default.

### Step 6: Prioritize the fix

Rank causes by frequency × revenue at stake × addressability. Fixing onboarding usually beats heroic save plays because it prevents churn at scale rather than rescuing one account at a time. Bad-fit churn is fixed in sales and marketing, not CS - route it upstream.

## Deliverable

Produce a churn analysis containing: retention by cohort (signup month, segment, channel), GRR/NRR with the gap explained, a root-cause breakdown across the seven categories with involuntary churn quantified separately, a leading-indicator early-warning model with trigger thresholds, save-offer economics for the economic segment, and intervention playbooks ranked by frequency × revenue × addressability.

## Metrics to track ongoing

Gross and net revenue retention by cohort; churn by root-cause category; activation rate and time-to-value; save rate on at-risk interventions plus 90-day post-save retention.

## Do NOT

- Do not prescribe from blended churn - averages hide the cohort where the problem lives.
- Do not read NRR alone; expansion from a few accounts can mask a gross-retention leak that GRR exposes.
- Do not treat bad-fit churn as a CS problem - the fix is qualification, and every dollar spent "saving" a bad-fit account is wasted twice.
- Do not offer discounts reflexively in the cancel flow; it trains the base to threaten cancellation and erodes price integrity.
- Do not skip the involuntary-churn check - recovering failed payments is the cheapest 20-40% of the problem.
- Do not count a save as a save until the account clears 90 days post-intervention.

## Quality bar

Ship only when: every churned account in the analysis window has one primary category; cohort cuts exist on all three axes; the early-warning model names concrete trigger thresholds; every recommended save offer has its economics shown; and the top-priority fix is justified by the frequency × revenue × addressability ranking, not by which intervention is most familiar.

## Escalation

Gym and fitness-studio retention has its own economics and playbook - use retention-and-churn-killer. Customers already churned belong to win-back-campaign; account growth belongs to expansion-revenue; recurring QBR motion belongs to customer-success-qbr.
