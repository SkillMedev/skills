---
name: referral-and-affiliate-system
description: Build a gym's member-referral engine and local affiliate, employee, and agency lead-getter system, with ask scripts, partnership terms, a volume projection, and a monthly cadence. Use when the owner says "get more referrals", "set up a referral program", "bring a friend", wants affiliate or local-business partners, asks "who else can send me leads", or wants coaches and staff to drive referrals. Do NOT use when the owner wants to generate leads through their own warm outreach, content, cold outreach, or paid ads — use core-four-lead-engine instead.
---

# Referral and Affiliate System

Turn members into a referral engine and recruit affiliates, employees, and agencies as outside lead-getters, then put all of it on the gym's monthly schedule so it runs by system, not by luck.

## Workflow

### Step 1: Gate on delivery
Confirm the gym produces real member results before building any referral push. Nobody refers a gym that did not work for them. If results are weak, stop and fix delivery and retention first, then return here.

### Step 2: Build a referral mechanism members actually use
1. Ask right after a win. The moment a member hits a result (a body-composition scan improvement, a personal record, a stated goal reached) is when they are proudest. Train coaches to ask in person at that moment, not on a random day.
2. Reward both sides. The referring member gets something and the joining friend gets something. Dual-sided incentives convert far better than one-sided.
3. Make the buddy challenge the default: "Bring a friend to the next 6-week challenge and you both get [reward]." A partner lowers the friend's friction and raises the member's own attendance.

Use the `referral-mechanics` reference below for ask scripts, timing, and incentive sizing.

### Step 3: Build local affiliate partnerships
1. Pick targets: complementary local businesses that serve the same avatar without competing — physiotherapists, chiropractors, nutritionists and dietitians, corporate HR and wellness leads, running and sports clubs.
2. Set a clear value exchange: they refer clients to your challenge; you refer members to their service, run a joint event, or pay a referral fee per joined member.
3. Track it: a unique code or link per partner, or a "who referred you" field at signup, so you know which partners produce and can double down.

Fill the `affiliate-outreach` template below for the first message and the terms.

### Step 4: Activate employees and agencies
1. Employees: give each coach and front-desk staffer a small referral and reactivation target, with a bonus tied to joined members, not to raw activity. Keep the consult and the member relationship in-house.
2. Agencies: bring in a marketing agency only to scale a proven offer and funnel (usually paid ads). Align them on cost per joined member, never on leads or impressions. Keep ownership of the strategy and the data.

See the `four-lead-getters` reference below for incentive alignment per type.

### Step 5: Put it in the monthly cadence
1. Add a buddy-referral push in week 5 of every challenge cohort, at peak results.
2. Have coaches ask for a referral at every logged win.
3. Run one affiliate outreach or check-in per week.
4. Review referred-member counts monthly.

Fill the `referral-program-sheet` template below to define the program and assign owners.

### Step 6: Project the volume
Run `referral_projection.js` to estimate referred members per month from the member base, referral rate, and close rate. Use it to set a realistic target and to size the incentive against the value of a member.

## Quality bar
- Delivery is verified before any referral ask goes out.
- Every incentive is dual-sided, and its cost is checked against member LTV from the projection.
- Every affiliate and partner is tracked by a unique code, link, or signup field — no untracked "they said they'd send people."
- Every recurring action (week-5 push, ask-at-win, weekly outreach, monthly review) has a named owner.
- The owner's own outreach, content, and paid-ads channels are left to core-four-lead-engine — this skill only covers people who get leads for the gym.

## Do NOT
- Do not ask for referrals on a schedule detached from wins; a cold "tell your friends" ask converts poorly.
- Do not run one-sided rewards or vague "discount" offers.
- Do not outsource the offer, the consult conversation, the member relationship, or the data. Outsource reach, not the core of the business.
- Do not promise affiliate fees you cannot track or pay per joined member.
- Do not size a reward without checking it against member LTV.

## Calculator

Self-contained Node script. Save as `referral_projection.js` and run with `node referral_projection.js`. No dependencies.

```javascript
// Referral projection. Edit inputs, then: node referral_projection.js
const inputs = {
  memberCount: 150,              // active members
  referralsPerMemberMonth: 0.15, // referred leads each member sends per month
  closeRate: 0.5,               // share of referred leads that join
  membershipLtv: 1558,          // contribution LTV per member (from money model)
}

function project(i) {
  const referredLeads = i.memberCount * i.referralsPerMemberMonth
  const newMembersMonth = referredLeads * i.closeRate
  const newMembersYear = newMembersMonth * 12
  const annualValue = newMembersYear * i.membershipLtv
  return { referredLeads, newMembersMonth, newMembersYear, annualValue }
}

const r = project(inputs)
console.log('Referred leads / month:   ', r.referredLeads.toFixed(1))
console.log('New members / month:      ', r.newMembersMonth.toFixed(1))
console.log('New members / year:       ', Math.round(r.newMembersYear))
console.log('Projected annual value:   ', '$' + Math.round(r.annualValue).toLocaleString('en-US'))
```

### Worked example output

```
Referred leads / month:    22.5
New members / month:       11.3
New members / year:        135
Projected annual value:    $210,330
```

Read it: a 150-member gym where each member refers just 0.15 friends a month produces about 22 referred leads, 11 new members a month, and 135 a year, worth roughly $210,000 in lifetime contribution at near-zero acquisition cost. Raise the referral rate with the buddy-challenge push and the ask-after-a-win habit, and this number climbs.

## Template: referral-program-sheet

```
REFERRAL PROGRAM. [FILL: gym name]

OFFER (dual-sided)
  Member who refers gets:   [FILL: e.g. one month free / store credit]
  Friend who joins gets:    [FILL: e.g. challenge at member rate]

ASK SCRIPT (used at a logged win)
  "[FILL: You just hit X. Who do you know who'd want this too?
   Bring them to the next challenge and you both get Y.]"

TIMING
  Trigger:                  [FILL: scan win, PR, goal reached]
  Cohort push:              week 5 of every challenge

TRACKING
  Mechanism:                [FILL: code / link / "who referred you" field]
  Owner:                    [FILL]
  Monthly target:           [FILL] referred members
```

## Template: affiliate-outreach

```
AFFILIATE OUTREACH. [FILL: partner type, e.g. local physio]

OPENER (message)
  "Hi [FILL name], I run [FILL gym] nearby. We work with a lot of the same
   people you do. I'd like to send my members to you for [FILL service], and
   offer your clients a free [FILL: body-composition scan / week of training]
   to get started. Open to a quick chat?"

PARTNERSHIP TERMS
  They send us:             [FILL: client referrals to the challenge]
  We send them:             [FILL: member referrals / joint event / fee]
  Referral reward:          [FILL: $X per joined member, or reciprocal]
  Tracking:                 [FILL: unique code per partner]
  Review cadence:           [FILL: monthly check-in]
```

## Reference: four-lead-getters

The four kinds of people who get leads for the gym, with mechanics and incentive alignment:

- Customers (referrals). Mechanics: members refer friends. Incentive: dual-sided reward, asked at a win. Highest trust, lowest cost — the first lead-getter every gym should build.
- Affiliates. Mechanics: complementary local businesses refer their clients. Incentive: reciprocal referrals, joint events, or a per-member fee. Works when both sides clearly gain and tracking is honest.
- Agencies. Mechanics: a vendor runs a channel (usually paid ads) for you. Incentive: paid per result or retainer. Use only to scale a proven offer and funnel; align on cost per member, not on leads or impressions.
- Employees. Mechanics: coaches and staff drive referrals and reactivations. Incentive: bonus tied to joined members. Reward joined members, not activity. Never outsource the consult or the member relationship.

Never outsource: the offer, the consult conversation, the member relationship, and the data.

## Reference: referral-mechanics

- Timing is the whole game. A referral asked at a peak result converts several times better than the same ask sent cold. Train coaches to ask the instant a member hits a win, in person, specifically: "Who do you know who would want this too?" A specific ask beats "tell your friends."
- Dual-sided incentives. Reward the referrer and lower the friend's friction at once — a month free for the member, the member rate for the joining friend. Size the reward against member value: when a member is worth over a thousand dollars in contribution, a month of dues as a reward is cheap.
- The buddy challenge. The strongest gym referral mechanic. In week 5 of every cohort, when results are visible, invite each challenger to bring a friend to the next cohort for a shared reward. The friend gets a built-in training partner, which raises attendance and retention for both.
