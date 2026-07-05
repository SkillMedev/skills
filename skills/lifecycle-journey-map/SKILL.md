---
name: Lifecycle Journey Map
description: Builds a customer lifecycle journey map with behavioral entry/exit criteria per stage, one goal and one signal per stage, message briefs, and dead-zone flags. Use when someone asks "map our customer lifecycle", "where are the gaps in our lifecycle emails", "what should we send at each stage", or is aligning a team on lifecycle messaging strategy. Do NOT use to write the actual email sequence - use email-drip-builder for onboarding drips, win-back-campaign for dormant users, or push-notification-copy for push messages; this skill produces the map those campaigns are built on.
---

# Lifecycle Journey Map

A lifecycle map answers one question for every moment a user can be in: what should they feel, know, or do next? Get this right and every downstream campaign has a clear job; skip it and the team ships overlapping messages with no owner, no goal, and no way to know if they worked. The costly mistake this skill prevents is calendar-based messaging - sending on day 7 regardless of what the user actually did.

## Inputs to collect

1. **Product and its first value moment** - the action that proves a user got value (first export, first invoice sent, first report shared). If unknown, propose one and label it a guess to validate against activation data.
2. **Tracked events** - the map can only trigger on events the product already tracks. Get the event list before designing transitions.
3. **Drop-off data per stage transition** - signup→activation rate, activation→habit rate, renewal rate. If unavailable, mark the map "unvalidated" and make pulling this data step 6.
4. **Existing touchpoints** - every email, push, and in-app message currently sent, with its trigger.
5. **Channels available** - email, in-app, push, SMS.

## Operating procedure

Order matters: stages before goals, goals before triggers, triggers before messages - a message brief written before its stage has an exit criterion has no way to know when to stop sending.

1. **Define the stages.** Use five canonical stages unless the product demands otherwise: **Acquisition, Activation, Engagement, Retention, Win-Back**. Resist inventing custom names until the team has shipped at least one campaign per stage - shared vocabulary reduces meeting time.
2. **Write entry and exit criteria for every stage as behavioral events, not dates.** A user who completes their first export on day 14 is exiting Activation on day 14, regardless of signup date. Every criterion must be an event the product tracks today. If the needed event is not tracked, flag it as an instrumentation gap rather than substituting a timer.
3. **Assign exactly one primary goal and one measurable signal per stage.** Activation owns the first value moment. Engagement owns habit formation. Retention owns renewal or upgrade intent. Win-Back owns resurrection. Blurring goals produces messages that do nothing well.
4. **Write a message brief per stage** - maximum three sentences covering: the user's primary anxiety, the one thing the product should say, the channel, and the call to action. This brief is the input contract for any campaign built on the map (hand it to email-drip-builder or push-notification-copy).
5. **Mark dead zones.** A dead zone is any gap longer than **7 days** where the user receives nothing and has not reached the next milestone. Every dead zone is a churn risk and a candidate for a new touchpoint.
6. **Validate with drop-off data before building anything.** Pull the drop-off rate at each transition and prioritize the stage with the highest **absolute** user loss - not the highest percentage. A beautiful onboarding sequence helps no one if 60 percent of users churn at the paywall before reaching it.

## Worked example - B2B invoicing SaaS

| Stage | Entry criterion | Exit criterion | Primary goal | Signal | Message brief (anxiety → message → channel → CTA) |
|---|---|---|---|---|---|
| Acquisition | Visited pricing or started trial | Account created | Get a real signup | Trial signups/week | "Is this worth my time?" → show the 3-day-close outcome → landing/email → start trial |
| Activation | Account created | First invoice sent | Reach first value moment | % sending invoice ≤7 days | "Will setup be painful?" → one action: send your first invoice in 4 minutes → email + in-app → send invoice |
| Engagement | First invoice sent | 3+ invoices in 30 days | Build weekly habit | Weekly active senders | "Is this my system now?" → show time saved vs. old workflow → in-app → connect bank feed |
| Retention | 3+ invoices in 30 days | Renewal or upgrade | Secure renewal/expansion | Renewal rate, upgrade rate | "Am I getting full value?" → usage recap + unused feature → email → book review or upgrade |
| Win-Back | No invoice in 45 days | Invoice sent again, or 90 days lapsed | Resurrect the account | Resurrection rate | "Did I miss anything?" → what changed + one-click resume → email → resume where you left off |

Dead-zone check on this map: a user who creates an account but stalls before the first invoice hits day 8 with nothing scheduled - that gap gets a touchpoint before anything else ships, because Activation shows the highest absolute loss.

## Deliverable

Produce a journey-map table with one row per stage containing: behavioral entry criterion, behavioral exit criterion, one primary goal, one measurable signal, and a three-sentence message brief - plus a flagged list of dead zones (gaps >7 days), a list of untracked events blocking triggers, and a prioritized fix order based on absolute drop-off.

## Do NOT

- **Do NOT trigger transitions on calendar time.** Day-based stages misclassify every fast and slow user; behavior-based stages classify all of them correctly.
- **Do NOT give a stage two goals.** A message serving two goals serves neither, and the signal becomes unmeasurable.
- **Do NOT invent custom stage names before shipping one campaign per canonical stage** - vocabulary debates burn the time the map was meant to save.
- **Do NOT build campaigns before validating drop-off.** Fixing the wrong stage first wastes the quarter.
- **Do NOT let the map reference events the product does not track** - that is a fictional map.

## Quality bar

The map ships only if: every stage transition names a tracked behavioral event; every stage has exactly one goal and one signal; every message brief fits in three sentences and names anxiety, message, channel, and CTA; every gap over 7 days is flagged; and the fix order is ranked by absolute user loss with the data source cited (or the map is explicitly labeled unvalidated).
