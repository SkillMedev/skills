---
name: Economic Analysis
description: Applies microeconomic frameworks — supply and demand, elasticity, marginal analysis, market structure, externalities — to a business decision and produces a recommendation with explicit assumptions and a sensitivity analysis showing which assumption flips the answer. Use when someone asks "what happens to revenue if we raise prices", "will profits in this market hold up", "how will competitors respond to this move", or needs the economic logic behind a pricing, entry, capacity, or regulation question. Do NOT use for setting a specific price point or packaging tiers — use pricing-strategy or saas-pricing instead; for putting a dollar figure on market size, use market-sizing instead.
---

# Economic Analysis

Economic reasoning makes the incentives and trade-offs behind a decision explicit — and shows which assumption the whole conclusion hangs on. The costly failure this skill prevents is the confident recommendation built on an unstated elasticity guess: small changes in that one number flip "raise prices" into "lose the market," and nobody notices until revenue does.

## Inputs to collect

1. **The decision**: the specific choice being made (raise price, enter market, add capacity, respond to a rival).
2. **The actors**: buyers, sellers, regulators — and who the analysis is for.
3. **The good or service** and its closest substitutes and complements.
4. **Market structure evidence**: number of competitors, differentiation, entry barriers.
5. **Any real data**: past price changes and volume responses, cost breakdowns, competitor prices. Real data beats framework defaults; where data is absent, state the assumed value and label it a guess.

## Operating procedure

### Step 1: Frame the problem economically

Identify the actors, the good, the market structure (competitive, monopolistic competition, oligopoly, monopoly), and the decision. Write the decision as a comparison of two states of the world, because every economic recommendation is ultimately "A beats B under these assumptions."

### Step 2: Map supply and demand

- List what drives demand (preferences, income, substitutes, complements) and supply (input costs, technology, number of sellers).
- Identify the equilibrium and what shifts each curve.
- Distinguish a movement along a curve (a price change) from a shift of the curve (a determinant change). Conflating these is the single most common error in applied analysis — a price cut that "grew demand" usually just moved along the curve.

### Step 3: Estimate elasticity — and treat it as the pivot assumption

- Price elasticity of demand decides the revenue direction: **inelastic** demand (necessities, few substitutes, small budget share) means raising price raises revenue; **elastic** demand (luxuries, many substitutes) means raising price lowers revenue. The boundary is unit elasticity (|ε| = 1).
- Where possible, estimate from data: percent change in quantity over percent change in price from a past price move. Otherwise anchor on structure — a product with three close substitutes is not inelastic no matter how much the team loves it.
- Check cross-price elasticity (substitutes vs complements) and income elasticity (normal vs inferior goods) when the decision involves adjacent products or a changing customer base.
- Use elasticity to reason about pricing power and revenue, not just unit sales.

### Step 4: Reason at the margin on costs

Separate fixed from variable costs. Decisions compare **marginal revenue to marginal cost**; sunk costs are ignored no matter how large or recent. Watch for economies of scale, and keep average cost out of marginal decisions — "our average cost is $X" answers a different question than "what does one more unit cost."

### Step 5: Model the competitive response

- Assess barriers to entry, competitor count, and differentiation. In competitive markets profits get competed away; durable profit requires a moat (assess it with competitive-moat if that is the real question).
- Run the game-theoretic pass: for any price or capacity move, write the rival's best response and then your best response to that. A move that is only profitable if rivals do nothing is a plan built on hope.

### Step 6: Check for externalities and market failures

Identify costs or benefits not captured in price — pollution, network effects, information asymmetry, public goods. These explain why unregulated outcomes may be inefficient and where regulation, subsidies, or pricing changes bite.

### Step 7: Run the sensitivity analysis (mandatory)

No recommendation ships without it. For each load-bearing assumption — elasticity, rival response, cost per unit, demand-shift size:

- Compute or reason the conclusion at a low, base, and high value of the assumption.
- Identify the **flip point**: the assumption value at which the recommendation reverses. If the flip point sits inside the plausible range of the assumption (e.g., recommendation flips at |ε| = 1.2 and the honest range is 0.8-1.6), say the answer is assumption-dependent and present both branches — do not average them into false confidence.
- Rank assumptions by how close their flip point is to the base case; the closest one is the thing to go measure before committing.

### Step 8: Synthesize into a recommendation

State: the recommended action, the economic logic in two or three sentences, the key assumption it rests on, the flip point, and the observable conditions that would change the answer.

## Worked artifact: sensitivity table

Decision: raise a subscription price from $20 to $24 (+20%).

| Assumption: price elasticity | Volume change | Revenue change | Recommendation |
|---|---|---|---|
| ε = -0.5 (low) | -10% | +8% | Raise |
| ε = -1.0 (base) | -20% | -4% | Marginal — do not raise on revenue grounds alone |
| ε = -1.5 (high) | -30% | -16% | Do not raise |

Flip point: ε ≈ -0.9. The honest elasticity range for a product with two close substitutes spans the flip point, so the recommendation is: run a controlled price test on a segment before rolling out — the analysis cannot decide this from structure alone.

## Deliverable

Produce an economic memo containing: the decision framed as a comparison, the framework analysis (demand and supply drivers, elasticity estimate with basis, marginal cost logic, competitive-response game), the sensitivity table with the flip point marked, and a recommendation naming the one assumption most worth measuring next.

## Do NOT

- Do not conflate movement along a curve with a shift of the curve — misdiagnosing this reverses the causal story.
- Do not include sunk costs in a forward-looking decision, however emotionally expensive they were.
- Do not use average cost where marginal cost belongs.
- Do not present a point conclusion when the flip point sits inside the plausible assumption range — present the branch logic.
- Do not mix positive analysis (what will happen) with normative claims (what should happen) without labeling which is which.
- Do not let ceteris paribus hide that real markets move many variables at once; name the variables held fixed.
- Do not use frameworks to override real data when data is available.

## Quality bar

- Every quantitative assumption is either sourced from data or explicitly labeled a guess with a range.
- The sensitivity analysis exists, covers the load-bearing assumptions, and states the flip point.
- The competitive-response step names the rival's likely move, not just "competitors may react."
- A reader can see exactly which measurement would most improve the decision.
