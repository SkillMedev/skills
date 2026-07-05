---
name: Term Sheet Explainer
description: Decodes every clause of a VC term sheet into plain English, marks each term founder-favorable, market-standard, or investor-favorable, and flags deviations in priority order. Use when a founder asks "what does liquidation preference mean", "is this term sheet normal", "explain participating preferred", "what is the option pool shuffle", or receives a term sheet and needs to understand it before responding. Do NOT use for planning counters, trades, and negotiation strategy - use term-sheet-negotiation instead; do NOT use for choosing between a SAFE and a priced round - use safe-vs-priced-round instead.
---

# Term Sheet Explainer

A term sheet is non-binding but path-dependent - what gets signed here sets the template for every future round. The costly mistake this skill prevents is signing terms the founder never actually understood: fixating on the valuation headline while participating preferred or an investor-controlled board rides through unread. This skill translates the jargon and grades each term; the negotiating itself belongs to term-sheet-negotiation.

## Operating procedure

1. Collect the inputs below before explaining anything - a term reads differently at pre-seed than at Series A.
2. Sort every term in the sheet into one of two buckets: **economics** (who gets what money) or **control** (who decides what). Economics is negotiated for fairness; control is negotiated for survival. Founders who only fight over valuation often give away the company.
3. For each term, produce three things: the plain-English meaning, the market-standard position for this stage, and this sheet's position graded founder-favorable / standard / investor-favorable.
4. Run the red-flags checklist. Any hit goes to the top of the output.
5. Assemble the clause-by-clause table (deliverable below), flag deviations in priority order, and hand it to term-sheet-negotiation for the counter strategy. Model ownership impact in cap-table-manager.

### Step 1: gather inputs

- The term sheet itself (or the specific clauses in question).
- Stage of the company (default seed if unstated).
- Whether the founder has competing offers - this changes which deviations are worth flagging as urgent.
- Current cap table basics: founder ownership, existing pool, prior SAFEs or notes.
- Label any stage or ownership figure the founder estimates rather than knows as a guess.

## Economics terms

- **Valuation (pre vs post)**: post-money = pre-money + investment. Always confirm which the sheet quotes; option pool sizing hides in this distinction.
- **Option pool shuffle**: a pool created pre-money dilutes founders only, not investors. Investor asks of 15-20% are common; 10-15% sized against an actual hiring plan is the defensible range. A pool sized to a round number instead of a hiring plan is founder dilution disguised as housekeeping.
- **Liquidation preference**: who gets paid first in a sale, and how much. 1x non-participating is standard and founder-fair. Participating preferred ("double dip" - the investor takes their money back *and* their ownership share) and multiples above 1x are red flags in normal markets.
- **Anti-dilution**: protection if the next round prices lower. Broad-based weighted average is the standard; full-ratchet reprices the investor's entire stake to the down-round price and punishes founders brutally.
- **Pro-rata rights**: the investor's right to maintain ownership percentage in future rounds. Standard for leads. Watch for super pro-rata, which lets them take a disproportionate share of the next round.

## Control terms

- **Board composition**: at seed, a 2-1 founder-favorable board is healthy. A balanced or investor-controlled board this early is a warning sign.
- **Protective provisions**: the list of actions requiring investor consent. Normal to cover selling the company or issuing senior stock; abnormal to cover hiring decisions or annual budgets.
- **Drag-along**: forces minority holders to join a sale approved by the majority. Confirm the approval threshold and whether common stock gets a say.
- **Founder vesting**: re-vesting the founder's own shares is common. The terms to check: credit for time already served, and acceleration on termination-without-cause and on acquisition (single trigger fires on the acquisition alone; double trigger requires acquisition plus termination - double trigger is the common ask).
- **Redemption rights**: an investor right to force the company to buy back their shares on a timeline. Rare at early stage and a red flag - it converts equity into debt-like pressure.

## Worked artifact: a term explained, good vs bad

**Term: liquidation preference.** Plain English - if the company sells, this decides who is paid first and how much before common shareholders see anything.

Bad (investor-favorable): "2x participating preferred." On a $3M investment and a $20M exit, the investor takes $6M off the top, *then* also takes their ownership percentage of the remaining $14M. On modest exits this can leave founders and employees with a fraction of what the headline valuation implied.

Good (market standard): "1x non-participating." The investor chooses either their $3M back or their ownership percentage of the $20M - whichever is greater, never both. Founders and employees keep the economics the cap table promises.

## Red flags checklist

- Full-ratchet anti-dilution (vs broad-based weighted average, the standard).
- Redemption rights forcing buyback on a timeline.
- Liquidation multiple above 1x, or participating preferred.
- Investor board control at seed.
- Protective provisions reaching into operations (hiring, budgets).
- Super pro-rata rights on the lead's ownership.

## Deliverable

Produce a clause-by-clause table with five columns: term, plain-English meaning, market-standard position for this stage, this sheet's position, and a recommended ask. Grade every term founder-favorable / standard / investor-favorable, and list deviations from standard in priority order - control red flags first, economics second, everything else last.

## Do NOT

- Do not grade a term without stating the stage context; a balanced board is a red flag at seed and normal at Series B.
- Do not let the valuation headline set the tone of the whole review - clean terms at a fair price beat bad terms at a high price.
- Do not summarize clauses the founder did not ask about while skipping ones present in the sheet; every material term in the document gets a row.
- Do not slide into negotiation scripting - which trades to offer and how to counter is term-sheet-negotiation's job, and mixing the two produces a muddled artifact.

## Quality bar

The table is done when: every material term in the sheet has a row; each plain-English cell is understandable by someone who has never raised; each grade cites the market-standard position it deviates from; red-flag items appear first; and no cell contains jargon that itself needs explaining.

## Escalation

This is education, not legal advice. The binding documents that follow the term sheet run to dozens of pages and always warrant a startup-specialist lawyer - the fee is trivial against the cost of one bad term. Route counter strategy to term-sheet-negotiation and ownership modeling to cap-table-manager.
