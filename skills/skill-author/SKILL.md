---
name: Skill Author
description: The section-by-section anatomy standard every paid-quality SKILL.md must meet. Use when drafting or restructuring the BODY of a skill, deciding what sections it needs, or checking a draft against the house standard - "what sections does a skill need", "structure this SKILL.md", "is this skill body complete". Do NOT use for the end-to-end creation process from a raw idea (use skill-creator), grading a finished skill (use skill-auditor), or writing only the description field (use skill-description-writer).
---

# Skill Author

The anatomy standard for a skill worth paying for. A free skill is a blog post in frontmatter; a paid skill is an operating procedure that produces a measurably better outcome than the model gives unprompted. Apply this standard while drafting; hand the finished draft to skill-auditor for grading.

## Inputs to collect before drafting

Ask for whichever of these the requester has not supplied, and do not draft until you have the first three:

1. The job - the single outcome the skill produces, in one sentence.
2. The trigger - three or more exact phrases a user would type when they need it.
3. The audience - practitioner level and context (a founder pricing a SaaS reads differently than a growth analyst).
4. Domain source material - frameworks, thresholds, templates the skill should encode. If none is given, use established practitioner methods and say which you used.

## The nine-part anatomy

Every skill body follows this order. Sections 1-4 and 7-9 are mandatory; 5-6 are mandatory wherever the domain allows them.

1. **Framing paragraph** - the outcome and the costly mistake the skill prevents. Two to four sentences, no throat-clearing.
2. **Operating procedure** - numbered steps in dependency order. When order matters, say why ("do the front-end math before LTV, because cash timing kills first").
3. **Input elicitation** - the exact inputs to collect from the user, with defaults, and the instruction to label guesses as guesses.
4. **Concrete thresholds** - the numbers a practitioner actually uses: targets, ranges, red lines ("LTV:CAC at or above 3:1", "conversion under 40 percent means fix the consult first"). If the domain genuinely has no numbers, encode judgment as explicit if/then rules instead.
5. **Worked artifact** - at least one of: a filled-in example with real numbers, a good/bad contrast pair, a copy-paste template with FILL fields, or a self-contained runnable calculator with its expected output shown and explained. Fenced code blocks are correct for calculators and templates.
6. **Deliverable section** - the exact artifact the user walks away with: "Produce a X containing A, B, C."
7. **Do NOT section** - named failure modes with the why ("do not use revenue for LTV; use contribution margin").
8. **Quality bar** - the checks a finished output must pass before it ships.
9. **Escalation boundary** - where relevant: not legal/medical/financial advice, when to bring in a professional, and which neighbor skill handles adjacent jobs (name it by slug).

## Length and disclosure

- Advisory skills: 500-1,200 words. Operator skills with calculators and templates: up to ~2,800.
- Under ~300 words a skill cannot clear the anatomy and reads as a stub. Over ~3,000, push reference material into sections titled "references/<topic>" at the bottom - the main body must be self-sufficient for the common case.
- Cross-link neighbor skills by plain slug mention ("pair with gym-meta-ads-funnel"), never wiki-link syntax.

## Style rules

- Imperative voice throughout the procedure; third person in the description.
- No dates, versions, "currently", or "latest" - skills must not rot.
- No first person. No filler that restates what the model already knows.
- Frontmatter: exactly two keys, name (Title Case, 64 chars max) and description (WHAT + WHEN + NOT formula - see skill-description-writer).

## Do NOT

- Do not ship a body without a worked artifact when the domain allows one; abstraction is what the user already gets for free.
- Do not present two equally weighted options at a decision point. State one default and an explicit escape hatch: "If X, then Y instead."
- Do not pad with encouragement, summaries of the obvious, or restatements of the procedure.
- Do not absorb a neighbor skill's job; route to it by slug in the escalation boundary.

## Quality bar

A draft meets the standard when: every mandatory anatomy section is present and load-bearing; a practitioner could execute the procedure without asking a clarifying question; at least one artifact could be copied out and used as-is; and deleting any single section would visibly weaken the output.
