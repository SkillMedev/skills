---
name: Segmentation Strategy
description: Builds a behavioral segmentation system for lifecycle messaging - RFM-based segments, event-driven membership triggers, one next action per segment, size audits, and send-time suppression rules. Use when someone asks "how should I segment my users", "our emails get ignored, who should get what", "how do I message at-risk users differently", or is personalizing lifecycle campaigns to cut unsubscribes and lift click-through. Do NOT use for building ad-platform audiences - use audience-targeting instead; for narrative customer personas use user-persona or icp-persona-builder; for mapping the full lifecycle stages and journeys use lifecycle-journey-map.
---

# Segmentation Strategy

Segmentation is the discipline of sending fewer, better messages to the right people. The goal is not many segments; it is segments that produce meaningfully different messages - because every segment that would receive the same email as its neighbor is pure operational cost, and a bloated segment map is why lifecycle programs stall at "monthly newsletter to everyone."

## Operating procedure

Follow in order: the behavioral axes (Step 2) must be chosen before segments are named (Step 3), and triggers (Step 4) cannot be written until each segment exists with a definition.

### Step 1: Gather inputs

Collect before designing; label estimates as guesses.

1. Product type: transactional (purchases) or engagement-based (sessions, actions). This decides whether the M in RFM is money or engagement depth.
2. The product events available in the messaging tool today - segments can only trigger on events that actually fire.
3. Current list size and rough activity distribution (what share was active in the last 7 and 30 days).
4. The first value moment for a new user, if known ("created first project", "completed first order").
5. Team capacity: how many distinct campaigns can realistically run at once. Default assumption: few.

### Step 2: Start with RFM, not demographics

Recency, Frequency, and Monetary value - or engagement depth for non-transactional products - are the three axes that predict future behavior most reliably. Demographics explain who users are; RFM explains what they are likely to do next. Build behavioral segments first and layer demographics on only where they change the message itself (a pricing-page email that differs by company size, for example).

### Step 3: Define no more than six active segments

More than six is operationally unsustainable for most teams. The six that matter, each mapping to a lifecycle stage and a distinct message strategy:

1. New Users (days 0-14) - get them to the first value moment.
2. Active Power Users - deepen, upsell, recruit for advocacy.
3. Casual Active Users - build the habit toward power use.
4. At-Risk Users (declining engagement) - intervene before dormancy.
5. Dormant Users - reactivation offers.
6. Churned - win-back or list hygiene; pair with win-back-campaign.

### Step 4: Tie membership to product events, not calendars

A segment is only as good as the signal that puts someone in it. Use behavioral triggers: a user becomes At-Risk when their weekly session count drops below their personal average for two consecutive weeks - not "hasn't opened an email in 30 days". Fall back to time-based cutoffs only where behavioral data genuinely does not exist, and mark those definitions as temporary.

### Step 5: Assign each segment exactly one next action

For every segment, define the single action that moves the user to a healthier segment. The segment brief must fit one sentence: move user from X to Y by doing Z. For New Users, Z is reaching the first value moment; for At-Risk, it is completing the specific feature their usage dropped away from. A segment without a next action is a report, not a strategy.

### Step 6: Install the suppression hierarchy

When a user qualifies for multiple segments, apply a fixed priority order and suppress lower-priority sends. Recommended priority: Churned/win-back < Dormant < Casual < Power < At-Risk < New User (highest - onboarding always wins). Two messages in the same week from different segments erode trust faster than receiving none.

### Step 7: Audit sizes monthly

- A segment under 2 percent of the list is too granular to justify a dedicated campaign - merge it into the nearest neighbor.
- A segment over 40 percent of the list is too broad to produce relevant messaging - split it on the strongest available behavioral axis.
- Track the At-Risk-to-Active recovery rate as the system's headline metric; segments exist to move people, and this is the proof they do.

## Worked artifact: segment definition, bad vs good

Bad:

```
Segment: "Engaged users"
Definition: opened an email in the last 30 days
Campaign: monthly product newsletter
```

Email opens measure email behavior, not product behavior; a 30-day window is a calendar cutoff, not a signal; and "newsletter" is not a next action.

Good:

```
Segment: At-Risk Users
Entry trigger: weekly_session_count below personal 8-week average
               for 2 consecutive weeks AND was Casual or Power previously
Exit: any week back at or above personal average (-> prior segment),
      or 4 more weeks of decline (-> Dormant)
Next action: complete [top feature from their own usage history]
Brief: move user from At-Risk to Casual Active by re-engaging the one
       feature that anchored their previous usage.
Campaign: 3-touch sequence over 10 days, feature-specific deep link,
          suppressed if a New User or higher-priority sequence is live.
Size guardrail: expect 5-15% of list; investigate the product, not the
                segment, if it exceeds 25%.
```

## Deliverable

Produce a segmentation spec containing: the six (or fewer) segment definitions with entry and exit triggers on named product events, one next action and one-sentence brief per segment, the suppression priority order, the monthly size-audit thresholds (2 percent merge line, 40 percent split line), and the recovery-rate metric that judges the system.

## Do NOT

- Do not lead with demographics; they rarely change what the message should say.
- Do not exceed six active segments - the seventh always ships a worse version of an existing message.
- Do not define segments by email engagement (opens, clicks); that measures the channel, not the customer.
- Do not let a user receive two segment campaigns in the same week; suppression is not optional.
- Do not keep a sub-2-percent segment alive for sentimental reasons, or leave a 40-percent-plus blob unsplit because splitting is work.

## Quality bar

The spec ships only when: every segment has an event-based entry and exit condition (or a labeled temporary time-based fallback); every segment's brief fits the move-X-to-Y-by-Z sentence; the suppression order is total (any pair of segments has a winner); and the size audit and recovery-rate review are on a named owner's monthly calendar.
