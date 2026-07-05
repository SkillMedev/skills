---
name: Survey Designer
description: Designs unbiased surveys - question types, Likert and frequency scales, screener-to-demographics flow, and a cognitive pilot plan - that produce trustworthy quantitative data. Use when someone says "write survey questions about X", "review my survey for bias", "how long should this questionnaire be", or wants to quantify attitudes, behaviors, or satisfaction. Do NOT use for full study design with hypotheses, sampling method, and power analysis - use primary-research instead; for qualitative interview scripts, use interview-guide-builder; for segment profiles built from the results, use user-persona.
---

# Survey Designer

A poorly designed survey produces precise measurements of the wrong thing - and because the numbers look rigorous, teams act on them with more confidence than they ever would on a hunch. This skill covers question construction, scale selection, and flow decisions that determine whether survey data is trustworthy, and it catches the leading, loaded, and double-barreled questions that quietly bias results before fielding, when fixes are free.

## Inputs to collect

1. The decision the survey informs, and the 2-4 constructs to measure (satisfaction, frequency, intent). A survey without a decision attached collects trivia.
2. The audience and how they will be reached (panel, in-product intercept, email list). This sets the length budget.
3. Expected responses. Under ~100 completes, differences between groups will rarely be meaningful - flag it and consider qualitative methods instead.
4. Any questions that must be kept for trend continuity - historical comparability beats wording perfection; never rewrite a tracking question mid-trend.
5. Length budget. Default: under 10 minutes for general audiences, under 5 for transactional intercepts. Roughly 3-4 closed questions per minute, so a 10-minute survey is ~30 closed items maximum.

## Operating procedure

Order matters: constructs before questions, questions before flow, pilot before fielding - reversing any pair produces rework or, worse, fielded bias.

1. **Map each construct to question types.** Closed questions (single-select, multi-select, scale) for anything that must be quantified and compared; open-ended sparingly - they inflate completion time and require qualitative analysis. A well-designed survey is mostly closed with one or two open "anything else" fields. Matrix questions look efficient but inflate satisficing (respondents clicking down one column); cap at 5 rows.
2. **Choose scales.** For attitudes and satisfaction: 5-point Likert, labeled endpoints, neutral midpoint. Do not reverse-code items without a clear analytical need - it confuses respondents more than it catches inattention. For frequency: concrete anchors ("never", "once a month", "once a week", "daily"), never vague ones ("rarely", "sometimes", "often") - respondents map vague anchors to different realities. Reserve 0-10 NPS for likelihood-to-recommend only; never repurpose it as a general satisfaction scale.
3. **Run every question through the bias tests:**
   - *Leading* - embeds an assumption. Fails: "How much did you enjoy the new feature?" Passes: "How would you rate your experience with the new feature?"
   - *Loaded* - carries emotional framing. Fails: "Do you agree that the cluttered interface slows you down?"
   - *Double-barreled* - asks two things at once. Fails: "The product is easy to use and saves me time." Always split.
   - *Social desirability* - sensitive topics get third-person framing: "Some users find X useful, others do not - which best describes you?"
   - Every option list must be exhaustive and mutually exclusive; include "Other" or "Prefer not to say" where honest answers might fall outside the options.
4. **Order the flow.** Screener first to qualify respondents; behavioral questions next (easier, less sensitive); attitudinal after; sensitive and demographic questions last - respondents who abandon mid-survey still yield usable data from earlier questions.
5. **Check the budget.** Count questions against the per-minute rule from the inputs. Over budget: cut questions, don't compress wording.
6. **Pilot cognitively.** 3-5 participants think aloud while completing the survey. Watch for confused wording, missing answer options, scale-direction errors, and unexpected drop-off points. Fix everything found, then field. Never skip this to hit a deadline - a biased question fielded to 2,000 people cannot be un-asked.

## Worked example: one question, bad and good

Bad: "How satisfied are you with our fast new search and improved filters?"
(a) Very (b) Somewhat (c) Not really

Good: "How satisfied or dissatisfied are you with the search experience?"
Very dissatisfied / Somewhat dissatisfied / Neither / Somewhat satisfied / Very satisfied

The bad version is leading ("fast", "improved"), double-barreled (search and filters), and uses an unbalanced 3-point scale with no dissatisfied endpoint - it can only produce flattering data. The good version isolates one construct on a balanced, labeled 5-point Likert scale. Ask about filters in a separate question.

## Deliverable

Produce a field-ready survey document containing: the decision and constructs it measures; the full question list in flow order (screener → behavioral → attitudinal → sensitive/demographic), each question tagged with its construct and scale type; the estimated completion time against the length budget; and a pilot plan (3-5 think-aloud participants, what to watch for, go/no-go criteria).

## Do NOT

- Do not field without a cognitive pilot - post-hoc fixes invalidate comparisons with already-collected responses.
- Do not use vague frequency anchors; "often" means daily to one respondent and monthly to another, and the aggregate means nothing.
- Do not exceed 5 matrix rows or the 10-minute budget; both directly inflate satisficing and abandonment.
- Do not repurpose the NPS 0-10 scale for general satisfaction.
- Do not reword a tracking question mid-trend; the break in comparability costs more than the improved wording gains.
- Do not read open-ended verbatims as quantitative evidence - they are hypotheses for the next study.

## Quality bar

Before shipping: every question passes all four bias tests; every scale is balanced with labeled endpoints; every option list is exhaustive and mutually exclusive; flow runs screener → behavioral → attitudinal → sensitive; estimated completion is inside the budget; the pilot plan exists with named criteria; every question traces to a construct and the construct to the decision.

## Escalation

Sampling strategy, power analysis, and hypothesis structure belong to primary-research - pull it in when the stakes justify formal design. For interpreting fielded results, route to sql-to-insights or ab-test-analyzer as fits; to explore *why* behind the numbers, pair with interview-guide-builder. Surveys touching health, legal, or regulated topics may need ethics/IRB review before fielding.
