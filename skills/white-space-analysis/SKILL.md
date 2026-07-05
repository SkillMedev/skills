---
name: White Space Analysis
description: Finds unmet market gaps by ranking jobs-to-be-done on importance versus satisfaction, overlaying them against current solutions and workarounds, and validating that each gap is real, winnable, and worth winning. Use when someone asks "where are the gaps in this market", "what's underserved here", "where should we play next", or is hunting for product, feature, or market-entry opportunities. Do NOT use for profiling competitors and building battle cards - use competitive-intelligence instead; for putting a dollar figure on an identified opportunity - use market-sizing instead; for extracting jobs-to-be-done from raw customer interviews - use jtbd-extractor instead.
---

# White Space Analysis

Find the gaps where customer needs exist but good solutions do not. White space is the intersection of an important, unsatisfied job and weak competitive coverage. The costly failure this skill prevents is the mirage gap: a space that looks empty because incumbents rationally avoided it - no willingness to pay, no economic way to serve it - entered at full cost and exited two years later.

## Inputs to collect

1. **The market and customer segment**, precisely: white space is relative to a specific customer, and a gap for one segment is well-served for another.
2. **Customer evidence**: interviews, surveys, reviews, support tickets, community threads. JTBD must come from research, not imagination - if only internal assumptions exist, label every job "hypothesis" and treat validation as the next step, not a formality.
3. **The known solution landscape**, including in-house tools and manual workarounds the team has seen.
4. **The user's capability base**: what they could credibly build or deliver, since a gap is only an opportunity for someone equipped to fill it.

## Operating procedure

### Step 1: Define the market and customer

Specify the segment and the context of use. Write one sentence: "White space for [segment] trying to [context]." Every later judgment is relative to this sentence.

### Step 2: Map jobs-to-be-done

List the jobs customers are trying to get done - **functional, emotional, and social**. For each job capture the desired outcome and how customers measure success. Then score each job on two axes, 1-10:

- **Importance**: how much it matters to the customer.
- **Satisfaction**: how well current solutions serve it.

Source scores from the evidence; where a score is inferred rather than measured, mark it. The opportunity zone is importance ≥ 7 with satisfaction ≤ 4. Importance ≥ 7 with satisfaction ≥ 7 is a red ocean - incumbents serve it well; avoid competing there head-on.

### Step 3: Map current solutions - including workarounds

Inventory what customers actually use: direct products, adjacent products stretched beyond intent, and manual workarounds ("hiring a spreadsheet" instead of software). For each, note which jobs it addresses and how well. Workarounds are the strongest gap evidence available - a customer maintaining a painful manual process has already proven both importance and dissatisfaction with their own time.

### Step 4: Overlay to find candidate white space

Cross the JTBD scores with the solution map. White space appears where a job is:

- High importance + low satisfaction → underserved, the prime opportunity.
- Solved only by clumsy workarounds.
- Not addressed by any existing solution at all - but treat this pattern with the most suspicion, not the least (see Step 5).

### Step 5: Validate that each gap is real - the incumbent question

A gap is not an opportunity until it survives all four tests:

1. **Size**: the job matters to enough customers to support a business - hand the quantification to market-sizing.
2. **Willingness to pay or switch**: evidence customers would spend money or migration effort, not just agree it is annoying. Strongest signals, in order: customers already paying for a partial solution, customers maintaining a costly workaround, customers who tried to build it themselves.
3. **The incumbent question**: why has nobody filled this? Acceptable answers: incumbents structurally cannot (business-model conflict, channel conflict, technology shift they missed); the gap is newly created by an external change; the segment was previously too small and is growing. Alarming answer: no reason found - assume the gap is a trap until a reason emerges.
4. **Winnability**: the gap persists for a reason the user can overcome with their capability base - a capability answer, not just neglect.

### Step 6: Rank and hypothesize

Rank surviving gaps by importance-satisfaction spread × segment size × winnability. For each, write a one-paragraph hypothesis: why the gap exists, what would fill it, and the cheapest test that would confirm or kill it.

## Worked artifact: JTBD opportunity matrix

Segment: in-house accountants at 50-500 person companies, closing the monthly books.

| Job | Type | Importance | Satisfaction | Current solution | Verdict |
|---|---|---|---|---|---|
| Reconcile intercompany transactions | Functional | 9 | 3 | Spreadsheets + email threads | **White space** - workaround-only, passes incumbent test (ERP vendors' module pricing conflicts with mid-market) |
| Produce the monthly P&L | Functional | 9 | 8 | ERP reporting | Red ocean - avoid |
| Prove to the CFO the close is on track | Social/emotional | 7 | 4 | Ad-hoc status emails | **Candidate** - validate willingness to pay; may be a feature, not a product |
| Archive prior-year workpapers | Functional | 4 | 5 | Shared drive | Ignore - low importance regardless of satisfaction |

Reading: the highest-scoring row wins not because the matrix says so, but because the workaround evidence and a defensible incumbent answer back it. The social job illustrates a common finding - real gap, but possibly monetizable only as part of something larger.

## Deliverable

Produce a white-space report containing: the segment definition sentence, the JTBD-by-importance/satisfaction matrix with evidence sources marked, the solution-and-workaround inventory, the validated white spaces ranked with all four validation tests answered per gap (explicitly including the incumbent answer), and a testable hypothesis per gap with the cheapest confirming experiment.

## Do NOT

- Do not treat an empty space as automatically valuable - size and willingness to pay decide, and "unserved" often means "unservable" (technically or economically infeasible).
- Do not skip the incumbent question; a gap with no explanation for why it persists is a trap wearing an opportunity costume.
- Do not ignore substitutes and workarounds when mapping solutions - they are competition, and ignoring them overstates the white space.
- Do not score importance and satisfaction from internal opinion and present the scores as research; mark inferred scores.
- Do not average away segment differences - a matrix built across mixed segments shows gaps that exist for nobody in particular.
- Do not stop at "high importance, low satisfaction" - that is a candidate list, not a conclusion; validation is the analysis.

## Quality bar

- Every job in the matrix traces to named customer evidence or carries a "hypothesis" flag.
- Every claimed white space has all four validation tests answered, including a specific, credible answer to why incumbents left it open.
- Workarounds appear in the solution inventory, with what they cost the customer.
- Each surviving gap ends in a falsifiable hypothesis and a cheapest-test, so the report leads to action rather than admiration.

## Escalation

Quantify surviving gaps with market-sizing; deepen competitor coverage with competitive-intelligence; if the gap warrants a product bet, design the customer validation study with primary-research.
