---
name: Usability Test Plan
description: Plans a usability study — research questions, realistic task scenarios with success criteria, metrics (completion rate, time on task, SEQ, SUS), a recruiting screener, and moderated vs. unmoderated guidance. Use when someone says "plan a usability test", "write task scenarios for our prototype", "how many participants do we need", or is about to put an interface or prototype in front of users. Do NOT use for open-ended discovery interview guides — use interview-guide-builder instead; for analyzing quantitative A/B experiments, use ab-test-analyzer.
---

# Usability Test Plan

A test plan is the contract between the research question and the study execution. Without one, teams run sessions, gather impressions, and call it usability testing. With one, findings are defensible and actionable.

## Define the Research Questions First

Before writing tasks, state 2-4 specific research questions the test must answer. Examples: 'Can users complete onboarding without assistance?', 'Do users understand the permission model before granting access?'. Tasks flow from questions — not the other way around. Reject any task that does not map to a research question.

## Writing Task Scenarios

Task scenarios must be realistic, motivated, and free of interface terminology. Good: 'You need to share the monthly report with your manager so she can review it before the board meeting. Please show me how you would do that.' Bad: 'Use the Share button to export the document.' Give participants context and a goal, not instructions. Each task should have a clear end state the observer can recognize.

## Success Criteria and Metrics

For each task, define: completion rate (binary: completed/not), time on task, error count, and any task-specific criteria (e.g., selected the correct permission level). Post-task, use a single-question scale — SEQ (Single Ease Question, 1-7) is the standard. Post-session, use SUS (System Usability Scale, 10 items) for a comparable satisfaction score. Define 'success' thresholds before testing, not after seeing results.

## Recruiting

Write a screener that targets the actual user population, not a convenient approximation. Specify: primary criteria (role, behavior, experience level), disqualifying criteria (competitor employees, UX researchers, anyone who has seen the prototype), and the target n. For most formative studies, 5 participants per distinct user segment surfaces the majority of usability issues. Moderated remote studies need 45-60 minutes per session; unmoderated can be shorter.

## Moderated vs. Unmoderated

Moderated is the default for complex flows, early-stage prototypes, or when the team needs to understand the 'why' behind failures. Unmoderated is appropriate for validating specific tasks on mature interfaces at higher sample sizes. Never run unmoderated tests on low-fidelity prototypes — participant confusion cannot be disambiguated from usability issues.

## Plan Deliverables

The written plan includes: research questions, participant profile and screener, task list with scenarios and success criteria, session guide outline, metrics table, and logistics (tool, recording consent, incentive).
