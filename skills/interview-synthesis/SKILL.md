---
name: Interview Synthesis
description: Turns raw interview notes into themed, evidence-tagged insights via a two-pass affinity-mapping procedure, with frequency and confidence ratings and stakeholder-ready quotes. Use when someone asks "synthesize these interviews", "what themes came out of our research round", "turn these transcripts into findings", or has finished a qualitative round and needs actionable output. Do NOT use for synthesizing published papers or mixed documents - use research-synthesis instead. Do NOT use for building the personas themselves - use user-persona instead. Do NOT use for consolidating hiring-interview scorecards - use interview-debrief-synthesizer instead.
---

# Interview Synthesis

Synthesis is where data becomes insight. The job is not to summarize what people said - it is to identify the underlying pattern that explains why they said it and what it means for design or strategy. The costly failure it prevents is the highlight reel: a deck of cherry-picked quotes that confirms what the team already believed, laundered through the word "research."

## Operating procedure

### Step 1: Gather inputs and check the dataset

Collect: the transcripts or structured notes, the original research questions, and who the findings are for (design team vs. executives changes the framing, not the rigor). Enforce the data rules:

- Every interview needs a cleaned transcript or structured notes with participant ID, date, and verbatim quotes marked. Do not synthesize from memory or from the researcher's paraphrase alone.
- **Minimum viable dataset: 5 interviews** for foundational themes. Fewer is workable only with an explicit low-confidence label on every finding.
- **Check for saturation**: if the last 3 interviews produced no new themes - only more evidence for existing ones - the round has reached saturation and the theme set can be treated as stable. If new themes were still appearing in the final interviews, say so and flag that more interviews would likely surface more; the synthesis is a snapshot, not the territory.

### Step 2: Extract atomic observations (pass one)

Go through each transcript and extract atomic observations - one idea per row or sticky note. These are factual, not interpretive: "P4 said she checks the app every morning before coffee," not "P4 is highly engaged." Keep the participant ID on every observation. Expect roughly 20-40 observations per hour-long interview; far fewer usually means the notes are summaries, not data.

### Step 3: Affinity-map the observations (pass two)

Run the affinity-mapping procedure:

1. Pool all observations from all participants into one space (physical wall or digital board), shuffled - deliberately ignoring which participant said what, so clusters form around meaning rather than around people.
2. Move observations into groups by similarity of underlying behavior or motivation, not surface topic. "Checks app before coffee" and "reads notifications in bed" may share a cluster ("processes the day's queue before work") even though one mentions the app and one does not.
3. Do not pre-name the groups. Let clusters form, split, and merge for at least one full pass; premature labels attract mismatched observations like magnets.
4. Name each stable cluster with a verb-noun label describing the behavior: "avoids manual entry", "relies on notifications as reminders" - never a topic noun like "notifications."
5. Re-attach participant IDs and count coverage per cluster. A cluster fed by one participant is an outlier candidate, not a theme.

Only after clustering does interpretation begin - observations first, interpretations second, because reversing the order lets the hypothesis choose the evidence.

### Step 4: Elevate clusters to insights

An insight is a non-obvious, actionable statement connecting observed behavior to an underlying motivation or friction. Use the format: **[User type] [does behavior] because [motivation/belief], but [tension or barrier].**

Contrast pair:

Weak: "Users find notifications helpful."

Strong: "Power users treat push notifications as their primary task queue because they distrust the in-app home screen, but this breaks down when notification volume exceeds ~15 per day."

The weak version restates a preference; the strong version names who, the mechanism, and the breaking point - each element a design lever.

### Step 5: Attach quotes and evidence tags

Each insight needs one strong verbatim quote and ideally two corroborating ones **from different participants** - three quotes from one person is one data point wearing three hats. A strong quote is specific, vivid, and free of researcher interpretation. Anonymize to "P3" or a persona label before any external sharing.

Tag every insight with **frequency** (how many of n participants) and **confidence** (high / medium / low - high requires majority frequency plus consistent, unprompted mentions). A single-participant observation is a signal, not a finding: keep it in an "outliers worth watching" section rather than promoting it to a theme, however compelling the quote.

### Step 6: Write the "so what"

Every theme ends with a recommendation: what the team should do, test, or decide given this insight. A synthesis that stops at "what we heard" is organized note-taking, not research output.

## Deliverable

Produce a findings document containing: an executive summary (3-5 bullets, insights not process), a themes table (theme name, insight statement in the four-part format, frequency n/N, confidence, top quote), a "so what" recommendation per theme, a saturation statement, and an appendix of outliers and open questions.

## Do NOT

- Do NOT count vividness as frequency - one unforgettable quote from one participant is still n=1.
- Do NOT cluster by question asked ("answers to Q3") - that reproduces the guide's structure instead of discovering the participants'.
- Do NOT let interpretation into pass one; an observation with an adjective in it ("was frustrated by") is already an interpretation.
- Do NOT drop disconfirming observations that fit no cluster - they belong in outliers or open questions, and they are often next round's research question.
- Do NOT present low-confidence findings with the same visual weight as high-confidence ones.

## Quality bar

- Every insight follows the [user type] / [behavior] / [because] / [but] format and traces to named observations.
- Every theme shows frequency as n/N and a confidence rating with its basis.
- Corroborating quotes come from different participants, anonymized.
- Saturation status is stated explicitly.
- Outliers are preserved in the appendix, not deleted or promoted.

## Escalation

To turn themed findings into personas, hand off to user-persona; to package findings for a stakeholder readout, pair with research-readout. If the next round's questions emerged here, feed them to interview-guide-builder.
