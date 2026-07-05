---
name: Search Intent Classifier
description: Labels a keyword's dominant search intent (informational, commercial, transactional, or navigational) from the live SERP and prescribes the page type that can rank for it. Use when someone asks "classify these keywords by search intent", "should this keyword get a blog post or a landing page", "what is the search intent of this keyword", or is screening a keyword list for intent before producing content. Do NOT use for grouping keywords into topical clusters and mapping them to a site architecture - use keyword-cluster-builder instead; for building the recommended page, use comparison-page-builder, landing-page-copy, or seo-optimizer.
---

# Search Intent Classifier

Read a query and its live SERP, label the dominant intent, and prescribe the page format that can win. The costly mistake this prevents is building the wrong page type - a beautifully written landing page targeting an informational query will never rank, and no amount of on-page optimization fixes a format mismatch. Classify and recommend; do not build the page.

## Operating procedure

Order matters: the SERP is pulled before any label is assigned, because the keyword string alone is a guess and the SERP is ground truth.

### Step 1: Gather inputs

Collect before classifying:

1. The exact target keyword(s). Default to classifying one at a time; batch lists get the same procedure per keyword with results in a table.
2. The live top 10 organic results for the exact query, plus visible SERP features (People Also Ask, shopping pack, map pack, video carousel).
3. For audits: the URL of the existing page that will not rank.
4. If no live SERP check is possible, say so explicitly and label every classification a guess based on query wording alone.

### Step 2: Label the dominant intent

Apply the four-way taxonomy using both wording signals and SERP evidence. When they conflict, the SERP wins.

- Informational - wording: "how", "what", "why", "guide", "ideas", "examples". SERP: long-form guides, People Also Ask, no shopping pack. Searcher wants to learn.
- Commercial investigation - wording: "best", "vs", "review", "alternatives", "top". SERP: listicles and "best of" roundups. Searcher is comparing before buying.
- Transactional - wording: "buy", "price", "coupon", "for sale", "near me". SERP: product and category pages, shopping packs, maps. Searcher wants to act now.
- Navigational - wording: a brand or product name. SERP: dominated by that brand's own properties. Searcher wants a specific destination.

Classification rules for hard cases:

- If 7+ of the top 10 share one format, that intent is dominant - label it.
- If the top 10 is genuinely split (e.g. 5 guides, 5 product pages), label it hybrid; do not force a single intent.
- A brand name plus a modifier ("hubspot pricing", "hubspot alternatives") is not navigational - the modifier carries the intent (transactional and commercial respectively).

### Step 3: Map intent to page type

- Informational → blog post or topic hub.
- Commercial → "best X" roundup, alternatives page, or comparison page.
- Transactional → product page, category page, or conversion landing page.
- Navigational → homepage or branded hub; if the brand is a competitor's, recommend not targeting it.
- Hybrid → a page that informs first, then converts on the same URL.

### Step 4: Note the SERP feature to match

Record which feature the format should mirror (shopping pack, video carousel, map pack, PAA), because Google is already telling you what it rewards for this query.

### Step 5: For audits, diagnose the mismatch

Classify what the existing page currently is, compare against the prescribed type, and recommend exactly one remediation: retarget the page to a query its current format fits, or rebuild it as the prescribed type. State which is cheaper given the page's existing links and traffic.

## Worked example: classified keyword set

```
KEYWORD                      WORDING SIGNAL    SERP EVIDENCE               INTENT          PAGE TYPE
how to clean cast iron       "how to"          9/10 guides + PAA           Informational   Blog post
best cast iron skillet       "best"            10/10 roundup listicles     Commercial      Best-X roundup
lodge 10 inch skillet price  brand + "price"   shopping pack + products    Transactional   Product page
lodge cast iron              brand only        8/10 lodge.com properties   Navigational    Do not target
cast iron vs stainless steel "vs"              6 guides / 4 comparisons    Hybrid          Comparison guide that
                                                                            (comm-leaning)  ends in a recommendation
```

Read the last row: wording says commercial, but a guide-heavy SERP means Google treats it as research-first - a pure comparison table would underperform a guide with a verdict.

## Deliverable

Produce a classification table containing, per keyword: the dominant intent (or explicit hybrid), the SERP evidence in one line, the prescribed page type, and the SERP feature to match. For audits, add the current page type, the mismatch, and one remediation (retarget or rebuild) with the reason.

## Do NOT

- Do not classify from the keyword string when a SERP check is possible; wording and SERP disagree often enough to matter.
- Do not force a single intent onto a genuinely split SERP - hybrid is a valid, actionable label.
- Do not chase navigational queries for competitor brands; a site will not outrank a brand for its own name.
- Do not build the recommended page. Comparison-table copy is comparison-page-builder; landing pages are landing-page-copy; rewriting existing content to rank is seo-optimizer.
- Do not cluster or prioritize a keyword list by topic - that is keyword-cluster-builder; this skill labels intent per keyword.

## Quality bar

Every label is backed by observed SERP evidence, not the keyword string alone (or is explicitly flagged as a wording-only guess). The output names one dominant intent or an explicit hybrid, one prescribed page type, and the SERP feature to match. Audits state the current type, the prescribed type, and a concrete remediation. If the same batch needs a content plan built around the classified keywords, pair with content-brief for the individual pages and serp-gap-analyzer to find what the ranking pages are missing.
