---
name: Research Readout
description: Turns synthesized research findings into a crisp stakeholder readout — so-what up front, 3-5 confidence-tagged findings each following the finding-evidence-implication rule, and owned recommendations. Use when someone says "present these research findings", "turn this study into a readout for leadership", "our research isn't landing with the product team", or needs a deck or brief that gets findings acted on. Do NOT use for building a narrative around quantitative metrics and charts — use data-story instead; for the upstream analysis that produces the findings, use interview-synthesis or research-synthesis; for slide craft itself, use slide-deck-builder.
---

# Research Readout

A readout is not a research report: its job is to transfer the minimum context stakeholders need to make a decision or change direction, then get out of the way. Most research goes unacted on not because it is wrong but because the readout is too long, too hedged, or buries the recommendations at the end — stakeholders decide in the first 60 seconds whether to stay engaged, and a sea of "mights" and "possiblys" reads as noise.

## Inputs to collect

1. The synthesized findings (from interview-synthesis, research-synthesis, or equivalent) — the readout communicates analysis, it does not perform it. If handed raw transcripts, route to synthesis first.
2. The audience and the decision on the table. A readout without a live decision becomes FYI content and dies; if no decision exists, ask what the study was supposed to unlock.
3. Study metadata: goal, method, sample size, and per-finding participant counts (needed for confidence tags).
4. Format constraint. Default by audience: async stakeholders get a deck, one finding per slide, headline in the title bar; live sessions get 20-30 minutes of findings plus 10-15 of discussion; engineering audiences get a one-page brief with a findings table.

## Operating procedure

1. **Write the so-what first.** Open with 2-3 bullets stating the most important findings and their implications for the product or business. This section is drafted first and rewritten last.
2. **Select 3-5 findings — no more.** Rank by decision impact and cut the rest to the appendix. Ten findings is a report wearing a readout's clothes.
3. **Apply the finding-evidence-implication rule to each.** Every finding must carry all three parts:
   - *Finding*: what was observed, as a declarative headline.
   - *Evidence*: the quotes, clips, or counts that back it — kept tight.
   - *Implication*: what it means for the business, which is what earns attention. "Users cannot find the billing page" is a finding; "the navigation structure is causing cancellations because users cannot find billing, which triggers most churn calls to support" is a finding with its implication attached.
4. **Tag every finding with confidence**: High (5+ participants, consistent pattern), Medium (3-4 participants, some variation), Low (1-2 participants, or inferred). Be direct on High findings; be explicit about limitations on Low ones; never hedge uniformly.
5. **Write 1-3 recommendations per major finding**, as imperatives, each specific, owned, and actionable within the team's control ("Redesign onboarding to surface the core action on the first screen — owner: growth squad"). Pair each recommendation with the finding that motivates it, so the team can disagree with the evidence rather than the researcher. If the right next step is more research, name the specific question it answers and the decision it unlocks — never a bare "do more research".
6. **Close with open questions** — what the study did not answer and what to study next.
7. **Fit the format** (Step 4's constraint) and attach the full report or recording as an appendix; never assume anyone will consume it.

## Worked example: one finding, done badly and well

Bad slide: "Theme 3: Navigation. Several participants had some difficulty with aspects of the navigation, which might suggest possible improvements could be considered."

Good slide — title bar: "Users cancel because they can't find billing (High — 7/9 participants)."
Body: Evidence — 7 of 9 participants failed the find-your-invoice task; "I assumed billing lived under my avatar; when it wasn't there I figured I'd just call to cancel" (P4). Implication — billing findability is the trigger behind the largest category of churn calls. Recommendation — move billing to top-level navigation and re-test with 5 users; owner: platform squad.

## Deliverable

Produce a readout in the agreed format with five sections: (1) Context — study goal, method, sample, one paragraph max; (2) Key Findings — 3-5 numbered, each with confidence tag, participant count, and implication; (3) Supporting Evidence — selected quotes or clips per finding, tight; (4) Recommendations — imperative, owned, paired to findings; (5) Open Questions — plus the full report as an appendix link.

## Do NOT

- Do not open with methodology. Context gets one paragraph; the so-what gets the opening.
- Do not present a finding without its implication — stakeholders will not do the translation, and untranslated findings do not change decisions.
- Do not hedge everything equally; uniform hedging destroys the signal that confidence tags exist to carry.
- Do not recommend "more research" without the specific question and the decision it unlocks.
- Do not exceed 5 findings in the main body — selection is the researcher's job, not the audience's.

## Quality bar

Before shipping: the so-what bullets stand alone for someone who reads nothing else; every finding carries a confidence tag with participant count; every finding states its implication; every recommendation is imperative, owned, and traceable to a finding; the whole readout fits its format budget (deck: one finding per slide; live: 20-30 minutes; brief: one page).

## Escalation

If the findings are metric-led — a chart trend needing a narrative — that is data-story. If the inputs are unanalyzed transcripts, run interview-synthesis first; multi-source document synthesis is research-synthesis. For deck mechanics beyond structure, pair with slide-deck-builder; if findings surface jobs and switching forces worth extracting formally, route to jtbd-extractor.
