---
name: Due Diligence Checklist
description: Runs structured diligence on an investment or acquisition target across four workstreams — commercial, financial, technical, and legal — and produces a red-flag register split into deal-killers versus manageable risks with a go/no-go recommendation. Use when someone asks "what should I check before investing", "run diligence on this company", "is this deal clean", or is preparing to wire money into a startup or acquisition. Do NOT use for decoding the clauses of a term sheet — use term-sheet-explainer instead; for negotiating terms, use term-sheet-negotiation; for building the target's data room from the company side, use data-room-builder.
---

# Due Diligence Checklist

Diligence exists to disconfirm the thesis, not confirm it: find the reason not to invest before the money moves. The costly mistake it prevents is confirmation bias — founders are persuasive, decks are polished, and the investor who only looks for supporting evidence reliably finds it. Enter assuming the deal is flawed and go hunting for the flaw; diligence is where skepticism is earned.

## Operating procedure

Run the four workstreams in this order. Commercial comes first because a broken market thesis makes the other three moot; legal comes last because its landmines are cheapest to check once the deal still looks alive.

### Step 1: Gather inputs

Collect before starting, and label every unverified founder claim as a claim:

1. The written investment thesis — one paragraph stating why this deal wins. Every finding gets tied back to it.
2. The data room or document set: financials, bank statements, cap table, material contracts, customer list.
3. Access for 3-5 customer reference calls (default: the investor picks the names, not the founder).
4. Deal type and check size — depth should scale with the check; a small angel check gets the killer questions, not all four workstreams at full depth.
5. Timeline and any exclusivity clock already running.

### Step 2: Commercial diligence — the market and the right to win

- Market: validate the TAM bottom-up (route to market-sizing), confirm the growth rate, and pressure-test timing — why now.
- Customers: talk to real ones. Why did they buy, would they recommend it, what would make them leave? Reference calls are the single most informative step in all of diligence; never skip them.
- Competition: who else solves this, and specifically why this company wins.
- Moat: what protects the business as it scales (route to competitive-moat).
- Unit economics: CAC, LTV, and payback validated from raw data, never from the deck.

### Step 3: Financial diligence — the numbers behind the story

- Reconcile reported revenue to bank statements and signed contracts. Confirm it is real, recurring, and recognized correctly. Revenue that will not reconcile to cash is a deal-killer, full stop.
- Quality of revenue: customer concentration (flag any single account above ~25% of revenue), churn, contract length, one-time versus recurring mix.
- Burn rate and runway from the actual cash position, not the model.
- Forecast assumptions: grounded in pipeline and cohort history, or aspirational?
- Cap table: ownership, option pool size, any unusual preferences or convertible debt.

### Step 4: Technical diligence — for technology targets

- Architecture scalability and the single largest technical risk.
- Code and infrastructure health; tech debt that will slow the stated roadmap.
- Security and data-handling posture; compliance exposure on privacy and regulated data.
- Engineering depth and key-person risk — if one departure halts the roadmap, price that in.
- IP ownership: confirm all code is assigned to the company.

### Step 5: Legal diligence — the structural landmines

- Clean incorporation and a cap table that matches the company's records.
- IP assignments signed by every founder, employee, and contractor — this is a frequent gap and can sink a deal on its own.
- Material contracts and any change-of-control clauses that fire on this transaction.
- Litigation, regulatory issues, outstanding liabilities.
- Employment compliance and any live or historical co-founder disputes.

### Step 6: Synthesize

Score each workstream pass / concern / fail. List every red flag and sort each into exactly one of two buckets:

- Deal-killers: fraud, revenue that will not reconcile, broken or contested cap table, unsigned IP with an uncooperative counterparty, no real moat in a commodity market.
- Manageable risks: fixable with a closing condition, a board covenant, an escrow/holdback, or a priced-in discount.

If a red flag cannot be confidently placed in either bucket, treat it as a deal-killer until resolved. Then answer the only question that matters: does the original thesis still hold?

## Red-flag register template

```
DILIGENCE RED-FLAG REGISTER — [FILL: target] — thesis: [FILL: one line]

#  WORKSTREAM   FINDING                          EVIDENCE            BUCKET        REMEDY / CONDITION
1  [FILL]       [FILL]                           [FILL: doc/call]    Deal-killer   n/a — walk unless resolved
2  [FILL]       [FILL]                           [FILL]              Manageable    [FILL: covenant/escrow/condition]
...

VERDICT: GO / GO WITH CONDITIONS / NO-GO
Thesis status: HOLDS / HOLDS WITH REVISION / BROKEN — [FILL: one sentence why]
```

Worked lines: "Financial — $310k of reported ARR is a signed LOI, not a contract — bank recs + contract review — Deal-killer unless converted to signed contract before close." "Legal — two early contractors never signed IP assignment — assignment audit — Manageable: closing condition that both sign before wire."

## Deliverable

Produce a diligence report containing: findings by workstream with a pass/concern/fail score, the red-flag register split into deal-killers versus manageable risks (each manageable risk paired with its remedy), the validated unit economics, and a go / no-go recommendation tied explicitly back to the original investment thesis.

## Do NOT

- Do not take deck numbers as findings; every material number must reconcile to a primary source (bank statement, contract, raw data).
- Do not let the founder curate the customer reference list — hand-picked references are a screened sample.
- Do not treat evasion as noise. When a founder is evasive on a specific topic, follow the evasion; it is the highest-signal thread in the deal.
- Do not blend deal-killers and manageable risks into one undifferentiated "risks" list — the whole decision rides on that split.
- Do not keep diligencing to justify sunk effort after a deal-killer is confirmed; the correct output of good diligence is often no-go.

## Quality bar

The report ships only when: every red flag cites its evidence source; every manageable risk has a named remedy; unit economics were rebuilt from raw data; at least three customer references were spoken to (or the gap is flagged prominently); and the recommendation explicitly states whether the thesis holds.

## Escalation

This is not legal, tax, or investment advice. Bring in transaction counsel for legal diligence and a QoE (quality of earnings) firm for financial diligence on any deal large enough to warrant the fee. For understanding the term sheet the deal produces, route to term-sheet-explainer; for negotiating it, term-sheet-negotiation.
