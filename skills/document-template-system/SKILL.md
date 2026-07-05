---
name: Document Template System
description: Builds a maintained library of reusable document templates for a team — frequency-based selection, four-part template anatomy, style and naming conventions, and a quarterly maintenance protocol. Use when someone asks "set up document templates for my team", "our docs are all formatted differently", "create a standard template for status reports or proposals", or "how do we keep templates from going stale". Do NOT use for designing Notion databases and their properties — use notion-database instead. For writing one specific process document rather than a template system, use process-doc; for visual brand rules, use brand-guidelines.
---

# Document Template System

A template is not a filled-out document with blanks — it is a decision made once so the team never makes it again. The costly failure is the over-built system: twenty templates nobody uses, each slightly stale, until authors quietly revert to copying last quarter's doc and the formatting drift returns. The best template systems are small, self-explanatory, and maintained by one named person.

## Operating procedure

Audit before building, build before standardizing, standardize before naming — each step's output is the next step's input.

### Step 1: Audit what the team actually produces

List the top 10 document types by frequency (count real docs from the last quarter; do not poll people on what they think they write). Build templates only for the top 5. Attempting to template everything produces a system nobody uses — every template below the frequency line is pure maintenance debt.

For each of the top 5 candidates, answer three questions before designing anything:

- Who authors this document, and how often?
- Who reads it, and what do they do with it? (A doc that drives a decision is structured differently from a doc that records one.)
- What varies between instances, and what should stay constant? The constant parts become required sections; the varying parts become variables.

If a candidate's answers are all "it depends," it is not one document type — split it or drop it.

### Step 2: Build each template with the four-part anatomy

Every template contains exactly these four elements:

1. **Purpose block** — a single paragraph for the author's eyes stating what the template is for, when to use it, and what it is NOT for. This block is deleted before the document is sent.
2. **Required sections** — appear in every instance; label them `REQUIRED`.
3. **Optional sections** — relevant only sometimes; label them `OPTIONAL — include if [condition]` and remove the label when used. The condition must be decidable by the author alone ("include if budget exceeds $10k", not "include if relevant").
4. **Variables** — instance-specific placeholders written as `[VARIABLE NAME IN CAPS]` so they are visually distinct and findable with text search. A shipped document containing a raw `[VARIABLE]` is the system's most visible failure — capitalized bracketed variables make it nearly impossible to miss one.

### Step 3: Set style conventions once, at the team level

Documentation style is a team-level decision, not a document-level decision. Establish once and enforce everywhere:

- **Dates**: YYYY-MM-DD in machine-readable contexts; spelled out ("June 17, 2026" style) in documents for executives or external parties.
- **Versioning**: v1.0 at first publish, v1.1 for minor updates, v2.0 for structural changes. The version bumps when content changes, not when someone opens the file.
- **Owner field**: every document has exactly one owner, and ownership is a person, not a team — "owned by Marketing" means owned by nobody.
- **Status values**: a fixed set — Draft / In Review / Approved / Archived — applied consistently. Four states; resist adding more.

### Step 4: Enforce naming conventions

A file name should answer three questions without opening the file: what is it, who owns it, and how current is it.

Default format: `[DocumentType]-[Subject]-[Owner]-[Date or Version]`
Example: `Report-Q2-Revenue-Ouellet-2026-06`

Never allow "final", "final-v2", or "latest" in file names — they are lies waiting to happen; the version or date field carries currency instead.

### Step 5: Install the maintenance protocol

Assign one maintainer per template library — a system without a name on it will be abandoned within two quarters. Schedule a quarterly review with a fixed agenda:

- Retire any template unused in the past year (check actual instances created, not opinions).
- Update style conventions if the team's tools or audience changed.
- Collect one piece of author feedback per active template and fix the top friction point.

## Worked artifact: a template built to the anatomy

A decision-document template, copy-paste ready:

```
PURPOSE (delete before sending): Use this when a decision needs sign-off from
people outside the working group. Not for status updates (use the status
template) or brainstorms. Aim for one page.

# Decision: [SHORT DECISION TITLE]
Owner: [ONE PERSON]   Status: Draft   Version: v1.0   Date: [YYYY-MM-DD]

## Context — REQUIRED
[2-4 SENTENCES: WHAT PROMPTED THIS, WHY NOW]

## Options considered — REQUIRED
1. [OPTION] — [ONE-LINE TRADEOFF]
2. [OPTION] — [ONE-LINE TRADEOFF]

## Recommendation — REQUIRED
[THE PICK AND THE ONE REASON THAT DECIDED IT]

## Cost and reversibility — OPTIONAL — include if cost > $10k or hard to undo
[WHAT IT COSTS, WHAT UNDOING IT WOULD TAKE]

## Sign-off — REQUIRED
[NAME] — [APPROVE / REJECT] — [YYYY-MM-DD]
```

**Bad variable style** for contrast: "Decision: (put title here)" — lowercase, unbracketed, invisible in a skim, unfindable by search, and guaranteed to ship in a real doc eventually.

## Deliverable

Produce a template library kit: the ranked top-10 audit with the top-5 cut line, one template per selected type built to the four-part anatomy, a one-page style-conventions sheet (dates, versions, owner, status), the naming convention with two filled examples, and the maintenance schedule naming the maintainer and the quarterly review agenda.

## Do NOT

- Do not template all ten document types — everything below the top 5 is maintenance debt that erodes trust in the whole library.
- Do not write purpose blocks meant to survive into the final document — they read as boilerplate to the audience and train authors to ignore instructions.
- Do not use lowercase or unbracketed placeholders — they ship in real documents because nobody can see them.
- Do not assign ownership to a team — a team owner is no owner, and the template drifts within a quarter.
- Do not allow "final" in any file name — the version field is the only source of currency.
- Do not skip the quarterly review because "the templates are fine" — staleness is invisible from the inside; the review is how you find it.

## Quality bar

- Every shipped template passes a search for `[` returning only intentional variables.
- A first-time author can complete the template without asking anyone a question — the purpose block and optional-section conditions carry all the judgment.
- Each template has a named owner and a last-reviewed date under one quarter old.
- The library contains 5 or fewer active templates, each with real instances created in the past year.
- File names in the shared drive follow the convention; spot-check ten recent docs.

## Related skills

For a single process document rather than a reusable system, use process-doc. If the templates live in Notion and need database-backed structure (properties, views, per-row templates), pair with notion-database. Recurring report formats with data in them belong to formatted-report-writer.
