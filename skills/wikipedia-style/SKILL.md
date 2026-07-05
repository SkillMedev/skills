---
name: Wikipedia Style Writer
description: Writes neutral, verifiable, encyclopedic prose following Wikipedia's core content policies and manual of style - NPOV, inline citations, notability-backed claims, and a strict ban on promotional language. Use when someone asks "write this like a Wikipedia article", "draft a Wikipedia page for this company or person", "make this neutral and encyclopedic", or "why does my article draft keep getting rejected". Do NOT use for persuasive or opinion writing - use op-ed-writer instead; for thesis-driven academic papers, use academic-essay; for verifying claims in existing text, use fact-checker.
---

# Wikipedia Style Writer

Most article drafts die for one of two reasons: they read like marketing, or they assert claims no independent source supports. Both are instant rejections, and both are invisible to the author because promotional habits feel like normal writing. This skill produces prose in the neutral, encyclopedic register - every significant claim cited, every superlative earned or cut - that survives editorial scrutiny.

## Inputs to collect

1. **Subject and scope** - what the article covers and what it deliberately excludes.
2. **Sources** - the list of available sources, each classified: independent or affiliated, primary or secondary, published where. If the user supplies none, request them before writing; an article cannot be built from memory.
3. **Notability check** - before drafting, confirm the subject has **significant coverage in multiple independent, reliable, secondary sources**. Passing mentions, press releases, the subject's own site, and paid coverage do not count. If the evidence is not there, say so plainly: no style fixes a notability gap, and drafting anyway wastes the user's time.
4. **Contested areas** - any disputes or criticism the article must cover. An article that omits well-sourced criticism fails NPOV as surely as one that editorializes.

## The three core policies

- **Neutral point of view (NPOV)**: represent all significant views fairly and without bias. State facts about opinions, not opinions as facts.
- **Verifiability**: every claim likely to be challenged needs an inline citation to a reliable, published source.
- **No original research**: never synthesize sources to reach a conclusion they don't individually support - if source A says X and source B says Y, the article cannot conclude Z.

## Operating procedure

### Step 1: Map claims to sources

Before writing a sentence, list every claim the article will make and the source that supports it. A claim with no source gets cut or flagged, not softened.

### Step 2: Draft the structure

1. **Lead section**: a concise summary standing on its own. First sentence defines the subject in bold ("**Subject** is a..."). A reader who stops after the lead has the gist. Citations optional in the lead if everything is cited below - but controversial claims get them regardless.
2. **Body**: organized by themes or chronology with section headers (== Heading ==, === Subheading ===).
3. **References / citations**.
4. **See also, External links** - sparingly.

### Step 3: Write in the encyclopedic register

- **Tone**: formal, impersonal, third person. No "you", no "we", no addressing the reader.
- **Present tense** for current facts; past tense for historical events.
- **Dates and numbers**: consistent format within the article.
- **No weasel words**: "some say", "it is widely believed" - attribute specifically or cut.
- **No editorializing**: "fortunately", "interestingly", "notably" inject the writer's view.

### Step 4: Apply the promotional-language ban list

These words and their relatives are banned unless directly quoted from a cited source: *renowned, world-class, groundbreaking, revolutionary, innovative, cutting-edge, leading, premier, prestigious, award-winning* (unless the specific award is named and cited), *best, unique, state-of-the-art, industry-leading, visionary, passionate, dedicated, solutions* (as marketing filler), *seamless, robust, dynamic, iconic, legendary*. Show notability through cited facts; never assert it. Unsourced claims of "first", "only", or "largest" are cut on sight.

### Step 5: Cite every challengeable claim

Use inline footnote markers with structured citations:

```
The company was founded in 2009.<ref>{{cite news |title=... |url=... |work=... |date=... |access-date=...}}</ref>
```

- Cite reliable, secondary, published sources. Avoid blogs, press releases (for claims about the subject), and self-published material.
- One citation per challengeable claim; bundle markers at the end of the sentence.
- Where no reliable source exists, flag the claim inline for the user to source rather than leaving it unverified.

### Step 6: Balance disputes

- Attribute each position: "Critics argue X, while supporters contend Y" - each cited.
- Give weight proportional to coverage in reliable sources; do not elevate fringe views to parity.
- Avoid loaded labels; prefer the most neutral accurate term.

## Worked contrast

**Bad (promotional, unsourced):** "Acme is a leading, innovative software company renowned for its groundbreaking platform, which has revolutionized how world-class teams collaborate."

**Good (neutral, cited):** "**Acme** is a software company that develops collaboration tools for distributed teams. Founded in 2009,<ref>{{cite news |title=... |work=... |date=...}}</ref> it reported 4 million users in a filing covered by *The Verge*.<ref>{{cite news |title=... |work=The Verge |date=...}}</ref> Its platform has been criticized for its data-retention practices<ref>{{cite news |title=... |work=... |date=...}}</ref> and praised for its offline support.<ref>{{cite news |title=... |work=... |date=...}}</ref>"

Every fact is attributable, praise and criticism both appear, and nothing asserts status the sources don't grant.

## Deliverable

Produce the article with a bolded lead, themed sections, inline {{cite}} references, and a flagged list of any claims still lacking a reliable source - plus, when notability evidence is thin, an explicit statement of what coverage is missing.

## Do NOT

- Do not write promotionally - it reads like marketing and gets rejected instantly, taking the subject's credibility with it.
- Do not use first person or address the reader.
- Do not include unsourced superlatives or "first/only/largest" claims.
- Do not build essay-like argumentation toward a conclusion - an encyclopedia describes; it does not persuade.
- Do not launder primary sources: the subject's own website can support routine facts (founding date, location), never claims of significance.
- Do not paper over a notability gap with better prose; report the gap.

## Quality bar

Every challengeable claim carries a citation or an explicit flag; zero ban-list words outside direct quotes; the lead stands alone; criticism and praise weighted to their coverage; no synthesis across sources; tone impersonal throughout; formats consistent.

## Escalation

For persuasive or opinion pieces, route to op-ed-writer; for thesis-driven papers, academic-essay; for verifying the claims in an existing text, fact-checker. When the subject clearly lacks independent coverage, recommend building coverage first (press, analyst reports) rather than forcing an article that will be rejected.
