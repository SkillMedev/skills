---
name: Competitive Intelligence
description: Map competitors across positioning, product, pricing, proof, and weaknesses, then turn the research into sales-ready battlecards with an update cadence. Use when someone asks "what are our competitors doing", "build a battlecard", "how do we compare to X", "why are we losing deals to X", or before a pricing or positioning decision. Do NOT use for designing long-term defensibility strategy — use competitive-moat instead; for a public comparison landing page use comparison-page-builder; for rep-facing objection scripts beyond the battlecard use objection-handler.
---

# Competitive Intelligence

Competitive intelligence turns scattered impressions of rivals into artifacts sales and product can act on. The costly mistake it prevents is losing winnable deals to a competitor pitch nobody prepared for — or worse, repositioning the whole product around a competitor claim that was never verified. Everything below runs on one discipline: separate fact from inference, and date every claim.

## Operating procedure

### Step 1: Gather inputs

1. The 3–5 competitors that actually show up in deals (ask sales; if pre-sales, the ones prospects mention). Default: cap at 3 — depth beats coverage.
2. Which deals or segments each appears in.
3. Existing win/loss notes, lost-deal reasons, sales call recordings if available.
4. The decision this research feeds: sales enablement, pricing move, roadmap, or positioning. This sets how deep each dimension goes.

### Step 2: Work the source ladder, primary first

Collect in this order and cite each claim's source:

1. Their own properties: homepage, pricing page, docs, changelog/release notes, security page. These yield positioning, features, pricing, and shipping velocity as fact.
2. Review sites (G2, Capterra, TrustRadius): filter to reviews from your ICP's segment; the "dislikes" sections are the closest thing to free win/loss data.
3. Job postings and team pages: what they're hiring for is what they'll ship in two quarters — label this inference.
4. Your own win/loss calls and prospect quotes: the highest-signal source for how they actually pitch against you.
5. Community chatter (forums, social, comparison threads): tie-breaker only; never the sole source for a claim.

### Step 3: Map the five dimensions

For each competitor, capture:

- Positioning: who they say they're for, and the one promise they lead with.
- Product: core capabilities, notable gaps, recently shipped features (from the changelog, with dates).
- Pricing: model (seat, usage, tiered), entry price, what's gated where. If not public, record "unknown — not published" rather than a guess.
- Proof: marquee customers, funding, momentum signals.
- Weaknesses: where they're vulnerable — verified via reviews or lost-deal quotes, never assumed.

Mark every cell F (fact, stated by them or observed directly) or I (inference, your read), and stamp the date collected. Flag unknowns explicitly instead of guessing.

### Step 4: Build the comparison matrix

Rows = competitors, columns = the five dimensions. Keep cells terse — one line each, with the F/I tag and date. The matrix is the internal source of truth; battlecards are views generated from it.

### Step 5: Fill a battlecard per competitor

Use the template below. A battlecard passes only if a rep can absorb it in 90 seconds before a call.

### Step 6: Set the update cadence

Competitive facts go stale fast. Establish on delivery:

- Monthly (15 min per competitor): recheck pricing page and changelog; diff against the matrix.
- Quarterly: full refresh of all five dimensions plus a win/loss review; retire or reverify anything older than 6 months.
- Event-driven: competitor funding round, major launch, or two lost deals citing the same claim triggers an immediate battlecard update.
- Every claim carries its collection date; treat any claim older than 6 months as expired until reverified.

## Battlecard template

```
BATTLECARD: [FILL: competitor]          Updated: [FILL: date]  Owner: [FILL]

WHEN YOU'LL SEE THEM
  Deal profile: [FILL: segments/deal types they appear in]
  Tell: [FILL: the question or phrase a prospect uses that signals they're evaluating them]

THEIR PITCH (how they'll frame it)
  [FILL: their one-line promise, in their words — source: their homepage]

WHERE WE WIN (2-3, honest and provable)
  1. [FILL: advantage] — proof: [FILL: source/customer/number]
  2. [FILL] — proof: [FILL]

WHERE THEY WIN (be honest; reps need to know)
  1. [FILL: their genuine advantage and which buyers it matters to]

LANDMINES (questions to plant that expose their weaknesses)
  - "[FILL: question]" — surfaces: [FILL: verified weakness, source]

OBJECTION HANDLING
  They'll say: "[FILL: likely attack]"
  We respond: "[FILL: response with proof point]"

PRICING SNAPSHOT
  Model: [FILL]  Entry: [FILL]  Gated: [FILL]  (F/I + date: [FILL])
```

Worked contrast for the "Where we win" field — Bad: "Better UX and more modern architecture." (unprovable, sounds like every vendor). Good: "Setup in under 10 minutes vs their 2-week implementation — 14 G2 reviews cite their onboarding time; ours is self-serve."

## Deliverable

Produce two artifacts: a comparison matrix (competitors × five dimensions, every cell tagged fact/inference with source and date) and one battlecard per competitor from the template, plus the written update cadence with an owner. Hand rep training on the cards to sales-enablement-kit.

## Do NOT

- Do not fabricate pricing, customers, or features — "unknown" is a valid and useful cell; a fabricated one poisons every decision downstream.
- Do not present inference as fact; a hiring-page guess dressed as a shipped feature sends product chasing ghosts.
- Do not disparage without a source; reps repeat it, prospects verify it, credibility dies.
- Do not build cards sales never asked for — start from the competitors in live deals.
- Do not let cards rot; a battlecard with claims older than 6 months is worse than none, because reps trust it.

## Quality bar

- Every claim has a source and a date; every cell is tagged fact or inference.
- Weaknesses are verified by reviews or lost-deal quotes, not assumed.
- "Where they win" is non-empty for every competitor — an all-wins card means the research is dishonest.
- A rep can read any battlecard in 90 seconds and name the two landmine questions from memory.
- The update cadence has a named owner and calendar triggers.

## Escalation

Route moat and defensibility strategy to competitive-moat, public comparison pages to comparison-page-builder, whitespace and category-gap analysis to white-space-analysis, and full objection-handling scripts to objection-handler.
