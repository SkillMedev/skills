---
name: Pricing Strategy
description: Set or revise pricing for any product by triangulating value, competition, and cost floor into a recommended structure, price points, and a test plan. Use when someone asks "what should I charge", "how do I price my product", "are we priced too low", "should we go usage-based or flat", or is preparing a repricing or a new-product launch. Do NOT use for designing SaaS tier ladders, value metrics, and expansion packaging in detail — use saas-pricing instead; for gym front-end offers and guarantees use gym-pricing-and-guarantees; for checking whether the resulting price produces healthy CAC payback use unit-economics.
---

# Pricing Strategy

Price is positioning: it tells the market what the product is worth before anyone uses it. The costly mistake this skill prevents is defaulting to cost-plus or copying a competitor's number — pricing is usually the least-tested lever in the business, and underpricing quietly donates margin the founder never gets back. Anchor on the value delivered, then sanity-check against competition and cost.

## Operating procedure

Work the steps in order. Value must be quantified before a structure makes sense, and the structure must be chosen before individual price points mean anything.

### Step 1: Gather inputs

Collect these before recommending anything. Where the user cannot answer, apply the default and label the number a guess.

1. Buyer segments: who buys, split by size, use case, or urgency. Default: assume two segments — a self-serve smaller buyer and a larger buyer with real budget.
2. The measurable outcome per segment: hours saved, revenue gained, cost avoided, risk removed — in the customer's units, not the product's.
3. What the realistic alternatives cost: direct competitors, a spreadsheet, a hire, or doing nothing.
4. Variable cost to deliver one unit (this is only the floor input, never the anchor).
5. The growth vector: does a customer's value grow through more people using it, more volume flowing through it, or more capability unlocked?
6. If already selling: current price, win rate, and how often price objections actually come up.

### Step 2: Quantify willingness to pay by segment

Quantify value in the customer's terms (time saved, revenue gained). "Saves each account manager 10 hours a month" times a loaded hourly cost is a dollar figure; "makes reporting easier" is not. Do this per segment — a freelancer and a 50-person agency capture different value from the same feature, and that difference is what tiers monetize later.

When value cannot be computed directly, run a Van Westendorp price-sensitivity survey on 15 or more real prospects (the four questions: at what price is this too cheap to trust, a bargain, getting expensive, too expensive). The acceptable price range sits between the marginal-cheapness and marginal-expensiveness crossings. Label survey-derived numbers as directional, not exact.

### Step 3: Apply the three lenses

- Value-based: the ceiling and the story. Rule of thumb: charge about one-tenth of the quantified value (the 10x value-to-price rule) so the customer keeps an obvious surplus; 10–25% of value captured is defensible when the value is provable and switching costs are real.
- Competitive: what alternatives charge, and how the product is differentiated. If the value lens supports a price far above competitors and there is no differentiation story to carry it, that is a positioning problem, not a pricing one.
- Cost-plus: the floor only — never price below sustainable margin, and never anchor on cost. Pricing on cost alone leaves value on the table by construction.

### Step 4: Choose the structure to match the growth vector

Per-seat, usage-based, tiered, or flat — match how the customer perceives and grows value:

- Value grows with headcount → per-seat. Simple to budget, but revenue caps with the customer's team size.
- Value grows with volume → usage-based, with a predictability guardrail (committed tiers or caps) so finance can budget it.
- Distinct willingness-to-pay segments → tiered; gate the features that matter to larger buyers, not the features that cost the most to build.
- Value is flat regardless of scale → flat price; compete on simplicity.

Keep the entry point low-friction and create one clear reason to move up. If the recommendation is a multi-tier SaaS ladder, hand the detailed tier design, value metric, and expansion packaging to saas-pricing.

### Step 5: Set the price points

- B2B: round, confident numbers ($50, $200, $1,500). Odd charm prices ($49.99) read as consumer.
- Consumer or self-serve prosumer: charm endings and a visible anchor ("$29, normally $49") pull weight.
- Show the highest tier first or alongside — it anchors everything below as reasonable.
- Keep price gaps between adjacent tiers around 2–2.5x; smaller gaps make upgrades feel pointless, larger ones feel like a cliff.

### Step 6: Write the testing plan

- Test new prices on new customers only; never surprise existing ones.
- Watch the objection rate: if fewer than roughly 20% of deals raise any price objection, the product is underpriced — raise until some deals push back.
- Judge a price test on close rate and 90-day retention together, not signup conversion alone; a cheaper price that attracts churny customers is a loss.
- Recommend exactly one structure with the reasoning, plus the single next test.

## Worked example: SaaS reporting tool for marketing agencies

- Value quantified: each account manager spends ~10 hours/month building client reports; loaded cost $55/hour → $550/month of value per AM.
- 10x rule → ~$55/AM/month ceiling zone; capture 10–25% → $55–$135 per AM is the defensible band.
- Competitive lens: incumbent dashboards run $99–$299/month flat; most agencies use spreadsheets (cost: the 10 hours).
- Cost floor: ~$6/account/month in infrastructure — irrelevant to the anchor, confirms huge margin room.
- Growth vector: value grows with number of client accounts managed, not seats → tiered on client accounts.
- Recommendation: Starter $59/month (5 client accounts), Agency $149/month (20 accounts, white-label reports gated here because larger agencies need them), Scale $349/month (unlimited, API). Round numbers, ~2.4x gaps, entry under the value of two saved hours.
- Next test: raise Agency to $179 for new signups and watch objection rate and 90-day retention.

## Deliverable

Produce a pricing recommendation containing: the quantified value per segment (with guesses labeled), the three-lens readout (ceiling, competitive band, floor), one recommended structure with the growth-vector reasoning, specific price points, and the single next price test with its success metric.

## Do NOT

- Do not price from cost — cost is a floor check, and anchoring there guarantees leaving value uncaptured.
- Do not copy a competitor's price without their positioning; the number only works attached to their story.
- Do not present three structures and let the user choose — recommend one, with reasoning, plus what to test next.
- Do not charge by a metric the customer wants to minimize; it makes success feel like punishment.
- Do not run a price test judged on signup conversion alone; cheap prices convert churn.

## Quality bar

- Value is expressed in the customer's units with a dollar translation, or explicitly labeled as unquantifiable with Van Westendorp as the fallback.
- Every number is sourced or labeled a guess.
- The recommended price sits inside the 10–25%-of-value band or the deviation is justified in writing.
- The structure maps to a named growth vector.
- A testing plan with one concrete next test is included.

## Escalation

This is strategy, not financial advice on the business's viability. For SaaS tier ladders, value metrics, and net-revenue-retention packaging, route to saas-pricing. For gym offers, route to gym-pricing-and-guarantees. To verify the chosen price yields healthy CAC payback and LTV:CAC, route to unit-economics.
