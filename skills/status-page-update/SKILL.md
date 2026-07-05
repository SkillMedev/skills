---
name: Status Page Update
description: Writes customer-facing incident status updates for each lifecycle stage - investigating, identified, monitoring, resolved - with cadence rules and plain-language templates. Use when someone asks "write a status page update", "what do we tell customers about this outage", "draft the resolved notice", or during an active incident needing customer communication. Do NOT use for the internal post-incident analysis - use postmortem-writer once the incident is closed; for deciding severity and internal escalation flow use sev-triage; for summarizing an incident upward to executives use escalation-summary.
---

# Status Page Update

Customers do not need the stack trace. They need to know what is broken, whether the team knows about it, and when it will be fixed. Every update earns trust or burns it - and the two fastest ways to burn it are silence (no post while customers are clearly affected) and a premature "resolved" that gets reopened. This skill produces updates that are fast, honest, specific about scope, and never blame anyone.

## Inputs to collect

Before writing any update, get:

1. **The observable symptom** - what customers actually experience, in their terms ("API requests returning 500s"), not internal terms ("the ingest shard is degraded").
2. **The affected scope** - which customers, which region, which feature. "Customers using API key authentication in us-east-1," never "some customers."
3. **The stage** - investigating, identified, monitoring, or resolved. The stage dictates the template and what may be promised.
4. **Timestamps** - incident start, and the timezone convention (UTC with label, or the user's timezone).
5. **The cause in plain language** - only for identified and later; never speculate before then.

## Cadence rules

Cadence is as load-bearing as the words:

- **First post within 15 minutes** of confirming customer impact. A late first post reads as "they didn't know."
- **Updates every 30-60 minutes** while the incident is active - 30 for SEV1, 60 acceptable for lower severities.
- **Every update states its next-update time and keeps it.** If there is nothing new, post anyway: "No new information yet; next update in 30 minutes." A kept promise with no news beats silence with news.
- **Monitoring window before resolving: usually 30-60 minutes** of sustained normal operation. A premature resolve followed by re-open destroys more trust than a longer outage.
- **Postmortem commitment: within 48-72 hours** for SEV1/2 - and only commit if one will actually be published.

## Operating procedure

1. Confirm customer impact and severity, then post the **Investigating** update within 15 minutes - a cause is not required to post.
2. Post on the cadence above, adding at least one new fact per update or explicitly saying there is none.
3. Move to **Identified** the moment root cause is understood, even if the fix is not deployed.
4. Move to **Monitoring** once the fix is deployed; name the metric being watched.
5. Move to **Resolved** only after the sustained-normal window; include actual start/end times, duration, and scope.
6. Route the follow-up: postmortem-writer for the public/internal analysis, escalation-summary for the executive rollup.

## Template set

### Investigating (post within 15 minutes; cause not required)

> We are investigating [SYMPTOM] affecting [WHO/SCOPE]. Customers may experience [OBSERVABLE IMPACT]. We will post an update within [30/60] minutes.

Rules: never speculate on cause; never promise a fix time; always give a concrete next-update time and keep it.

### Identified (root cause understood, fix may still be in flight)

> We have identified the cause of [SYMPTOM] as [PLAIN-LANGUAGE CAUSE - no jargon, no internal system names]. We are [SPECIFIC ACTION IN PROGRESS] and expect [OUTCOME] by [TIME]. Next update by [TIME].

Rules: plain-language cause only; acknowledge if impact was broader than previously stated; no blame - "a configuration error in our database layer," never "a vendor pushed a bad change" or "human error by an engineer."

### Monitoring (fix deployed, not yet resolved)

> We have deployed a fix for [SYMPTOM]. [METRIC OR SIGNAL] has returned to normal levels. We are monitoring to confirm stability and will resolve this incident once we are confident in the fix.

Rules: state what is being watched; give a realistic resolve window (usually 30-60 minutes); do not rush to resolve.

### Resolved (after sustained normal operation)

> This incident is resolved. [SYMPTOM] affected [SCOPE] from [START TIME] to [END TIME] ([DURATION]). We have confirmed normal operation. A full postmortem will be published within [48-72 hours].

Rules: actual times in UTC or the customer's timezone, labeled; commit to a postmortem only if it will ship.

## Tone rules

- 7th-grade reading level; no acronyms without expansion.
- "We," never "the team" or passive voice - passive voice is where blame-dodging hides.
- No blame language, ever: not vendors, not individuals, not "unexpected behavior by a third party." Own the customer experience regardless of fault.
- Do not say "sorry for the inconvenience." Say "We understand this impacts your work and we are moving as fast as possible."
- Every update carries a timestamp plus either a next-update time or a resolution statement.

## Deliverable

Produce the update for the current stage - filled template, timestamp, scope statement, and next-update commitment - plus, when the incident spans stages, the full sequence of posts with their cadence times.

## Do NOT

- **Do NOT wait for a cause to post the first update** - the 15-minute clock beats the diagnosis.
- **Do NOT use a vague cause** ("due to an unexpected issue"). Give at least "a configuration error in our database layer."
- **Do NOT blame** vendors, individuals, or "third-party issues" - customers hear excuse, not explanation.
- **Do NOT omit impact scope** - "some customers" tells every customer it might be them.
- **Do NOT post back-to-back Investigating updates with no new information** without saying so explicitly.
- **Do NOT resolve early** - a re-opened incident costs more trust than a longer honest one.
- **Do NOT promise a fix time during Investigating** - promise the next update instead.

## Quality bar

An update ships only if: it names the symptom in customer terms and the scope precisely; it carries a timestamp and a next-update time or resolution statement; it contains zero jargon, internal system names, or blame; the first post landed within 15 minutes and subsequent posts hit the 30-60 minute cadence; and Resolved posts include real start/end times, duration, and a postmortem commitment only if genuine.
