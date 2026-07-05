---
name: Skill Creator
description: Use when authoring a brand-new skill from scratch - turning a capability idea or a "make me a skill that…" request into a complete SKILL.md with a trigger-precise description and the full paid-quality anatomy: procedure, elicitation, thresholds, worked artifact, deliverable, Do NOT, quality bar. Triggers on "write a new SKILL.md from scratch", "turn this capability idea into a complete skill". Do NOT use to review or grade an existing skill (use skill-auditor), to convert an existing prompt (use prompt-to-skill), or for writing unrelated to skills.
---

# Skill Creator

Turn a capability idea into a committable SKILL.md that meets the paid-quality bar. The costly mistake this prevents: writing a competent essay about the topic instead of an operating procedure - a skill that reads well and changes nothing about the model's output.

## The bar

The skill you write ships only if all of these hold:

- It **fires on exactly the right requests and never on the wrong ones.** The description front-loads concrete triggers a router can act on with no ambiguity.
- It **does not collide** with any existing skill. Before writing, check the catalog for trigger overlap and carve clean boundaries into both descriptions.
- Its body carries the **full anatomy** (skill-author is the canonical standard): framing, ordered procedure, input elicitation, concrete thresholds, at least one worked artifact, an explicit deliverable, a Do NOT section, and a quality bar.
- It is **token-disciplined.** The description loads every session; the body loads on trigger. Every line is paid for by every installer, every session.
- It is **correct and self-contained** - real tools, real paths, real practitioner numbers; no hallucinated capabilities; no context it hasn't established.

## Operating procedure

1. **Find the trigger first.** Before any body text, answer: what exact request should make this fire? Name concrete signals - task verbs, named artifacts, file types, the phrases a user would actually type. If you cannot name them precisely, the skill is not ready to write; press for the real use case.

2. **Check for collisions.** Search the catalog (browse_skills) for skills whose triggers could fire on the same request. Carve the boundary and write it into both descriptions as negative scope. A new skill that steals a neighbor's territory is a defect.

3. **Gather the domain substance.** Collect the frameworks, thresholds, and templates the skill will encode - from the requester, or from established practitioner methods you name. A skill with no numbers and no artifact is an essay; if the substance is not available yet, say what is missing rather than padding around it.

4. **Write the description - trigger first.** WHAT + WHEN + NOT (skill-description-writer is the canonical standard): open with when to activate, quoting real user phrasing; state what it produces; append "Do NOT use for X - use <slug> instead" wherever a misfire into a named neighbor is plausible.

5. **Write the body in anatomy order.** Framing paragraph (outcome + the mistake prevented), numbered procedure with dependency reasoning, input elicitation with defaults, thresholds with real numbers, worked artifact (filled example, good/bad pair, FILL-field template, or runnable calculator with expected output shown - fenced code blocks are correct here), Deliverable, Do NOT with the why, quality bar, and an escalation boundary where the domain touches legal, medical, or financial ground.

6. **Set name and slug.** Name in Title Case; slug in kebab-case matching the dominant noun. Match the house voice of the existing catalog so the set reads as one author.

7. **Self-check, then hand to skill-auditor.** Re-read the description cold: fires on the right request, silent on a near-miss? Re-read the body: could a practitioner execute without a clarifying question? Is there an artifact they could copy out and use as-is? Then state the skill is ready for a skill-auditor pass before publishing.

## Deliverable

A single committable SKILL.md - valid frontmatter (name, description) plus the full-anatomy body - at the correct slug/SKILL.md path. No commentary baked into the file.

## Do NOT

- Do not write the body before the trigger is nailed. A vague trigger produces a skill that never fires or always fires, however good the body.
- Do not ship an essay. If the body has no procedure, no numbers, and no artifact, it does not clear the bar - get the substance or say what is missing.
- Do not pad: no preamble, no hedging, no restating what the model already knows. If a line does not change behavior, cut it.
- Do not ship territory collisions; fix the boundary in both descriptions before delivering.
- Do not put dates, versions, or "latest" anywhere in the file.
- Do not grade existing skills here - that is skill-auditor's job; hand off rather than absorbing its scope.
