---
name: buyer-nurture-sequence
description: Keeps not-yet-ready homebuyers warm for months with timeline-based segmentation, listing alerts with a why-this-one note, a monthly market-update touch, the pre-approval nudge, and re-engagement triggers. Use when an agent says "keep my buyer leads warm", "these buyers aren't ready yet", "build my buyer drip", or "leads keep going cold and buying with someone else". Do NOT use for generic marketing email drips — use email-drip-builder instead — or for post-open-house first contact, which is open-house-follow-up.
---

# Buyer Nurture Sequence

Most buyer leads are not ready today — they are ready in four to ten months, and they hire whichever agent is still usefully present when readiness arrives. The costly mistake this skill prevents is the binary treatment of leads: work the hot ones, forget the rest. The forgotten ones buy anyway, with someone else, and the agent re-pays acquisition cost for a lead they already owned.

Worked example throughout: Priya, a residential agent in a suburban market, 14 transactions last year at a $485,000 average sale price, building toward 24. Her open houses (see open-house-follow-up, the upstream skill that feeds this one) produce 3-5 nurture entries each; over a year that is a pool of roughly 60-80 not-yet-ready buyers. Converting even 8-10 of them is most of the gap between 14 and 24 transactions — at her $485k average price, each converted nurture lead is a five-figure commission event that cost nothing new to acquire.

## Operating procedure

1. **Segment every incoming lead by stated timeline** before anything else — cadence, content, and the call schedule all depend on it, so an unsegmented lead cannot be nurtured, only spammed. Intake carries the handoff notes from open-house-follow-up (or wherever the lead originated): timeline, financing status, criteria, and anything they said.
2. **Set up the listing-alert-plus-context play** for every segment. This is the core mechanic.
3. **Schedule the monthly market-update touch** for the whole pool.
4. **Place the pre-approval nudge** at the segment-appropriate moment.
5. **Wire the re-engagement triggers** so behavior, not the calendar, promotes leads between segments.
6. **Review the pool monthly:** re-segment anyone whose behavior or replies changed, and prune only hard opt-outs — never prune for silence.

## Inputs to collect

- Lead source and handoff notes (from open-house-follow-up: lane, timeline, financing, criteria; label anything inferred rather than stated as a guess).
- Search criteria: price band, beds/baths minimums, locations by commute or district boundary, must-haves. Confirm criteria with the lead in the first nurture touch — handoff notes drift.
- Financing status: pre-approved / in progress / not started.
- Channel preference: SMS vs email (default: alerts by email, personal notes by SMS, calls per the cadence).
- A CRM or alert system that can tag segments and log touches. Without one, this system dies in week three — set it up first.

## Segmentation and cadence

| Segment | Timeline | Cadence | Content mix |
|---|---|---|---|
| A — Near | 0-3 months | Weekly, plus real-time alerts | Alerts with context, showing offers, pre-approval nudge NOW if not done |
| B — Mid | 3-6 months | Every 2 weeks, plus alerts | Alerts with context, monthly market update, pre-approval nudge at ~month 4 before promotion to A |
| C — Far | 6-12 months | Monthly | Market update, occasional standout alert only, education touches (costs, process, timing) |

Promotion rules: B→A at 90 days before stated target or on a re-engagement trigger; C→B likewise. Demotion is fine too — a lead who says "we pushed the move to next spring" moves down a segment, not off the list. Segment A leads showing active behavior stop being nurture leads: work them personally, this skill's job is done.

## The listing-alert-plus-context play

Raw portal alerts are wallpaper — the buyer already gets them from Zillow. The nurture version adds one or two sentences on WHY this listing fits what they said:

```
Subject: This one's worth a look — [FILL: address]

[FILL: name] — this just listed at $[FILL] and it clears your two big
ones: [FILL: their criterion #1, e.g. "the fenced yard"] and [FILL:
criterion #2, e.g. "under 10 minutes to the station"]. The trade-off
is [FILL: honest drawback, e.g. "the smaller second bathroom"].
Want a look before the weekend?
```

Rules: only send listings that genuinely fit (one wrong alert costs more trust than five right ones earn); always include the honest drawback (it is the credibility mechanism — an agent who names trade-offs is an advisor, not a spammer); Segment A gets these same-day, B within a couple of days, C only for genuine standouts.

## The monthly market-update touch

One email, whole pool, personalized only by area. Contents: median sale price movement in their target area, days-on-market trend, inventory count in their band, rate movement in one neutral sentence, and one plain-language takeaway ("inventory in the $450-500k band is the tightest it's been all year — good listings are moving in under two weeks"). Facts and property data only — never neighborhood demographic commentary. This touch is why silence never means removal: it keeps permission alive at near-zero cost.

## The pre-approval nudge

The single highest-leverage nurture action: a buyer who gets pre-approved has done the psychological work of becoming a buyer, and pre-approval surfaces budget reality months before it can kill a deal. Timing — Segment A immediately, Segment B around month 4, Segment C education-only until promoted. Frame as intel, not obligation:

```
No commitment in this — but a 20-minute pre-approval call tells you your
exact number, locks your file so you can move fast when the right one
appears, and it's free. Homes like the ones you're watching have been
going under contract in [FILL: local DOM] days; buyers without a letter
lose those. Want an intro to a couple of lenders I trust?
```

Offer two or more lender names, per RESPA — steering to a single lender for anything of value is a compliance line, not a style choice.

## Re-engagement triggers

Behavior promotes leads faster than any calendar. Watch for, and act same-day on:

- **Alert engagement spike** — multiple opens/clicks in a week → personal SMS: "You clicked on the Fernwood listing a few times — want to walk it?"
- **A reply of any kind** — even "thanks!" → move the conversation to a question about their timeline.
- **Their criteria-matching listing takes a price cut** → alert with context: "This one just dropped to $479k — inside your band now."
- **Rate movement that changes their payment meaningfully** → one plain-fact note tied to their band.
- **Stated-timeline approach** — 90 days before their target date → promotion plus the pre-approval nudge.
- **Six months of total silence in Segment C** → one direct re-permission text: "Still thinking about a move in the next year, or should I stop the updates?" A "stop" is a clean list; a reply is a re-engagement.

## Thresholds

- Time-to-first-nurture-touch after handoff: under 48 hours, or the handoff momentum from open-house-follow-up is wasted.
- Open rate on market updates: 35-50 percent is healthy for a permissioned pool; below 25 percent, the content is generic — audit the takeaway line.
- Expected annual conversion of a well-run nurture pool: 10-15 percent of entries transact with the agent within 18 months. Below 5 percent, the alerts are unfitted or the triggers are not being worked.
- Alert fit discipline: if a lead dismisses 3 consecutive alerts, stop and re-confirm criteria — the profile has drifted.

## Deliverable

A running nurture system: every lead tagged A/B/C with confirmed criteria, alert feeds with context notes, the monthly market update scheduled, pre-approval nudges placed by segment, re-engagement triggers wired, and a monthly review habit. The output over time is Segment A promotions handed to the agent as active buyers.

## Do NOT

- Do not send raw listing links without the why-this-fits note — that is Zillow's job, done worse.
- Do not run one cadence for all timelines — weekly contact with a 10-months-out buyer is how opt-outs happen.
- Do not prune for silence — silence is timing; only a "stop" removes someone.
- Do not fake urgency ("this one won't last!") — one false alarm ends the trust the whole system runs on.
- Do not push pre-approval on Segment C in month one — nudge at readiness, educate before that.
- Do not recommend or filter listings by neighborhood demographics, and never characterize areas by who lives there — criteria are price, property, and commute facts.
- Do not run this from memory or a spreadsheet of good intentions — no system, no nurture.

## Quality bar

- Every lead has a segment, confirmed criteria, and a next scheduled touch.
- Every alert sent includes fit context and an honest trade-off.
- Every re-engagement trigger fires a same-day human action, not another automated email.
- The monthly update contains at least one concrete local number and one plain takeaway.
- Zero touches that carry no value.

## Escalation and compliance

Real estate is a licensed profession. Everything sent must be accurate; fair-housing rules prohibit steering and demographic targeting language — match buyers to properties by their stated criteria, never by area demographics, and describe listings by property and amenities, not the people nearby. Lender referrals follow RESPA (multiple names, no kickbacks). SMS/email cadences must honor consent and opt-outs. Legal and brokerage compliance review applies to templates before first use. Upstream: leads arrive from open-house-follow-up with triage notes. Adjacent: homeowners in the pool who start asking "what's mine worth" are seller leads — route them to seller-lead-nurture.
