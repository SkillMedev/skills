---
name: core-four-lead-engine
description: Pick the lead channels that fit a gym's stage, back daily activity out of a member goal, and build a 30-day lead plan across the Core Four (warm outreach, content, cold outreach, paid ads). Use when a gym owner says "I need more leads", "how do I get members", "what marketing should I do", "build a lead plan", "warm/cold outreach", or "what's a lead magnet". Do NOT use when the owner wants other people to bring in members (referrals, affiliate partners, agencies, employees) — use referral-and-affiliate-system instead; for paid-ad creative and budget depth, hand off to gym-meta-ads-funnel.
---

# Core Four Lead Engine

Turn "get more members" into a measured 30-day plan with daily activity targets across the four ways a gym can reach people itself.

There are only four ways to let people know about the gym, mapped by who you contact (knows you, or stranger) and how many at once (one, or many): warm outreach (knows you, one-to-one), content (follows you, one-to-many), cold outreach (strangers, one-to-one), paid ads (strangers, one-to-many). Every tactic is a version of one of these.

## Workflow

1. **Pick the starting channel by audience and budget.** Start where the gym has an unfair advantage. Existing audience (email list, social following, past members): start with warm outreach and content — free, fast, highest conversion. Money but no audience: start with paid ads against a front-end offer — buys reach now. Neither: start with cold outreach and content — costs time, not money, and builds the audience the other channels need. Pick one or two channels the owner can run daily without fail; add more later.

2. **Set the volume math.** Back daily activity out of the member goal. The funnel runs reach to lead to booked to show to close. Decide the goal and the conversion rates, then run `lead_volume.js` to compute leads needed and daily activity per channel. See `references/volume-math` for estimating each rate. The point is to make the goal concrete: "8 warm conversations and 21 cold outreaches a day" is a plan; "get more members" is not.

3. **Run each channel with a protected daily minimum.** Each channel works only with consistent volume. Warm outreach: message people who know you with a genuine check-in, then a soft offer — minimum is a fixed number of conversations a day. Content: post on a fixed cadence, each piece showing a problem the gym solves and a transformation — minimum is a cadence you never miss. Cold outreach: reach local strangers with a relevant opener and a free lead magnet — minimum is a fixed number of new outreaches a day. Paid ads: a daily budget against the front-end offer — hand off to gym-meta-ads-funnel for creative and budget.

4. **Use a lead magnet to convert attention into leads.** Cold and content traffic does not buy yet. Offer something valuable and free or cheap that reveals the problem the paid offer solves: a free body-composition scan reveals the gap; a macro calculator reveals the prospect has been guessing. A good lead magnet solves one narrow problem completely and makes the next step obvious. See `references/lead-magnet-design`.

5. **Build the 30-day lead plan.** Fill the template below: channels, the daily minimum for each, the lead magnet, and the owner of each channel. Review weekly against actual leads and adjust with the more-better-new sequence.

## Quality bar

- Every channel in the plan has a numeric daily minimum, not "post more."
- Daily targets trace back to the member goal through the funnel math, not guesses.
- At most one or two channels at launch; a new channel is added only after the current one runs consistently.
- The plan names an owner per channel and a weekly review with target-vs-actual.

## Do NOT

- Do not run all four channels at once from a standing start — none will get the volume it needs.
- Do not pitch the membership in cold or content traffic; lead with the lead magnet.
- Do not skip the math and set activity by feel; the daily count is the whole point.
- Do not build a referral or affiliate program here — that is getting others to sell for you; use referral-and-affiliate-system.

## Calculator

Self-contained Node script. Save as `lead_volume.js` and run with `node lead_volume.js`. No dependencies. Edit the inputs and the channel shares to match the gym.

```javascript
// Lead volume planner. Edit inputs, then: node lead_volume.js
const goal = {
  monthlyNewMembers: 20,
  workingDays: 22,
  // funnel conversion rates
  leadToBooked: 0.5,
  bookedToShow: 0.7,
  showToClose: 0.5,
}

// Per channel: share of total leads to source here, the native activity unit,
// and how many units of activity it takes to produce one lead.
const channels = [
  { name: 'Warm outreach', share: 0.30, unit: 'conversations', perLead: 5 },
  { name: 'Content', share: 0.20, unit: 'post impressions', perLead: 100 },
  { name: 'Cold outreach', share: 0.20, unit: 'cold outreaches', perLead: 20 },
  { name: 'Paid ads', share: 0.30, unit: 'ad leads (budget in ads skill)', perLead: 1 },
]

const shows = goal.monthlyNewMembers / goal.showToClose
const booked = shows / goal.bookedToShow
const leads = booked / goal.leadToBooked
const ceil = (n) => Math.ceil(n)

console.log('To reach', goal.monthlyNewMembers, 'new members per month you need:')
console.log('  Shows:  ', ceil(shows))
console.log('  Booked: ', ceil(booked))
console.log('  Leads:  ', ceil(leads))
console.log('Daily activity per channel (' + goal.workingDays + ' working days):')
for (const c of channels) {
  const channelLeads = leads * c.share
  const activityPerDay = (channelLeads * c.perLead) / goal.workingDays
  console.log(
    '  ' + c.name.padEnd(15),
    ceil(channelLeads) + ' leads/mo ->',
    ceil(activityPerDay), c.unit + '/day'
  )
}
```

### Worked example output

```
To reach 20 new members per month you need:
  Shows:   40
  Booked:  58
  Leads:   115
Daily activity per channel (22 working days):
  Warm outreach   35 leads/mo -> 8 conversations/day
  Content         23 leads/mo -> 104 post impressions/day
  Cold outreach   23 leads/mo -> 21 cold outreaches/day
  Paid ads        35 leads/mo -> 2 ad leads (budget in ads skill)/day
```

Read it: 20 members a month is 115 leads, which is 8 warm conversations and 21 cold outreaches every working day, content earning about 104 impressions a day, and ads producing 2 leads a day. The goal is now a daily checklist. Change the shares to match the channels the gym can actually run.

## Template: 30-day-lead-plan

```
30-DAY LEAD PLAN. [FILL: gym name]. Goal: [FILL] new members.

CHANNEL          DAILY MINIMUM            LEAD MAGNET            OWNER
Warm outreach    [FILL] conversations     [FILL]                [FILL]
Content          [FILL] posts/week        [FILL]                [FILL]
Cold outreach    [FILL] outreaches        [FILL]                [FILL]
Paid ads         $[FILL]/day              [FILL: offer]         [FILL]

WEEKLY REVIEW
  Leads target:   [FILL]      Actual: ____
  Booked target:  [FILL]      Actual: ____
  Adjust:         [FILL: which channel to do more / better / add]
```

## references/core-four-grid

```
                ONE TO ONE            ONE TO MANY
  WARM (know)   Warm outreach         Content
  COLD (strang) Cold outreach         Paid ads
```

Channel mechanics and gym examples:

- Warm outreach. Direct messages and calls to people who know you. Lead with a real check-in, not a pitch. Example: message past challengers who never joined, congratulate a recent life event, then invite them to the new cohort.
- Content. Posts, reels, and emails to your following. Show problem and transformation. Example: a 30-second client before-and-after with the obstacle they overcame.
- Cold outreach. Messages and calls to local strangers. Lead with a free lead magnet, not the membership. Example: offer a free InBody scan to a local office.
- Paid ads. Bought reach to strangers. Sell the front-end offer, not the gym. Depth in gym-meta-ads-funnel.

Scaling sequence (more-better-new), applied to each channel before adding the next:

1. More: increase daily volume of what already produces leads.
2. Better: improve the opener, the content, the offer, the targeting.
3. New: add another channel only once the current one is consistent.

## references/lead-magnet-design

A good lead magnet solves one narrow problem completely, is fast to consume, reveals the bigger problem the paid offer solves, and makes the next step obvious. Eight gym examples:

1. Free week of training.
2. Free body-composition (InBody) scan and readout.
3. Five-day fat-loss kickstart guide.
4. Macro and calorie target calculator with a sample day.
5. Front-end offer entry at a low price.
6. Free form-check session for one lift.
7. "Busy parent" 20-minute home workout pack.
8. Local corporate wellness lunch-and-learn.

Each one reveals a gap. A scan reveals body-fat the prospect did not know. A macro calculator reveals they have been guessing. The reveal creates the desire the paid offer fulfills.

## references/volume-math

Back into activity from the goal. Work the funnel in reverse:

- Members needed equals the monthly goal.
- Shows needed equals members divided by show-to-close rate.
- Booked needed equals shows divided by booked-to-show rate.
- Leads needed equals booked divided by lead-to-booked rate.
- Activity needed per channel equals that channel's lead share times its activity-per-lead, divided by working days.

Estimate rates from the gym's own history first. With no history, start with lead-to-booked 0.5, booked-to-show 0.7, show-to-close 0.5, and correct them after two weeks of real data. The exact numbers matter less than running the math and turning the goal into a daily count you can hit.
