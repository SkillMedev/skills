---
name: JTBD Extractor
description: Extracts Jobs To Be Done from qualitative research data — job stories in the "When I…, I want to…, so I can…" form, a forces-of-progress map (push, pull, anxiety, habit), and functional, emotional, and social dimensions, each backed by verbatim quotes. Use when someone says "extract the jobs from these interviews", "run a JTBD analysis on this diary study", "why do users switch to or from us", or has transcripts and wants solution-agnostic goals rather than feature requests. Do NOT use for clustering findings into themes across a study — use interview-synthesis instead; for building persona profiles from research, use user-persona; for planning the interviews themselves, use interview-guide-builder.
---

# JTBD Extractor

Jobs To Be Done reframes research around the progress people are trying to make, not product features or user attributes — the Intercom/Ulwick lineage, not a cargo-culted template. The costly failure this skill prevents is shipping a "jobs inventory" that is really a feature wishlist with JTBD grammar pasted on: jobs contaminated with solution language are unstable, and a roadmap built on them chases tools instead of progress.

## Inputs to collect

1. The raw data: interview transcripts, diary-study entries, support tickets, or switch-moment notes. Verbatim material is required — summaries strip the quotes that serve as evidence. If only summaries exist, proceed but downgrade all evidence strength and say so.
2. Sample size and who was interviewed. Fewer than 5 participants means the inventory is directional; label it that way.
3. The decision the jobs will inform (roadmap, positioning, onboarding redesign). This sets granularity — positioning wants the main job; roadmap wants the sub-jobs.
4. Any existing jobs inventory to reconcile against, so new jobs merge rather than duplicate.

## Operating procedure

Order matters: forces are mapped per candidate job, so candidates must be isolated first; dimensions come last because they need the accumulated quote evidence.

1. **Sweep for candidate jobs.** A job is a stable, solution-agnostic goal a person pursues in a specific situation; jobs do not change when products change. Flag moments where participants describe switching products, hiring a workaround, feeling frustrated a tool cannot do something, or repeating a task across multiple tools. Each is a candidate.
2. **Name each job verb-noun** ("reconcile monthly spend", "prove progress to my manager") — never a feature name.
3. **Map the four forces of progress per job**, each with at least one verbatim quote as evidence:
   - **Push** — frustration or limitation of the current solution that motivates change.
   - **Pull** — the appeal of a new or better way.
   - **Anxiety** — fear or uncertainty about switching or adopting.
   - **Habit** — inertia and comfort with the current way.
   Read the balance: a job with weak push and strong habit explains low adoption even when a solution is objectively better; strong pull with strong anxiety predicts trial without conversion.
4. **Write one job story per job**: "When [situation], I want to [motivation], so I can [expected outcome]." The situation must be specific — "when I am reviewing my team's output at the end of a sprint" beats "when I am at work". No solution language in any slot.
5. **Extract the three dimensions per job**, with evidence: Functional (what they literally need done), Emotional (how they want to feel or avoid feeling), Social (how they want to be perceived). If emotional or social evidence is missing across most jobs, the interviews did not go deep enough — flag it as a research gap rather than inventing it.
6. **Group and grade.** Cluster related jobs under a higher-order main job only where the data supports it. Tag each job Strong (3+ participants, direct quotes) or Inferred (1-2 participants, or read between the lines).

## Worked example

Bad job story: "When I open the dashboard, I want to use the export button, so I can get my data into the weekly report." (Situation is a screen, motivation is a feature, outcome is a task — nothing survives a product change.)

Good job story: "When I'm assembling the Monday status update for leadership, I want to pull last week's numbers without chasing three teams, so I can look on top of the business before the meeting starts."

Forces for that job, from transcripts: Push — "I spend Sunday night pinging people for numbers" (P3, P7). Pull — "if it just showed up compiled, I'd trust it more than what I stitch together" (P3). Anxiety — "if the tool pulls a wrong number, that's my credibility in the meeting" (P5). Habit — "my spreadsheet is ugly but I know exactly where everything is" (P2, P7). Dimensions: functional — compile cross-team metrics weekly; emotional — walk in feeling prepared, not exposed; social — be seen by leadership as in control.

## Deliverable

Produce a jobs inventory: for each job, a verb-noun title, the job story, a forces map (one bullet per force with a supporting verbatim quote and participant ID), the three dimensions with evidence, and a Strong/Inferred grade — with related jobs grouped under a main job where warranted, and a research-gaps note listing forces or dimensions the data could not support.

## Do NOT

- Do not embed solution language anywhere in a job story ("so I can use the dashboard to…") — it dates the job to the current product and kills its planning value.
- Do not accept generic situations. "When I'm at work" produces jobs that fit everyone and inform nothing.
- Do not fabricate emotional or social dimensions the transcripts do not contain; report the gap instead.
- Do not present a force without a quote. An evidence-free forces map is the analyst's opinion in a framework costume.
- Do not confuse a persona with a job — different people hire the same job, and one person hires many jobs.

## Quality bar

Before shipping: every job title is verb-noun and survives the test "would this job still exist if our product disappeared?"; every job story has a specific situation and zero solution language; every force cited carries a verbatim quote with participant attribution; each job is graded Strong or Inferred; missing dimensions are flagged as research gaps, not filled in.

## Escalation

When the transcripts will not support forces or dimensions, the fix is upstream: route to interview-guide-builder to design switch-interview questions before extracting again. For cross-cutting themes rather than jobs, use interview-synthesis; to turn jobs into audience profiles, hand the inventory to user-persona; to communicate results to stakeholders, pair with research-readout.
