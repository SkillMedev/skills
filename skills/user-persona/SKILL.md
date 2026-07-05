---
name: User Persona Builder
description: Synthesizes interview notes, surveys, support tickets, and analytics into evidence-based user personas that drive design decisions, with every claim traced to source data. Use when someone asks "build personas from our research", "turn these interview notes into personas", "how many personas do we need", or "are our personas evidence-based". Do NOT use for sales-targeting ideal customer profiles with filterable firmographics and buying committees - use icp-persona-builder instead. Do NOT use for extracting jobs-to-be-done from research - use jtbd-extractor instead.
---

# User Persona Builder

Build personas that designers and PMs actually consult - grounded in observed behavior, not demographics invented for a slide. A persona is a research artifact, not a character. The costly failure this prevents is the decorative persona: a stock photo, a name, an age, and a made-up hobby that no design decision ever touches, which quietly teaches the team that research outputs are theater.

## Operating procedure

### Step 1: Gather inputs and enforce the evidence floor

Ask for the source material: interview notes or transcripts, survey results, support tickets, analytics, session recordings. Then apply the evidence rules:

- **Minimum for defensible personas: 8-12 interviews** across the target population; below 5, refuse to call the outputs personas - label them proto-personas (assumption maps) and recommend research first, offering to design it (pair with interview-guide-builder).
- Surveys and analytics alone cannot produce personas - they show what and how much, never why. They corroborate interview-derived clusters; they do not replace them.
- If there is no research at all, say so plainly and offer a research plan. Never fabricate a persona from team assumptions and present it as evidence.

Also collect: the decisions the personas must inform (onboarding? pricing? feature priority?), and any existing segmentation to reconcile against. Label assumed context as a guess.

### Step 2: Find patterns, not averages

Read across all data and tag recurring behaviors, goals, and pain points. Cluster users by **behavior and motivation, not by age or job title** - two 28-year-old marketers can need opposite products, and a 24-year-old and a 55-year-old with the same workflow belong in the same persona. Averaging produces a user who does not exist; clustering finds users who do.

Aim for the smallest number of personas that captures the meaningful differences - usually 3-5. Each persona must represent a distinct strategy or need; if two clusters would lead to the same design decisions, merge them.

### Step 3: Build each persona from the skeleton

```
PERSONA - [FILL: memorable label tied to defining behavior, e.g. "The Batch Processor"]
Evidence base: [FILL: n of X interviews, plus corroborating sources]   Research date: [FILL]

SNAPSHOT     [FILL: one sentence - who they are via what they do, not who they resemble]
CONTEXT      [FILL: role, environment, constraints - time, budget, skill level]
GOALS        [FILL: what they are ultimately trying to accomplish - the job-to-be-done]
             Evidence: [FILL: e.g. 7/10 interviews; P2, P4, P7 explicit]
FRUSTRATIONS [FILL: concrete obstacles from the data, not generic gripes]
             Evidence: [FILL]
BEHAVIORS    [FILL: how they actually act - tools, workarounds, frequency]
             Evidence: [FILL: observed vs self-reported, noted separately]
QUOTES       [FILL: 1-3 verbatim quotes, participant IDs attached]
NEEDS        [FILL: what the product must do to serve them]
ASSUMPTIONS  [FILL: anything inferred rather than observed - marked to validate]
DESIGN IMPLICATION  Because [goal/frustration], the product should [decision].
```

The design implication line is mandatory - it is what makes a persona drive decisions instead of decorating a wall.

### Step 4: Tie every claim to evidence

For each goal and frustration, note how many participants expressed it or which data point supports it ("6 of 10 interviews", "38% of survey respondents", "top-3 support ticket driver"). A claim supported by one participant is a signal, not a persona trait. Mark anything inferred as an assumption to validate later - the assumptions row is a feature, not an embarrassment.

Contrast pair:

Bad: "Sarah, 34, loves technology and values efficiency. She enjoys yoga."

Good: "Batch Processor - clears all approvals in one Friday session because context-switching costs her more than delay does (8/12 interviews; P3: 'I'd rather let it pile up than break focus'). Design implication: build a bulk-approve queue, not per-item notifications."

### Step 5: Differentiate and rank

Place personas on the one or two dimensions that matter most for the decision at hand (e.g., expertise vs. usage frequency). If two personas sit on top of each other, merge them; if one has no distinct need, question whether it earns its place. Then designate: **primary** (design for), **secondary** (accommodate), **anti-persona** (explicitly not for - naming who you will not serve is as clarifying as naming who you will).

### Step 6: Set the expiry

Personas decay. Stamp each with the research date and recommend revalidation - every 12-18 months for stable products, after any major product or market shift otherwise. An undated persona is unfalsifiable and will be trusted long after it is wrong.

## Deliverable

Produce a persona set containing: 3-5 personas built from the skeleton with per-claim evidence counts, verbatim quotes with participant IDs, a primary/secondary/anti-persona designation, a one-line design implication per persona, an explicit assumptions-to-validate list, and the research date with a revalidation cadence.

## Do NOT

- Do NOT invent demographics, hobbies, or stock-photo characterization the data does not support - they add false vividness and invite stereotyping.
- Do NOT cluster by demographic category; behavior and motivation only. Demographic clustering produces stereotypes with citations.
- Do NOT average across participants - the "average user" is a statistical fiction nobody can design for.
- Do NOT present proto-personas (assumption-based) with the same visual authority as researched ones; label the difference loudly.
- Do NOT ship a persona without a design implication line - an implication-free persona changes no decisions.

## Quality bar

- Every goal and frustration carries an evidence count or source; zero unsourced claims.
- Each persona is behaviorally distinct - no two would lead to the same design decisions.
- Quotes are verbatim and attributed to participant IDs, anonymized for external sharing.
- Assumptions are explicitly separated from findings.
- The set names a primary persona and the research date.

## Escalation

For sales-facing targeting with firmographics, triggers, and buying committees, route to icp-persona-builder. To generate the interview data these personas require, pair with interview-guide-builder; to theme the raw notes first, pair with interview-synthesis.
