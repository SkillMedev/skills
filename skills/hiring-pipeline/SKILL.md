---
name: Hiring Pipeline
description: Designs an end-to-end hiring pipeline from job requisition through debrief and offer, with stage-by-stage conversion benchmarks, a reverse-funnel capacity plan, and time-to-fill targets. Use when someone asks "design our hiring process", "how many candidates do we need to make one hire", "our offers keep getting declined", "candidates are dropping out of our funnel", or "how long should hiring take". Do NOT use for writing the interview questions and rubrics for one loop - use interview-guide-builder instead - for writing the sourcing messages to candidates - use candidate-outreach-personalizer instead - for the scorecard artifact itself - use hiring-scorecard instead - or for the job post copy - use job-description-writer instead.
---

# Hiring Pipeline

An unstructured hiring process produces slow decisions, biased ones, and lost candidates - the top candidate accepts elsewhere during week three of a meandering loop. A structured pipeline reduces bias, compresses time-to-hire, and gives every candidate a consistent experience; the reverse funnel below also tells you, before you open the role, whether the top of the funnel is big enough to produce a hire at all.

## Operating procedure

The order is load-bearing: the scorecard (Step 1) must exist before sourcing, because every later stage evaluates against it, and interviewers cannot own attributes that were never defined.

### Step 1: Write the requisition and scorecard

Collect from the hiring manager before anything opens:

- Mission of the role - what success looks like in 6-12 months, one paragraph.
- Must-have vs nice-to-have skills - keep must-haves to 3-5. Long lists deter great candidates, especially from underrepresented groups.
- Scorecard - the 4-6 attributes every interviewer will evaluate against, with what "strong" looks like for each. This is the backbone of an unbiased loop; pair with hiring-scorecard to build it.
- Comp band and level - agreed before sourcing, never negotiated ad hoc mid-process.
- Target start date - this sets the time-to-fill clock.

### Step 2: Plan the funnel backward from one hire

Use these stage-conversion benchmarks to size the top of funnel; treat them as planning defaults and replace with your own data once you have it:

```
Stage                        Typical conversion   Cumulative per hire
Applicants -> recruiter screen     10-20%          ~100-200 applicants
Recruiter screen -> HM screen      ~50%            ~16-20 screens
HM screen -> full loop             50-65%          ~8-10 HM screens
Full loop -> offer                 25-33%          ~3-4 loops (onsites)
Offer -> accept                    80-90%          ~1.2 offers
```

Sourced/referral candidates convert 3-5x better than cold applicants at the early stages, which is why channel mix matters. Time-to-fill norms: 35-45 days for most individual-contributor roles, 45-60 for senior engineering and leadership. If the plan implies more than 60 days, cut loop length or widen sourcing now, not in week six.

### Step 3: Source

- Write an inclusive job post: outcomes over credentials, no jargon, salary range visible (route the copy to job-description-writer).
- Source from multiple channels - referrals, communities, direct outreach - to widen the funnel; personalize outreach via candidate-outreach-personalizer.
- Track funnel metrics by stage and by source from day one so drop-off is visible weekly.

### Step 4: Screen

- Recruiter screen (30 min): motivation, basics, comp alignment, logistics. Surfacing the comp band here prevents week-five offer shocks.
- Hiring-manager screen (30-45 min): role fit plus a couple of substance questions.
- Ask the same questions of every candidate at each stage - consistency is what makes comparison legitimate.

### Step 5: Run the interview loop

Design a loop of 4-5 interviews maximum where each interviewer owns specific scorecard attributes with no overlap:

```
- Technical / craft            -> attributes: [FILL]
- Systems / problem solving    -> attributes: [FILL]
- Collaboration / values       -> attributes: [FILL]
- Domain / role-specific       -> attributes: [FILL]
```

Rules: each interviewer knows their attributes, uses consistent questions and a rubric per attribute (build these with interview-guide-builder), and submits a written, evidence-based rating BEFORE the debrief. Ratings shared early become groupthink. Compress the loop into 5-10 business days end to end; a loop that drags for weeks loses the best candidates to faster companies.

### Step 6: Debrief and decide

- Run a structured debrief: each interviewer presents rating plus evidence, lowest-tenure voices first.
- Apply an explicit bar: strong signal on all must-have attributes, no serious concerns.
- Record hire / no-hire with reasons. "Didn't feel like a fit" is not data; require the attribute and the evidence.
- The hiring manager makes the call; the loop informs it.

### Step 7: Offer and close

- Extend the offer within 2-3 business days of the final interview - great candidates have options, and every day of silence costs accept-rate.
- Sell honestly against the motivations the candidate stated in the recruiter screen.
- If offer-accept falls below 80%, diagnose: comp band mis-set at Step 1, a slow process, or a poor closing conversation.

## Worked artifact: pipeline conversion table

A real funnel review for a senior backend role, week 6:

```
Stage                    Count   Conversion   Benchmark   Verdict
Applicants                 140
Recruiter screens           24       17%        10-20%    on track
HM screens                   9       38%        ~50%      LEAK - screen bar unclear
Full loops                   5       56%        50-65%    on track
Offers                       1       20%        25-33%    slightly low, small n
Accepts                      0        0%        80-90%    LEAK - offer sat 6 days

Time elapsed: 42 days. Action: rewrite recruiter-screen pass criteria against
the scorecard; pre-approve comp so offers go out within 48h of debrief.
```

The table localizes the problem to two seams instead of the vague "hiring is hard."

## Deliverable

Produce a hiring-pipeline design: the requisition with scorecard and comp band, the reverse-funnel capacity plan with per-stage targets, the stage sequence with owners and timeboxes, the interviewer-to-attribute matrix, the debrief protocol and decision bar, and the weekly conversion table template with benchmarks alongside actuals.

## Do NOT

- Do not open sourcing before the scorecard exists - interviews become unstructured chats and bias fills the vacuum.
- Do not let interviewers compare notes before written ratings are in; early consensus is groupthink wearing a debrief costume.
- Do not run a 7-round loop over multiple weeks - every added round costs candidates and adds little signal past round 5.
- Do not use "culture fit" as a gut check; replace it with explicit values-based questions tied to scorecard attributes.
- Do not negotiate level or comp band ad hoc after the loop; set it in Step 1.
- Do not read the funnel only in aggregate - a healthy blended conversion can hide one broken source or stage.

## Quality bar

- Must-haves number 3-5 and the scorecard has 4-6 attributes with "strong" defined for each.
- Every interview attribute has exactly one owner; no overlaps, no orphans.
- The funnel plan shows per-stage targets against the benchmark table and a time-to-fill target of 45 days or less (60 for senior roles).
- Ratings are written and submitted before any debrief discussion.
- Every no-hire records the attribute and evidence, not a vibe.
