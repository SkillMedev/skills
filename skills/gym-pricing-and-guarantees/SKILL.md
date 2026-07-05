---
name: gym-pricing-and-guarantees
description: Set a single-location gym's price points and design a risk-reversal guarantee it can actually afford, with margin math so a refund never bleeds the business. Use when a gym owner asks "what should I charge", wants to set or raise a membership or challenge price, asks "is a money-back guarantee safe" or "how risky is my guarantee", or needs guarantee language to put on an offer. Do NOT use when constructing the whole offer (value stack, bonuses, scarcity, naming) — use grand-slam-offer-builder instead; do NOT use when testing whether the price self-funds acquisition — use gym-money-model instead.
---

# Gym Pricing and Guarantees

Price on the value and outcome you deliver — not on your delivery cost or the gym down the road — then bolt on a guarantee that removes the buyer's risk without putting the business at risk. This skill owns the gym's price points and guarantee language only.

## Workflow

### Step 1: Price on value, not cost or competitor
- Anchor high, then justify with the stacked value: a $499 challenge shown next to a list of inclusions worth far more reads as a deal; the same $499 with no framing reads as expensive.
- Never cost-plus. Adding a margin to delivery cost caps the price at your imagination of cost, not the buyer's desire for the outcome.
- Never match the local discount gym. Matching a budget competitor tells the buyer you are the same thing for less and destroys the premium position. A higher price can raise perceived likelihood — people trust that real results cost something.
- If you must move on price, add value or remove an inclusion — never cut the number. See references/pricing-on-value.

### Step 2: Set the three price points
A single-location gym usually runs three tiers. Set each with a target gross margin and a one-line value justification, then fill the price-architecture template with the gym's actual numbers.
- Front-end challenge: priced to clear the 2x cash rule (tested in gym-money-model), commonly $199–$599 by market. Target delivery margin 60–70%. Justification: stacked challenge value vs. price.
- Core membership: the recurring engine, priced on ongoing transformation and community, monthly, commonly $129–$199. Target margin 65–75%. Justification: cost per week of coaching vs. a personal trainer.
- Semi-private / high-ticket: small-group or hybrid coaching for buyers who want more access, commonly $249–$500+ monthly. Target margin 60–70%. Justification: near-personal-training results at a fraction of one-to-one cost.

### Step 3: Choose a guarantee by risk and confidence
A guarantee converts because it moves risk from buyer to seller. Pick the type by fulfillment confidence and how much risk the business can carry. See references/guarantee-types for full mechanics.
- Conditional (safe default): refund or free extension only if the member did their part — minimum sessions attended, meals logged, check-ins made. Low risk because non-doers do not qualify, and the conditions drive the behavior that produces results.
- Unconditional: full refund, no questions, inside a window. Highest conversion, highest risk. Use only when margin is fat and refund rates are proven low.
- Anti-guarantee: no refunds, stated plainly and framed as commitment. Filters for serious buyers. Use on high-ticket where buy-in predicts results.
- Performance: ties payment or continuation to a measurable result ("hit your goal or your next month is free"). Powerful and high-risk; use only with a measurable goal and a confident, tracked system.

### Step 4: Check the guarantee against margin
Before publishing any refundable guarantee, run margin_check.js. It reports the effective margin after expected refunds and the breakeven refund rate — the share of buyers who can claim before the offer stops making money on delivery. The guarantee is safe only when breakeven sits comfortably above any realistic refund rate.

### Step 5: Write the guarantee language and conditions
State the promise, the window, and the exact conditions in plain language using the guarantee-language template. The conditions protect the business and double as the behaviors that create the result. Hand the chosen price points and guarantee to grand-slam-offer-builder for the full offer.

## Quality bar
- Every tier has a target margin AND a one-line value justification — no bare price numbers.
- Every refundable guarantee has been run through margin_check.js and clears breakeven with cushion.
- Guarantee conditions are specific and measurable (counts, dates, metrics), never vague ("do the work").
- The price is justified by value the buyer can see, not by what delivery costs the gym.

## Do NOT
- Do not cost-plus or match a discount competitor to set price.
- Do not publish an unconditional or performance guarantee on thin margin or an unproven fulfillment system.
- Do not discount to close — restate value, offer a payment plan, or remove an inclusion.
- Do not write the value stack, bonuses, scarcity, or offer name here — that is grand-slam-offer-builder.
- Do not claim the price self-funds ad spend — that conclusion belongs to gym-money-model. margin_check.js covers delivery cost only, not acquisition.

## Calculator

Self-contained Node script. Save as `margin_check.js` and run with `node margin_check.js`. No dependencies.

```javascript
// Guarantee margin check. Edit inputs, then: node margin_check.js
const inputs = {
  price: 499,           // what the buyer pays
  fulfillmentCost: 150, // cost to deliver, incurred even if later refunded
  refundRate: 0.10,     // assumed share of buyers who claim the guarantee
}

function check(i) {
  const grossMargin = (i.price - i.fulfillmentCost) / i.price
  // Refunded buyers get the price back but you already spent fulfillment.
  const profitPerSale = i.price * (1 - i.refundRate) - i.fulfillmentCost
  const effectiveMargin = profitPerSale / i.price
  // Breakeven: refund rate where profit per sale hits zero.
  const breakevenRefundRate = 1 - i.fulfillmentCost / i.price
  return { grossMargin, profitPerSale, effectiveMargin, breakevenRefundRate }
}

const r = check(inputs)
const pct = (n) => (n * 100).toFixed(1) + '%'
console.log('Gross margin (no refunds):  ', pct(r.grossMargin))
console.log('Profit per sale at refund:  ', '$' + r.profitPerSale.toFixed(2))
console.log('Effective margin w/ refunds:', pct(r.effectiveMargin))
console.log('Breakeven refund rate:      ', pct(r.breakevenRefundRate))
console.log(
  inputs.refundRate < r.breakevenRefundRate
    ? 'Verdict: guarantee is affordable on delivery cost.'
    : 'Verdict: refunds exceed breakeven, this guarantee loses money.'
)
```

### Worked example output

```
Gross margin (no refunds):   69.9%
Profit per sale at refund:   $299.10
Effective margin w/ refunds: 59.9%
Breakeven refund rate:       69.9%
Verdict: guarantee is affordable on delivery cost.
```

Read it: at a 10% refund rate the challenge still keeps ~60% margin, and you would have to refund roughly 7 in 10 buyers before delivery turns unprofitable. That cushion means the owner can offer a strong guarantee. This covers delivery cost only — keep real refund rates low to protect ad spend, which gym-money-model accounts for separately.

## Template: price-architecture

```
GYM PRICE ARCHITECTURE. [FILL: gym name]

TIER              PRICE        MARGIN TARGET   VALUE JUSTIFICATION (one line)
Front-end         $[FILL]      [FILL]%         [FILL]
Core membership   $[FILL]/mo   [FILL]%         [FILL]
Semi/high-ticket  $[FILL]/mo   [FILL]%         [FILL]

Anchor line for the front-end:
  "[FILL: $X,XXX of coaching and support for $YYY]"
```

## Template: guarantee-language

Three ready scripts. Replace the FILL fields with the gym's numbers.

```
1. CONDITIONAL RESULTS GUARANTEE (safe default)
"Complete the [FILL: 6-week] challenge and follow the plan. Attend at least
[FILL: 16 of 24] sessions, log your nutrition each week, and make every check-in.
If you do all of that and do not [FILL: lose 5% body fat / drop a size], we coach
you free until you do."

2. SATISFACTION GUARANTEE (window-based)
"Train with us for [FILL: 14] days. If you do not love your coaching and your
plan, tell us by day [FILL: 14] and we refund every dollar. No forms, no friction."

3. ATTENDANCE-CONDITIONED GUARANTEE (drives the behavior that works)
"Show up for [FILL: 3] sessions a week for the full [FILL: 6 weeks]. If you hit
that attendance and do not see the result we promised, your next [FILL: month]
is on us."
```

## references/pricing-on-value

Anchoring works because price is judged by comparison, not in isolation. Present the stacked value first, then the price, and the price lands against the value rather than against the buyer's wallet. Premium positioning compounds this: a higher price signals a stronger result and raises perceived likelihood — the exact lever most gyms are weakest on.

Why discounting hurts: a discount teaches the buyer the real value was lower and that waiting earns a better deal. It also attracts the price-shopper, the member most likely to churn. When a prospect pushes on price, restate the value, offer a payment plan, or remove an inclusion — never cut the number. Hold price and let the offer do the work.

## references/guarantee-types

- Unconditional. "Money back, no questions, 30 days." Mechanics: full refund in a window. Risk: highest; a weak fulfillment system or a refund-prone audience can drain cash. Conversion lift: largest. Fit: established gym with proven low refund rate and healthy margin.
- Conditional. "Do the work and get the result, or we keep coaching you free." Mechanics: refund or free extension only when stated actions were met. Risk: low, because the conditions exclude non-doers and create the behavior that produces results. Conversion lift: strong, and it pre-sells the behavior. Fit: the default for most gym challenges.
- Anti-guarantee. "No refunds. This is for the committed." Mechanics: states there is no refund and frames it as a filter. Risk: lowest for the business. Conversion: lower volume, higher-quality buyers. Fit: high-ticket and coaching where commitment predicts results.
- Performance. "Hit the goal or the next month is free." Mechanics: ties continuation or payment to a measurable result. Risk: high if the goal is vague or the system is weak. Conversion lift: very strong when believable. Fit: a confident gym with a measurable promise and tracking.

Match the guarantee to fulfillment confidence: the stronger your system and margin, the bolder the guarantee you can afford. When unsure, ship a conditional guarantee and run margin_check.js to confirm the cushion.
