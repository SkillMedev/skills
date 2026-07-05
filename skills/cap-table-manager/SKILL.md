---
name: cap-table-manager
description: Use when a founder needs to understand or model ownership and dilution. Triggers on "model my cap table", "how much will I be diluted", "option pool", "SAFE conversion", "post-money vs pre-money", "what do I own after this round", "pro rata". Turns equity from a black box into a model the founder controls.
---

# Cap Table Manager

Founders lose more value to cap-table mistakes they did not model than to
valuation they negotiated badly. This skill makes dilution legible: you should
be able to state what you own after the next two rounds, and why.

## When to use this skill

Use it during [[fundraising-stage-selector]] to pressure-test dilution, before
signing any SAFE or term sheet, and whenever someone says "we'll just expand the
pool." Pair instrument mechanics with [[safe-vs-priced-round]].

## The mechanics that actually move ownership

### Pre-money vs post-money
- Post-money valuation = pre-money + new investment.
- Investor ownership = investment ÷ post-money. ($2M into $8M pre => $10M post
  => 20% to investors.)
- Modern SAFEs (post-money SAFEs) lock the investor's percentage; founders
  dilute *around* them when later money comes in. Know which kind you signed.

### The option pool shuffle
- Investors usually require the new option pool to be carved out of the
  *pre-money* — so founders, not investors, bear its dilution.
- A "20% pool on a $10M post" can quietly cost founders several points. Model
  the pool inside the pre-money and negotiate its size to the actual hiring plan
  (see [[fundraise-team-hiring]]), not a round-number default.

### SAFE stacking
- Multiple uncapped or high-cap SAFEs feel free now and convert into a painful
  chunk at the priced round. Always model the *fully converted* table, not the
  cash-in-hand one.

## Build the model

1. Start with the current fully-diluted table: founders, existing options,
   prior SAFEs/notes (at their caps).
2. Layer the new round: investment, pre/post, new pool top-up.
3. Convert every SAFE at this round's terms.
4. Read off post-round ownership for each row and the founder total.
5. Project one round further at plausible terms to see the trajectory.

## Guardrails

- Keep founders meaningfully above 50% through seed if at all possible; the math
  gets unforgiving by Series A.
- A round selling >25% is a yellow flag — too dilutive or underpriced.
- Watch liquidation preference and participation; they change *value* even when
  they don't change *percentage*. See [[term-sheet-negotiation]].

## Anti-patterns

- Modeling the cash table instead of the fully-converted one.
- Accepting the pool top-up as fixed instead of sizing it to the hiring plan.
- Signing stacked SAFEs without ever modeling their combined conversion.

## Deliverable

A fully-diluted cap table for today, post-round, and one round out, with the
founder ownership trajectory, the pool's true cost, and a flagged list of any
term that moves value without moving percentage.
