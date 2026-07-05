---
name: Skill Description Writer
description: Writes the description field that makes a skill fire reliably — WHAT it does, WHEN to invoke it with quoted user phrasing, and what it is NOT for. Use when drafting or improving only the description or trigger of a SKILL.md or catalog entry — "this skill never fires", "fix this description", "write the frontmatter description". Do NOT use to author a whole skill (use skill-creator) or to grade/audit one (use skill-auditor).
---

# Skill Description Writer

Crafts the description field that determines whether a skill is ever selected. A weak description means the skill never fires; an overloaded one fires on the wrong tasks. This is the highest-leverage line in any skill: the body only matters if the description gets it loaded.

## Inputs to collect

1. What the skill produces (its deliverable, one sentence).
2. Three or more phrases a real user would type when they need it — in the user's words, not the author's.
3. The nearest neighbor skills a request could confuse it with.

If the author cannot supply real user phrasing, derive candidates from the body's deliverable and triggers, and mark them for validation with skill-tester.

## The three-part formula

Every description contains these parts, in this order:

1. **WHAT** — one tight sentence naming the capability in active third person: "Generates a zero-downtime database migration plan."
2. **WHEN** — concrete trigger conditions, quoting real user phrasing where possible: "Use when adding columns, altering types, dropping tables, or backfilling data on a live schema" or "Triggers on 'is this term sheet fair', 'liquidation preference', 'board seats'."
3. **NOT** — negative scope routing to the named neighbor, whenever a plausible confusion exists: "Do NOT use for offline schema redesign — use database-schema instead."

For skills with strong, distinctive triggers, WHEN may lead ("Use when…") and WHAT follows — the order that disambiguates fastest wins. The NOT clause is mandatory when a neighbor exists and omitted only when nothing could plausibly collide.

## Trigger term density

Pack the WHEN clause with the exact nouns, verbs, and phrases users type. Think: what would someone say the moment they need this? Include synonyms for the same action ("negotiate", "counter", "push back on"). Concrete verbs beat abstract nouns: "adding, altering, debugging" fires; "enhancement, improvement" never does.

## Length and formatting

- Frontmatter description: 1024 chars max. Simple skills land at 150–300 chars; skills with many trigger phrases and a NOT clause legitimately run 400–700.
- Catalog card/summary field: 200 chars max — WHAT plus the strongest trigger only.
- Third person, active voice throughout ("Generates", "Reviews" — never "I will" or "Helps you").
- No backticks or markdown inside the value; no trailing period issues in YAML; no dates or versions.

## Good / bad

Good: "Reviews a diff for correctness, security, and regression risk. Use before opening a PR, after writing a non-trivial change, or when asked to 'review this code'. Do NOT use for style-only formatting passes."
Bad: "A helpful skill for reviewing things when you want feedback." — no trigger, no scope, vague qualifier.

Good: leads with the output — "Produces a cited research report…"
Bad: leads with the method — "Uses web searches to gather information…"

## Deliverable

The rewritten description string (frontmatter version, plus the 200-char catalog version when asked), with a one-line note naming which trigger phrases were added and which neighbor the NOT clause routes to.

## Do NOT

- Do not stuff triggers the skill cannot serve — over-broad descriptions cause misfires, which cost more trust than silence.
- Do not describe the skill's internal method; describe the user's moment of need and the output.
- Do not use vague qualifiers ("helpful", "useful", "powerful", "better").
- Do not omit the NOT clause when a named neighbor exists; collisions are territory defects, and the description is where they are fixed.

## Quality bar

Done when: WHAT, WHEN, and (where a neighbor exists) NOT are all present; at least three concrete trigger phrases appear in the user's own words; a router reading only this description would fire it on the right request and stay silent on the nearest wrong one.
