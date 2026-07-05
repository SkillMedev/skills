---
name: Patent Prior Art
description: Runs a systematic prior-art search — feature decomposition into claim elements, CPC/IPC classification plus keyword vocabulary, patent databases AND non-patent literature, forward and backward citation walks — and maps results into claim charts with a novelty assessment and a documented search log. Use when someone asks "is my invention novel", "has anyone already patented this", "find prior art against this claim", or is deciding whether a filing or an invalidity argument is worth pursuing. This is research, not legal advice; filing and freedom-to-operate decisions require a patent attorney. Do NOT use for a general academic literature survey unrelated to patentability — use literature-review instead.
---

# Patent Prior Art

Assess whether an invention is novel and non-obvious by finding everything that already discloses it. The costly failure this skill prevents is the false-clear search: keywords-only, English-only, patents-only searching that misses the conference paper or Japanese utility model that later kills the application — after the filing fees, or worse, during litigation. This is a search-and-map task, not a legal opinion.

## Inputs to collect

1. **The invention disclosure**: what it does, how it works, and what the inventor believes is new.
2. **The priority date** (or intended filing date) — everything published on or after it is irrelevant, and date discipline is absolute.
3. **The field and its jargon**: how practitioners, not just patent drafters, name these concepts.
4. **Known competitors and prior products** in the space — likely assignees to search.
5. **Search depth**: quick patentability screen (top databases, hours) vs thorough pre-filing search (all sources below, days). Default to thorough if the user will spend money on the outcome.

## Operating procedure

### Step 1: Deconstruct the invention into claim elements

Break the invention into its essential technical features — the problem solved, the mechanism, and what is allegedly new. Separate the novel core from conventional surrounding components; searching the conventional parts wastes effort, and missing the core invalidates the search. Write each element as one row of the eventual claim chart.

### Step 2: Build the search vocabulary

- For each element, list synonyms, formal technical terms, and layperson terms — patent drafters deliberately use unusual wording, so keyword search alone systematically misses.
- Map elements to classification codes: **CPC and IPC**. Classification search catches documents that use entirely different words for the same concept; a search without a classification pass is incomplete by construction.
- List likely assignees and named inventors active in the field for targeted searches.

### Step 3: Search systematically — patents AND non-patent literature

Scope rule: any public disclosure anywhere, in any language, before the priority date counts as prior art. A patents-only search is not a prior-art search.

- **Patent databases**: Google Patents, Espacenet, USPTO (Patent Public Search), WIPO PATENTSCOPE. Espacenet and PATENTSCOPE cover foreign filings — do not stop at US results.
- **Non-patent literature (NPL)**: academic papers (Google Scholar, IEEE, arXiv), conference proceedings, standards documents, product manuals, datasheets, theses, and archived web pages (Wayback Machine). For software and fast-moving fields, NPL is where the killing reference usually lives.
- Run three query types and combine results: keyword queries, classification queries, and **citation walks** — for each strong hit, follow its backward citations (what it built on) and forward citations (what built on it).
- Log every query, database, and date in the search log as you go — an undocumented search cannot be defended or extended later.

### Step 4: Screen and map into claim charts

For each candidate reference, build a claim chart: invention elements in rows, one column per reference, each cell marked disclosed / partially disclosed / not disclosed, with a pinpoint cite (column and line, paragraph, or figure). A single reference disclosing **all** elements defeats novelty; a plausible **combination** of references may defeat non-obviousness.

### Step 5: Assess novelty and obviousness

- **Novelty**: is there one prior document disclosing every essential element? Name it.
- **Non-obviousness / inventive step**: would combining known references be obvious to a person skilled in the art? Note the motivation to combine (same problem, same field, explicit suggestion) and any unexpected results cutting the other way.
- Record the publication date of every reference and verify it precedes the priority date — a disclosure one day after the priority date is not prior art, and an assumed date is not a verified date.

### Step 6: Report — including the negative result

Deliver the feature breakdown, the complete search log, the top references with claim charts, and a novelty assessment with confidence. List the strongest single reference and the strongest combination **separately** — they answer different legal questions. "No blocking art found in the scope searched" is a valid, documented outcome; state the scope so its limits are visible.

## Worked artifact: search log template

Copy and fill one row per query as the search runs.

```
PRIOR-ART SEARCH LOG
Invention: [FILL: one-line description]
Priority date: [FILL: YYYY-MM-DD] — only disclosures BEFORE this date count
Searcher: [FILL]   Search depth: [FILL: screen / thorough]

CLAIM ELEMENTS
  E1: [FILL: essential feature 1]
  E2: [FILL: essential feature 2]
  E3: [FILL: add rows as needed]

CLASSIFICATION CODES CONSULTED
  CPC: [FILL: e.g. G06F 16/903]   IPC: [FILL]

QUERY LOG
| # | Date run | Database | Query / class / citation walk | Hits reviewed | Kept |
|---|----------|----------|-------------------------------|---------------|------|
| 1 | [FILL]   | [FILL]   | [FILL: exact query string]    | [FILL]        | [FILL: ref IDs] |

REFERENCES KEPT
| Ref | Type (patent/NPL) | Pub. date | Before priority? | Elements disclosed | Pinpoint cites |
|-----|-------------------|-----------|------------------|--------------------|----------------|
| R1  | [FILL]            | [FILL]    | [FILL: yes/no]   | [FILL: E1, E3]     | [FILL]         |

STRONGEST SINGLE REFERENCE: [FILL: ref + which elements it covers/misses]
STRONGEST COMBINATION: [FILL: refs + motivation to combine]
SCOPE LIMITS: [FILL: languages, databases, or NPL sources NOT searched]
```

## Deliverable

Produce a prior-art report containing: the claim-element breakdown, the filled search log (every query, database, and date), claim charts for the top references with pinpoint citations, the strongest single reference and strongest combination stated separately, a novelty/obviousness assessment with confidence, and the explicit scope limits of the search.

## Do NOT

- Do not search patents only — NPL, product documentation, and archived web pages are equally fatal prior art, and in software usually more so.
- Do not search English only — foreign-language disclosures count in full; use Espacenet and PATENTSCOPE machine translation rather than skipping them.
- Do not skip the classification pass — keyword-only searches miss documents that describe the same mechanism in different words.
- Do not cite a reference without verifying its publication date against the priority date.
- Do not bury a negative result — "no blocking art found" with a documented scope is a deliverable, not a failure.
- Do not drift into legal conclusions ("this is patentable", "you don't infringe") — the report maps evidence; an attorney draws conclusions.

## Quality bar

- Every claim element appears as a row in at least one claim chart.
- Every reference kept has a verified publication date earlier than the priority date.
- The search log lets a second searcher reproduce or extend the search without guessing what was covered.
- The report states the strongest single reference, the strongest combination, and the scope limits — all three, always.

## Escalation

This is research, not legal advice. Patentability opinions, freedom-to-operate analysis, filing strategy, and invalidity positions require a registered patent attorney or agent — recommend one whenever the user's next step involves spending money at a patent office or asserting/defending against a patent. For surveying a research field without a patentability question, route to literature-review.
