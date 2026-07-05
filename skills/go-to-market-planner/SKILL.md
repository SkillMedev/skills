---
name: Go-To-Market Planner
description: Build a sequenced go-to-market plan - beachhead ICP, positioning, one or two channels, and dated milestones - instead of a scattershot launch. Use when someone asks "how do I take this to market", "what's my GTM strategy", "which channel should I start with", "who should I sell to first", or is planning a new product or a new market entry. Do NOT use for the dated launch-week timeline and asset checklist - use launch-plan-sequencer instead; for Product Hunt copy and day-of tactics use product-hunt-launch; for deep persona research use icp-persona-builder.
---

# Go-To-Market Planner

A GTM plan is a series of explicit bets about who you serve, why they switch, and where you reach them. The costly mistake this skill prevents is the scattershot: five channels, three audiences, no milestone that would prove any bet wrong - which burns six months of runway learning nothing. Sequence beats simultaneity: concentrate force on one beachhead, win it, then expand.

## Operating procedure

Follow the order - each step inherits the previous one. A channel chosen before the ICP is a guess; a milestone set before the channel is a wish.

### Step 1: Gather inputs

Collect before planning; use the defaults and label guesses as guesses.

1. The product and the painful moment it removes (one sentence).
2. Any existing traction: users, revenue, waitlist, warm audience. Default: zero.
3. Founder advantages: audience, network, domain credibility, distribution access.
4. Runway and capacity: how many months and how many people can execute. Default: solo founder, 20 hours/week on GTM.
5. Price point and sales motion implied by it (self-serve under ~$100/month; sales-assisted above ~$5k/year).

### Step 2: Pick the beachhead ICP

Define the ideal customer by firmographics plus the trigger that creates urgency - "50-200-person agencies" is a segment; "50-200-person agencies that just lost a client over reporting errors" is a beachhead. Then name who you are not for, in writing; focus is the whole game early. A good beachhead is: reachable through one channel, feels the pain weekly, can buy without a committee, and is big enough for the first revenue milestone but small enough to dominate (roughly 1,000-10,000 reachable accounts). Hand deep persona work to icp-persona-builder.

### Step 3: Write the positioning

One line: for [ICP] who [need], [product] is the [category] that [key benefit], unlike [alternative]. Anchor against the real alternative - often a spreadsheet or doing nothing, not the venture-backed competitor. If this line takes more than one attempt to say aloud, refine it with positioning-statement before proceeding.

### Step 4: Build the messaging hierarchy

One core promise, supported by three pillars, each with proof. Translate features into outcomes the ICP already wants. Every asset downstream - landing page, cold emails, launch posts - reuses this spine; route the full ladder to messaging-hierarchy and page copy to landing-page-copy.

### Step 5: Choose one or two channels

Pick 1-2 channels where the ICP already congregates; don't spread thin. For each channel specify three things: the message, the offer, and the success metric. Match channel to motion:

- Founder network + outbound (cold-email-craft) when deal size supports conversations (above ~$2k/year).
- Community/content/SEO when the ICP searches or gathers publicly and price is self-serve.
- Paid only after one organic channel has proven the message converts - paid amplifies a working message, it does not find one.

Give a channel 6-8 weeks and a numeric kill threshold before adding another.

### Step 6: Sequence the milestones

Three phases, each with a falsifiable gate:

- Phase 1 - Validate (weeks 0-6): 20 ICP conversations, 5-10 design partners or paying pilots. Gate: at least 40% describe the pain unprompted as top-three.
- Phase 2 - Launch (weeks 6-10): coordinated one-week launch to the warm list built in Phase 1. Hand the dated timeline to launch-plan-sequencer and Product Hunt assets to product-hunt-launch.
- Phase 3 - Repeat (weeks 10-24): prove one repeatable channel. Gate: 10 customers acquired through the channel without a founder relationship, at an acceptable CAC (check with unit-economics).

Define the one metric that tells you each bet is working; if a metric can't fail, it isn't a gate.

## Worked example

Product: flaky-test detection for CI. Beachhead: engineering managers at 50-300-person SaaS companies running GitHub Actions, triggered by a release delayed by test flakiness (not "all developers"). Not for: enterprises with dedicated DevEx teams, hobbyists. Positioning: for EMs whose releases stall on flaky CI, FlakeGuard is the CI add-on that quarantines flaky tests automatically, unlike retry scripts that hide the problem. Channels: (1) founder-led outbound to EMs who posted about CI pain - offer a free flakiness audit, metric: 15% reply rate; (2) one deep teardown post per month in engineering communities, metric: 50 signups/post. Milestones: 20 EM conversations by week 4; 8 design partners by week 8; launch week 10 via launch-plan-sequencer; 10 non-network customers at under $500 CAC by week 24.

## Deliverable

Produce a one-page GTM plan containing: the beachhead ICP with trigger and an explicit not-for list, the one-line positioning, the messaging spine (promise, three pillars, proof), 1-2 channels each with message/offer/success metric and a kill threshold, and three phase-gated milestones with dates and numbers.

## Do NOT

- Do not target "anyone who…" - a beachhead you can't list 100 named accounts for is not a beachhead.
- Do not launch to a cold room; Phase 1 exists to build the warm list Phase 2 spends.
- Do not run three channels at once pre-revenue; attribution becomes noise and nothing gets enough reps.
- Do not set milestones without numbers; "get traction" cannot fail, so it cannot teach.
- Do not skip the not-for list - every choice ties back to the ICP, and if it doesn't serve them, cut it.

## Quality bar

- The ICP includes a trigger event, not just firmographics.
- The positioning names a real alternative the ICP actually uses today.
- Each channel has all three of message, offer, and success metric, plus a kill threshold and date.
- Every phase gate is falsifiable - a specific number by a specific week.
- The plan fits on one page; if it doesn't, the bets aren't explicit yet.

## Escalation

Route the dated launch arc and asset checklist to launch-plan-sequencer, Product Hunt execution to product-hunt-launch, channel deep-dives to channel-strategy, and market-size sanity checks to market-sizing.
