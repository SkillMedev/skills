---
name: SERP Gap Analyzer
description: Compares a draft or outline against the pages already ranking for its target query and outputs the missing subtopics, entities, and questions as a prioritized gap table — each gap classified must-have, differentiator, or skip. Use when someone asks "compare my draft against the top ranking pages", "what is my draft missing compared to the pages that rank", "run a content gap analysis on my draft", or has a target query plus 3-5 ranking URLs and wants to close coverage gaps before publishing. Do NOT use for a page that already ranks and is decaying over time — use content-refresh-auditor. Do NOT use for rewriting sections into citable answer blocks for AI engines or featured snippets — use aeo-answer-blockifier. Do NOT use for grouping a keyword list into page-level topics — use keyword-cluster-builder.
---

# SERP Gap Analyzer

Compare a draft against the pages that already rank for its target query and report which subtopics, entities, and questions it omits — ranked by whether closing each gap is worth it. The costly mistake this prevents is publishing a page that reads well but silently skips the coverage the query demands, then wondering why it stalls on page two: the ranking pages collectively define what "complete" means for this query, and a draft that ignores that consensus competes blind.

## Operating procedure

Order matters: the coverage map (step 2) feeds entity and question extraction, and classification (step 6) is meaningless before consensus is counted.

### Step 1: gather inputs

Collect before analyzing. If the ranking URLs are not supplied, ask for them — never invent rankings or competitor content.

- The target query (one query; a second query means a second analysis).
- The draft or its outline. An outline is acceptable; depth assessment (step 5) will be presence-only.
- 3-5 URLs currently ranking for the query. Default to the top organic results, excluding ads and aggregator pages that serve a different intent.
- The draft's target intent (informational, comparison, transactional). If unstated, infer it from the query and label the inference as a guess.

### Step 2: build the coverage map

Pull the H2/H3 outline from each ranking URL. Aggregate into one master subtopic list. Count consensus per subtopic: covered by a majority of competitors (3+ of 5, or 2+ of 3) = table stakes; covered by exactly one = optional differentiator signal.

### Step 3: extract entities and salient terms

List the named entities competitors reference — tools, standards, people, brands, specs, related concepts. Flag entities present across multiple competitors but absent from the draft. Topical depth comes from the right co-occurring entities, not keyword density.

### Step 4: mine the questions

Collect People Also Ask entries, FAQ blocks, and question-form subheads across the ranking pages. Each is a query the draft could also satisfy. Flag questions no competitor answers well — those are the strongest additions, because they win coverage no ranking page currently owns.

### Step 5: assess depth, not just presence

For subtopics the draft already touches, compare treatment depth against competitors. Rule of thumb: if the median competitor gives a subtopic several hundred words or a dedicated section and the draft gives it a sentence, that is a depth gap even though the topic "appears". Note format gaps too — comparison tables, step lists, or original data competitors use and the draft lacks.

### Step 6: classify every gap and prioritize

Label each gap exactly one of:

- **must-have** — majority consensus AND on-intent. Not closing it caps the page.
- **differentiator** — covered by one or zero competitors but adds unique, on-intent value (especially unanswered questions from step 4).
- **skip** — off-intent, tangent, or affiliate filler, regardless of who covers it.

Order the output: must-haves first (presence gaps before depth gaps), then the two or three highest-value differentiators. Everything else is explicitly skipped with a reason.

## Gap table template

Copy and fill one row per gap:

```
QUERY: [FILL]        DRAFT: [FILL: url or doc]        INTENT: [FILL]
COMPETITORS: [FILL: url1] [FILL: url2] [FILL: url3] [FILL: url4] [FILL: url5]

| # | Gap (subtopic / entity / question) | Type            | Found in      | Consensus | Class          | Action                     |
|---|------------------------------------|-----------------|---------------|-----------|----------------|----------------------------|
| 1 | [FILL]                             | presence        | [FILL: urls]  | [FILL]/5  | must-have      | add section, ~[FILL] words |
| 2 | [FILL]                             | depth           | [FILL: urls]  | [FILL]/5  | must-have      | deepen from [FILL] to [FILL] |
| 3 | [FILL]                             | question        | PAA / [FILL]  | [FILL]/5  | differentiator | answer in [FILL] section   |
| 4 | [FILL]                             | format          | [FILL: urls]  | [FILL]/5  | differentiator | add [FILL: table/steps]    |
| 5 | [FILL]                             | entity          | [FILL: urls]  | [FILL]/5  | skip           | off-intent: [FILL: why]    |
```

Filled example rows for the query "cold brew ratio":

```
| 1 | Ratio for concentrate vs ready-to-drink | presence | url1,url2,url4 | 3/5 | must-have      | add section, ~250 words          |
| 2 | Steep time and temperature              | depth    | url1,url3,url5 | 3/5 | must-have      | deepen from 1 line to ~200 words |
| 3 | "Why is my cold brew bitter?"           | question | PAA, none answer well | 0/5 | differentiator | answer as its own H2      |
| 4 | Ratio conversion table (cups/liters)    | format   | url2           | 1/5 | differentiator | add table                        |
| 5 | History of cold brew in Japan           | entity   | url5           | 1/5 | skip           | off-intent for a how-to query    |
```

## Deliverable

Produce the filled gap table plus an ordered action list ("add X, deepen Y, answer Z") where every action names the gap, its class, its competitor source, and the concrete change — not a description of competitor pages.

## Do NOT

- Do not duplicate competitor structure verbatim; matching every heading produces a derivative page that adds nothing and gives the engine no reason to prefer it.
- Do not pad to hit a word count — length is a symptom of coverage, not a goal.
- Do not recommend closing gaps that serve a different intent than the target query; an off-intent section dilutes relevance even when a competitor has it.
- Do not analyze from memory. Every gap must cite a specific supplied URL; if the URLs were not provided, stop and ask.
- Do not treat a one-competitor topic as table stakes; consensus is a count, not a feeling.

## Quality bar

- Every gap row is tied to at least one specific competitor source with a consensus count.
- Presence gaps, depth gaps, format gaps, and question gaps are distinguished, not lumped.
- Every gap carries exactly one class (must-have / differentiator / skip) and skips carry a stated reason.
- The action list is executable as written: a writer could close every must-have without asking a clarifying question.
