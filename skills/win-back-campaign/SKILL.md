---
name: Win-Back Campaign
description: Designs segmented win-back email sequences for dormant users - dormancy tiers, a three-beat sequence with a single proportional incentive, and a suppression rule - and measures success by re-activation, not opens. Use when someone says "our users went quiet", "design a win-back campaign", "how do I re-engage lapsed customers", "should we email people who haven't logged in for months", or lifecycle messaging has stopped landing with a lapsed segment. Do NOT use for recovering an unfinished checkout - use abandoned-cart-sequence instead. Do NOT use for diagnosing and fixing why active customers cancel - use churn-reduction instead. Do NOT use for building always-on onboarding or nurture flows - use email-drip-builder instead.
---

# Win-Back Campaign

A win-back campaign is a structured last attempt to re-engage users who have stopped responding - not a discount blast. Done correctly it surfaces the real reasons users left and converts a meaningful minority back into active customers; done lazily it burns deliverability, trains the list to expect discounts, and buries the churn signal the business needs. The two costly mistakes this skill prevents: treating all dormant users as one segment, and sending a "final email" that turns out not to be final.

## Operating procedure

### Step 1: Gather inputs

Collect before writing anything. Label estimates as guesses.

1. Product usage cadence: daily-use app, monthly subscription, or B2B tool - this sets the dormancy definition.
2. The activity event that counts as "alive" (login, purchase, core action) and the re-activation event that counts as "won back."
3. Segment sizes at each inactivity depth, and rough LTV per segment (sets the incentive ceiling).
4. What has shipped since these users left (fuel for Beat 2).
5. Available incentives: discount, free month, feature unlock, consultation - and what finance will approve.

### Step 2: Define dormancy precisely

Dormancy means different things per product. Set a hard threshold: no login in 30 days for a daily-use app, no purchase in 90 days for a monthly subscription, no activity in 60 days for a B2B tool. The threshold determines segment size and message urgency. "Kind of inactive" is not a segment.

### Step 3: Tier by inactivity depth

Depth of dormancy predicts winnability, so the offer must scale with it:

- Tier 1 - recently lapsed (past threshold up to ~2x threshold, e.g. 30-60 days for a daily-use app): highest win rate. No incentive yet; lead with value and what is new. Discounting this tier trains users to lapse for coupons.
- Tier 2 - cooled (roughly 2x-3x threshold, e.g. 60-90 days): value message plus a modest incentive.
- Tier 3 - cold (beyond ~3x threshold, e.g. 90+ days): the strongest approved incentive, then a genuine goodbye. Win rates here are low single digits; spend accordingly.

Run each tier through the same three-beat structure with tier-appropriate offers.

### Step 4: Structure each tier as three beats

- Beat 1 (day 0): acknowledge the silence without guilt-tripping. Open by naming the gap plainly - the user has not been around lately. Do not pretend nothing happened; users who feel seen re-engage at higher rates than recipients of tone-deaf promotion. Remind them of the specific value they were getting (their data: projects created, money saved), not generic benefits.
- Beat 2 (day 7): show what is new since they left - a feature, an improvement, a piece of content. Give them a reason the product today is not the product they abandoned.
- Beat 3 (day 14): the direct ask with the tier's incentive and a real deadline. This is the final email - apply the honesty rule below.

### Step 5: Apply the final-email honesty rule

If Beat 3 says "this is our last email," it must be true. After Beat 3, suppress non-responders from all marketing sends. A "final" email followed by more emails destroys the credibility that makes final emails work, invites spam complaints, and poisons every future campaign. Honesty here is also the mechanism: a true goodbye is the single highest-open email in most win-back sequences.

### Step 6: Choose one strong incentive, not several

Offer one clear thing - a discount, a free month, a feature unlock, a consultation. Multiple small incentives read as desperation and dilute perceived value. Size it to segment LTV: an incentive worth more than roughly one month of expected margin on a won-back user is buying vanity re-activations.

### Step 7: Measure re-activation, then close the loop

Success is users returning and completing a meaningful action, not opening an email. Track re-activation rate as the primary metric (open and click rates are diagnostics only). Include a one-question "what made you leave?" reply prompt or survey link in Beat 1 - the answers are churn-reduction's raw material. Suppressing non-responders is a win, not a loss: it protects deliverability and keeps the list healthy.

## Worked artifact: three-email sequence, Tier 2 (B2B tool, 60-90 days inactive)

```
EMAIL 1 - day 0 - subject: "You've been missed at [FILL: product]"
  Open: "You haven't logged in since [FILL: date] - no guilt, just noticed."
  Body: one concrete value recap using THEIR data ("your team shipped
        14 reports in your first two months").
  Ask:  one-question reply prompt: "What pulled you away?"
  No incentive.

EMAIL 2 - day 7 - subject: "What's new since you left"
  Body: 2-3 shipped improvements, each one sentence, most relevant first.
  Ask:  "Log back in and see" - deep link straight to the improved feature.
  Incentive: soft - "your data and settings are exactly where you left them."

EMAIL 3 - day 14 - subject: "Last email from us - 1 free month if you're in"
  Body: plain statement that this is the final email and why.
  Offer: ONE incentive - 1 free month on re-activation, expires in 7 days.
  Close: "If now's not the time, no hard feelings - we'll stop emailing."
  After: non-responders suppressed from marketing sends. Actually.
```

Bad Beat 1 opener: "We have exciting news and a special offer just for you!"
Good Beat 1 opener: "You haven't been around since March. Fair enough - here's what you were getting from us, and one question: what changed?"

## Deliverable

Produce a campaign spec containing: the dormancy definition, the three tiers with segment sizes and per-tier offers, the full three-beat copy outline per tier (subjects, openers, asks, incentive, send-day), the suppression rule, and the measurement plan naming the re-activation event.

## Do NOT

- Do NOT send one blast to all dormant users - a 35-day lapser and a 200-day ghost need different messages and different offers.
- Do NOT open with a discount to recently lapsed users; it trains lapsing and skips the cheaper value-first win.
- Do NOT stack multiple small incentives; one strong offer, proportional to segment LTV.
- Do NOT send a fourth email to non-openers; if three beats got nothing, more volume only damages deliverability.
- Do NOT call an email "final" and keep sending - the honesty rule is load-bearing.
- Do NOT report opens as success; only the re-activation event counts.

## Quality bar

The spec ships only when: dormancy has a hard numeric threshold per the product's cadence; every tier has a distinct offer that scales with inactivity depth; Beat 1 names the absence plainly and asks why they left; Beat 3 contains exactly one incentive with a real deadline; the suppression rule is written into the automation, not left as intent; and the primary metric is re-activation rate.

## Adjacent jobs

For recovering an abandoned checkout within hours or days, use abandoned-cart-sequence. For diagnosing why active users cancel and fixing the leak upstream, use churn-reduction (feed it the Beat 1 survey replies). For ongoing lifecycle flows rather than a bounded re-engagement push, use email-drip-builder.
