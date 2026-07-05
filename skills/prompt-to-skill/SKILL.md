---
name: Prompt to Skill
description: Converts a prompt a user runs repeatedly into a reusable, paid-quality SKILL.md. Use when someone says "I keep pasting this prompt", "turn this prompt into a skill", "package this workflow", or has a recurring task pattern they want portable across projects and sessions. Do NOT use to author a skill from a capability idea with no existing prompt (use skill-creator) or to restructure an existing SKILL.md (use skill-author).
---

# Prompt to Skill

Takes a prompt someone runs over and over and packages it into a skill any agent can load, discover, and execute consistently. The costly mistake this prevents: transcribing the prompt verbatim into frontmatter, which preserves its padding and loses its triggers — producing a skill that neither fires nor performs.

## Inputs to collect

1. The prompt itself, verbatim — the real text they paste, not a summary of it.
2. Two or three example runs: the input they gave and the output they liked. Real runs reveal what the prompt actually does versus what it says.
3. What the prompt gets wrong today — the edits they make to its output every time. Those corrections become skill rules.

If the user cannot produce an example run, treat the request as a capability idea and hand off to skill-creator.

## Operating procedure

### Step 1 — Extract the pattern

Answer before writing anything:

1. What is the one-sentence job this prompt does?
2. What exact inputs does the user provide each run?
3. What does a successful output look like, structurally?
4. When would someone reach for this? Name three or more concrete trigger phrases in the user's own words.

If the prompt handles more than one job, split it: one skill per job. A prompt that "reviews my code and writes the commit message" is two skills.

### Step 2 — Distill into the anatomy

Map the prompt onto the standard skill anatomy (see skill-author for the full standard):

- Procedural instructions become the numbered operating procedure.
- The inputs from Step 1 become the input-elicitation section.
- Heuristics and rules of thumb become concrete thresholds or if/then rules.
- The best example run becomes the worked artifact — keep one representative example, formatted as a good/bad pair if the user's corrections show what "bad" looked like.
- The user's recurring corrections become the Do NOT section. These are the highest-value lines in the whole skill: they encode failure modes the base model actually exhibits.
- The output description becomes the Deliverable section.

Drop prompt padding entirely: "please", "make sure to", "always remember", role-play framing ("you are an expert…"). Keep the rule, cut the filler.

### Step 3 — Write the description last

Only after the body is settled, write the frontmatter description using the WHAT + WHEN + NOT formula (see skill-description-writer), quoting the trigger phrases from Step 1 verbatim. The description is what makes the skill fire; the trigger phrases came from the user, so use their words.

### Step 4 — Verify against the original

Run the user's example input through the new skill mentally (or via skill-tester for a live check). The output must match or beat the output they liked. If it loses something the prompt had, a rule got dropped in distillation — find it and restore it as a rule, not as padding.

## Quality bar

The conversion is done when: the skill fires on all three trigger phrases; every recurring correction the user reported is encoded as a Do NOT rule; the worked example survives from a real run; the body contains zero lines of prompt padding; and the user could delete their saved prompt without losing anything.

## Do NOT

- Do not transcribe the prompt verbatim — distill it. Padding in a prompt is annoying; padding in a skill is a recurring token tax on every session.
- Do not invent triggers the user never said. Their real phrasing is the discoverability data; synthetic phrasing misses how they actually ask.
- Do not merge multiple jobs to keep one file. Two small precise skills beat one vague one.
- Do not skip the example run. A skill converted without evidence of what "good output" means is a guess wearing frontmatter.
