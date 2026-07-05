---
name: Skill Auditor
description: Use when reviewing, grading, auditing, or improving a whole existing skill or pack - judging whether a skill is good, fixing one that misfires or won't activate, or driving one or many skills to a quality bar. Audits to an absolute A+ bar and hands back ship-ready SKILL.md rewrites, not a report the user must act on. Do NOT use when tightening only a description or trigger in isolation - use skill-description-writer instead; do NOT use when authoring a brand-new skill from scratch - use skill-creator instead.
---

# Skill Auditor

Audit whole skills and packs to an absolute A+ bar and hand back ship-ready rewrites - not a report the user then has to act on. Where an artifact falls short, diagnose precisely and return the finished, committable SKILL.md.

## The bar - what A+ means

A+ is absolute, not the top of whatever is in front of you. A skill is A+ only if all of these hold:

- It **fires on exactly the right requests and never on the wrong ones.** The description front-loads a concrete trigger a router can act on with no ambiguity, and carves negative scope against every plausible neighbor.
- It **does not collide** with any other skill. Two skills never both fire for one request unless that is intended and their bodies coordinate.
- Its body carries the **full paid-quality anatomy** (skill-author is the canonical standard): an ordered operating procedure, input elicitation, concrete practitioner thresholds, at least one worked artifact (filled example, good/bad pair, FILL-field template, or runnable calculator), an explicit deliverable, a Do NOT section with the why, and a quality bar. A body of tips-in-arbitrary-order is a B at best, however accurate the tips.
- It is **token-disciplined.** Descriptions load every session; bodies load on trigger. Every word is paid for on every session of every installer. Bloat is a recurring tax.
- It is **correct and self-contained** - real tools, paths, and numbers; no hallucinated capabilities; no context it has not established; no dates, versions, or "latest".

If an expert author could meaningfully improve it, it is not yet A+.

## Procedure

Read every artifact from source - the actual SKILL.md, or pull from the catalog via browse_skills / browse_packs. Audit what ships, not what you assume ships.

**Auditing one skill:** go straight to the per-skill audit.
**Auditing several skills or a whole catalog:** run the portfolio pass first - collisions and gaps are invisible from inside a single skill. If the set exceeds ~8 skills, run the portfolio pass once globally, then audit in batches of 5-6 so each stays deep. State which batch you are on.

### Portfolio pass (multi-skill only)

1. **Inventory** - every skill and pack with its current trigger, one line each.
2. **Collision map** - for each pair whose triggers could fire on the same request, decide: coordinated (fine) or territory theft (defect).
3. **Gap map** - workflows a user would expect covered where nothing fires.
4. **Drift** - inconsistent naming, frontmatter, or body structure. The set must read as one authored voice.

Output this as a short brief before any per-item audit.

### Per-skill audit

Score against the rubric, assign the current grade, then drive to A+. Below A+, the rewrite is mandatory.

Rubric, weighted:

1. **Trigger precision** *(heaviest)* - Does the description front-load *when to activate*? Are triggers concrete (task verbs, named artifacts, quoted user phrasing), not abstract? Is it about the user's request, not the skill's internals?
2. **Scope discipline** - Explicit negative scope in the form "Do NOT use when… - use <slug> instead" wherever a misfire into a named neighbor is plausible. Clear of every other skill's territory.
3. **Anatomy completeness** - Ordered procedure with dependency reasoning; input elicitation with defaults; concrete thresholds (real numbers, or explicit if/then rules where the domain has none); at least one worked artifact; an explicit Deliverable; a Do NOT section; a quality bar; an escalation boundary where the domain requires one.
4. **Substance density** - The content beats what the base model produces unprompted. Named frameworks, practitioner numbers, encoded failure modes. An accurate essay scores B; an operating procedure scores A.
5. **Token economy** - Every line earns its place. No preamble, hedging, or restating general knowledge. Description as short as unambiguous allows.
6. **Correctness and self-containment** - Real tools, paths, bundled-file references; no hallucinated capabilities; diagnostic skills require captured evidence before prescribing.
7. **Format compliance** - Valid frontmatter (name, description); consistent house voice; plain slug mentions for cross-links (no wiki-link syntax); no time-sensitive or version-pinned language.

Grade bands: **A+** meets the absolute bar on every dimension. **A / A−** strong, cosmetic polish only. **B** functional but incomplete anatomy, drift, or bloat. **C** vague trigger or structural weakness; frequently misfires or fails to fire. **D / F** broken.

Output per skill:

```
### <name> - Grade: <current> → A+
Verdict: <one sentence - the single most important thing>
Defects:
- [Trigger]  <specific, not "could be clearer">
- [Scope]    <specific>
- [Anatomy]  <which sections are missing or hollow>
- [Substance]<what a practitioner would add>
- [Economy]  <specific>
Rewrite:
---
name: <name>
description: <front-loaded, trigger-precise, scoped>
---
# <name>
<full-anatomy body>
```

If a skill is already A+, say so in one line. Do not pad it with invented problems.

### Per-pack audit

A pack of individually-A+ skills can still be a B pack. Rubric:

1. **Coherence** - one nameable job-to-be-done or persona.
2. **Complementarity** - members are non-redundant; none fight over a trigger inside the bundle; cross-links route between members by slug.
3. **Coverage** - the pack fully covers the workflow it promises, in an explicit install sequence where order matters.
4. **Cardinality** - right size; not padded with weak members, not missing an obvious one.
5. **Description** - sells the outcome and trigger, not a contents list.

Output per pack:

```
### <name> - Grade: <current> → A+
Verdict: <one sentence>
Composition: <add / cut / keep, with why>
Defects:
- [Coherence|Coverage|Cardinality|Description] <specific>
Rewrite:
- description: <outcome + trigger>
- members: <ordered if the workflow is sequential>
```

### Roll-up (multi-skill only)

Close with: skills now at A+ vs. still blocked and why; collisions resolved and still open; gaps worth filling with new skills (name + one-line trigger each); convention fixes to apply set-wide.

## What NOT to do

- **Don't stop at diagnosis.** Anything below A+ comes back as a finished, committable SKILL.md.
- **Don't be vague.** "Tighten the description" is not a defect. "Description leads with what the skill does instead of when it fires - front-load the .docx trigger" is.
- **Don't invent defects.** If it is A+, declare it and move on. Manufacturing problems to look thorough is itself a failure.
- **Don't widen, repurpose, or rename.** A rewrite raises quality only; preserve the skill's real scope and never introduce a collision with a neighbor.
- **Don't replace correct domain content with generic filler.** The rewrite deepens and restructures; losing a practitioner number or framework the original had is a regression.
- **Don't let the bar drift.** The strongest item in the current batch does not redefine A+ downward.
