---
name: cma-narrative-builder
description: Turns a comparative market analysis into a pricing story a seller accepts — defensible comp selection, plain-language adjustments, the overpricing-cost math, and a price-band recommendation aligned to portal search brackets. Use when an agent says "help me present my CMA", "the seller wants to list too high", "how do I justify this price", or "Zillow says their house is worth more". Do NOT use for negotiating the offers that come in after listing — use re-negotiation-prep instead.
---

# CMA Narrative Builder

A CMA is not a spreadsheet problem, it is a persuasion problem: the data almost always supports a number lower than the seller's hope, and the listing dies or thrives on whether the agent can tell the pricing story convincingly enough that the seller prices to the market instead of to their memories. The costly mistake this skill prevents is taking an overpriced listing to keep the seller happy — the property sits, goes stale, and sells below what correct pricing would have fetched, and the agent wears the failure.

Worked example throughout: Priya, a residential agent in a suburban market, 14 transactions last year at a $485,000 average sale price, building toward 24. The subject property is 742 Alder Court — 4-bed, 2.5-bath, 2,340 sq ft colonial, 2022 kitchen renovation, new roof, 0.4 miles to commuter rail (the same worked listing as listing-description-writer). The sellers believe it is worth $525,000 because "Zillow says $521k." The comps support $495,000-$505,000.

## Operating procedure

1. **Select comps by the three rules before touching adjustments.** Selection is where CMAs are won or lost; a weak comp poisons every number downstream, so lock the comp set first. Aim for 3-5 solds plus 2-3 actives (the actives show the competition, not the value).
2. **Adjust each comp to the subject** using the plain-language adjustment method below. Adjust the comp toward the subject, never the reverse.
3. **Run the overpricing-cost math** so the "just try it high for a few weeks" conversation is answered with numbers before it is raised.
4. **Set the price band and snap to the search bracket** — $499,000, not $505,000.
5. **Build the presentation in this order:** comps and their stories first, adjustments second, the market-time math third, the recommendation last. Sellers who hear the number before the evidence argue with the number; sellers who see the evidence first often say the number themselves.
6. **Rehearse the objection scripts,** especially "Zillow says more."

## Inputs to collect

- Subject facts: beds, baths, GLA (gross living area), lot, condition, renovations with year, and anything unusual (busy road, easement, no garage).
- Candidate comps from the MLS: solds within the proximity/recency rules, plus actives and any expireds/withdrawns in the band (expireds are the cautionary tales).
- The seller's number and where it came from (Zillow, a neighbor's sale, a refinance appraisal). Label it: it is an anchor to be moved, not an input to the analysis.
- Local market tempo: median days on market and current list-to-sale ratio for the segment. If unknown, pull it; do not guess.

## Comp selection rules — and how to defend each comp

- **Proximity:** same subdivision or school-attendance boundary first; under 1 mile in suburban markets. Crossing a boundary that changes buyer demand (a highway, a district line) invalidates a comp even at 0.3 miles. Defense line: "Buyers who tour your home also tour this one — that is what makes it a comp."
- **Recency:** closed within 6 months, ideally 3. Beyond 6 months the market has moved and the comp needs a time adjustment, which is one more number to defend. Defense line: "This closed in [month]; the buyer pool that priced it is the same pool shopping now."
- **Similarity:** within ~15 percent on GLA, same bed/bath class, same style and era where the market cares. One big difference is adjustable; three small differences compound into fiction. Defense line: "Same four-bed colonial, same lot class — the only real difference is the kitchen, and we adjust for that."

Rule of thumb: if a comp needs more than 3 adjustments or total adjustments above ~10 percent of its price, drop it and find a better one.

## Adjustments in plain language

Never present a naked adjustment grid. Each adjustment is one sentence: what differs, which direction, and roughly what the market pays for it locally. For 742 Alder Court:

- "The Birchwood Lane sale closed at $492,000, but its kitchen is original 1998 — buyers here have been paying roughly $15,000-$20,000 more for a renovated kitchen, so it supports about $508,000-$512,000 for yours."
- "The Maple Street sale at $510,000 has a three-car garage and a finished basement you don't have; net those out and it supports around $498,000."

Direction check: the comp is inferior → adjust its price up; superior → adjust down. Saying it in words ("theirs has X, yours doesn't, so subtract") prevents the sign errors that shred credibility mid-presentation.

## The overpricing-cost math

The pattern in residential data is consistent: listings that sell in the first weeks sell at or near list; listings that reprice after sitting sell below where correct initial pricing would have landed, because staleness itself becomes a negotiating signal ("what's wrong with it?"). Present it as arithmetic, not opinion.

```javascript
// Overpricing cost model. Edit inputs, then: node overpricing.js
const inputs = {
  supportedPrice: 499000,   // top of the comp-supported band
  aspirationalPrice: 525000, // seller's number
  freshListRatio: 0.99,     // list-to-sale ratio, first-2-weeks sales (local MLS stat)
  staleListRatio: 0.93,     // list-to-sale ratio after a price cut (local MLS stat)
  monthlyCarry: 3100,       // mortgage+tax+insurance+utilities while it sits
  extraMonthsIfStale: 2.5,  // added market time in the stale scenario
}
const i = inputs
const freshNet = i.supportedPrice * i.freshListRatio
const staleNet = i.aspirationalPrice * i.staleListRatio - i.monthlyCarry * i.extraMonthsIfStale
const money = (n) => '$' + Math.round(n).toLocaleString()
console.log('Price right, sell fresh:   ', money(freshNet))
console.log('Price high, cut, go stale: ', money(staleNet))
console.log('Cost of overpricing:       ', money(freshNet - staleNet))
```

Expected output with these inputs:

```
Price right, sell fresh:    $494,010
Price high, cut, go stale:  $480,500
Cost of overpricing:        $13,510
```

Script for the presentation: "Testing $525,000 is not free. If it sits and we cut, the data says stale listings in this market close around 93 percent of list — that path nets you about $13,500 less than pricing right today, plus two and a half more months of carrying costs and showings."

Pull `freshListRatio` and `staleListRatio` from your local MLS stats for the segment; the 0.99/0.93 pair is a typical suburban pattern, not a universal constant — label it a local estimate until verified.

## The price band and the search-bracket rule

Portals bucket searches at round numbers ($475-500k, $500-525k). A $505,000 list price is invisible to every buyer whose search caps at $500,000, and it buys nothing from the $500-525k searcher, who compares it against genuinely bigger houses. Snap to the top of the lower bracket: **$499,000, not $505,000.** For 742 Alder Court the comp-supported band is $495,000-$505,000, so the recommendation is $499,000 — inside the band, maximum search exposure, and positioned as the best-renovated option in the $475-500k bracket rather than the smallest in the next one.

## Objection scripts

**"Zillow says $521,000."** — "Zillow has never been inside your home or the comps. It is a statistical model with a published median error of several percent either way — on a house like yours that error band is wider than our entire pricing decision. What I have that it does not: the Birchwood sale had an original kitchen, the Maple sale had a three-car garage. Those differences are exactly what the algorithm cannot see, and they are worth real money in both directions."

**"Let's just try it high for two weeks."** — Run the overpricing math above, then: "The first two weeks are the only time buyers see you as new. Spending them at a price the data doesn't support burns the launch window on the wrong number."

**"The neighbors got $530k."** — "Let's pull that sale together." (Do it live — it usually differs on GLA, lot, or condition, and letting the seller discover the difference beats telling them.)

## Deliverable

A presentable CMA narrative: 3-5 defended comps each with a one-sentence adjustment story, the overpricing-cost arithmetic with local ratios, a recommended list price snapped to a search bracket ($499,000 for the worked example), and rehearsed scripts for the three standard objections. The listing copy that follows the signed agreement is listing-description-writer's job, using this same worked listing.

## Do NOT

- Do not present the recommended price before the comps — evidence first, number last.
- Do not keep a comp that needs more than 3 adjustments or >10 percent total adjustment; find a better one.
- Do not adjust the subject toward the comp; adjust the comp toward the subject.
- Do not take the listing at the aspirational price with a private plan to "get the reduction later" — the launch window is spent by then, and the reduction conversation is harder than this one.
- Do not trash Zillow — explain what the model cannot see; sellers trust an agent who explains a tool more than one who dismisses it.
- Do not select comps to fit a predetermined number, in either direction. The comp set comes first; the number falls out of it.

## Quality bar

- Every comp passes proximity, recency, and similarity, and has a one-line defense.
- Every adjustment is stated in plain language with direction and a local dollar rationale.
- The recommendation is a band plus a single search-bracket-aligned number.
- The overpricing math uses local ratios or labels them estimates.
- The narrative order is comps → adjustments → market math → number.

## Escalation and compliance

Real estate is a licensed profession. A CMA is a broker price opinion, not an appraisal — say so, and follow your state's rules on BPO labeling. Listing claims must be accurate; fair-housing rules prohibit steering and demographic targeting language — comp defenses describe properties and amenities ("same school-attendance boundary" as a market-boundary fact), never the people in a neighborhood. Legal and brokerage compliance review applies to anything in writing. When a seller lead is not ready to list, keep them warm with seller-lead-nurture, which routes back here when the CMA conversation is earned; once offers arrive, move to re-negotiation-prep.
