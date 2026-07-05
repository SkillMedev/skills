---
name: safe-vs-priced-round
description: Use when a founder is choosing how to structure a raise. Triggers on "SAFE vs priced round", "should I use a SAFE", "valuation cap vs discount", "post-money SAFE", "convertible note", "when to do a priced round", "MFN clause". Explains the instruments and their real dilution consequences.
---

# SAFE vs Priced Round

The instrument you raise on decides how much you dilute, when, and how much
control you trade — often more than the headline valuation does. Founders who
treat the SAFE as "free money now" get a nasty surprise at the priced round.
This skill makes the choice deliberate.

## When to use this skill

Use it during [[fundraising-stage-selector]] when sizing the round and before
signing anything. Model every choice through [[cap-table-manager]] so the
dilution is real, not abstract.

## The instruments

### SAFE (Simple Agreement for Future Equity)
- No interest, no maturity, converts to equity at the next priced round.
- Fast (signable in days, legal cost near zero on standard docs), cheap,
  standard (Y Combinator post-money SAFE). Dominant at pre-seed and seed.
- **Post-money SAFE**: the investor's percentage is fixed at signing; *founders*
  absorb the dilution of later SAFEs and the new-round pool. This is the common
  form — know that you dilute around it.
- **Pre-money SAFE** (older): SAFE holders dilute each other; murkier math.

### Convertible note
- Like a SAFE but it is debt: carries interest (commonly 2-8%) and a maturity
  date (commonly 18-24 months). Rare outside specific situations; the maturity
  date is a real risk if the next round slips.

### Priced equity round
- You sell shares at an agreed valuation now. Sets a real price, a board seat,
  and full terms (preferences, protective provisions). More legal cost and time —
  typically several weeks to close and tens of thousands of dollars in combined
  legal fees, versus days and near-zero for a standard SAFE.
- Standard at Series A; also common at larger seeds.

## The SAFE terms that decide your dilution

- **Valuation cap**: the maximum valuation at which the SAFE converts. Lower cap
  = more dilution for you, more upside for the investor. The most negotiated
  term. Quick check: on a post-money SAFE, amount raised ÷ post-money cap = the
  percentage you just sold ($500k on a $5M cap is 10%, full stop).
- **Discount**: a percentage off the next round's price (typically 10-20%).
- **Cap vs discount**: investors take whichever is better for them at
  conversion. Model both.
- **MFN (most-favored-nation)**: a SAFE holder gets the best terms you later
  grant. Stacking MFN SAFEs can quietly ratchet up everyone's terms.

## Choosing

- **SAFE** when: speed matters, you are pre/early-seed, you have strong investor
  demand and want to avoid setting a price too early.
- **Priced round** when: you have a lead writing a large check, you want a clean
  cap table and a real valuation, or stacked SAFEs are getting hard to model.

Watch the **stacking trap**: several uncapped or high-cap SAFEs feel painless
until they all convert at the Series A and reveal you sold more than you thought.
Always model the fully-converted table before signing the next one. Practitioner
red lines: keep cumulative SAFE dilution before the priced round under roughly
20-25%; if the stack is heading past that, or a single round would sell more
than ~25%, it is time to price the round instead of adding another SAFE.

## Worked example: the stack that ate 31%

A founder raises three post-money SAFEs over 18 months, each feeling small:

- $500k on a $5M post-money cap → 10.0%
- $750k on a $7.5M cap → 10.0%
- $1M on a $9M cap → 11.1%

No shares have moved, so the cap table still "looks" clean. But 31.1% of the
company is already spoken for. At the Series A the lead takes 20% and requires
a 10% option pool — carved out pre-money, so it also comes from the founders.
Fully converted, the founders wake up below 45% before the B, having believed
they were "only giving up 10% at a time."

The fix, applied in time: after the second SAFE (20% committed), the correct
move was to price the third raise. A $1M-plus check with 20% already stacked is
exactly the "stacked SAFEs are getting hard to model" trigger above — and a
priced round would have set the pool with the investors sharing the dilution
conversation openly, instead of it landing silently on the founders.

## Anti-patterns

- Raising on a cap so low it craters your ownership at conversion.
- Treating SAFE money as non-dilutive because no shares moved yet.
- Stacking SAFEs without modeling combined conversion — especially past the
  ~20-25% cumulative line.
- A convertible note whose maturity hits before your next round.

## Quality bar

- Every SAFE in the stack is converted in the model — none treated as "not real
  dilution yet."
- The fully-converted table includes the next round's option pool, carved where
  the term sheet actually carves it.
- Both cap and discount conversion are modeled, with the investor taking the
  better of the two.
- The recommendation names specific terms to seek and resist, not just an
  instrument.
- Founder ownership after the modeled priced round is stated as a single number.

## Deliverable

A recommendation (SAFE / note / priced) with the reasoning for this stage, the
specific terms to seek and to resist (cap, discount, MFN), and a fully-converted
dilution model for the chosen path. Carry terms into [[term-sheet-negotiation]].
