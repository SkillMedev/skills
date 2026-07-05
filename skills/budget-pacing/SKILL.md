---
name: Budget Pacing
description: Paces and reallocates ad budget across channels through the month to hit both CAC and volume targets, with daily tolerance bands, a reallocation reserve, and escalation red lines. Use when someone asks "are we pacing to spend the budget", "we're overspending and it's only the 12th", "should I move budget from Meta to Google mid-month", "how do I spend the rest of the budget without wrecking CAC", or is managing live campaign spend at month start or month end. Do NOT use for explaining why last month's spend missed plan - use budget-vs-actual instead - or for auditing whether the channels themselves are worth funding - use paid-acquisition-audit instead.
---

# Budget Pacing

Budget pacing is spending the right amount at the right time to meet both volume and efficiency targets. Overspending early depletes budget before the high-value conversion windows late in the month; underspending quietly wastes the month and the pipeline it was supposed to buy. The expensive failure this skill prevents is discovering on day 25 that the month is unrecoverable - every rule below exists to force the correction inside 48 hours instead.

## Operating procedure

Order matters: targets must exist before monitoring means anything, and monitoring must be daily before reallocation is safe.

### Step 1: Gather inputs

Collect before the month begins. Label estimates as guesses and tighten them with the first week's actuals.

1. Total monthly budget and the CAC target (or target range) per channel.
2. Volume target: conversions, leads, or purchases expected for the month.
3. Day-of-week seasonality from history - e.g., if Tuesday-Thursday convert 20% better, those days get weighted higher. If no history exists, start flat and label it a guess.
4. Channel list with trailing 30-day CAC and an honest read on remaining impression volume per channel.
5. The business's risk tolerance for over/under-delivery, which calibrates the escalation thresholds below.

### Step 2: Set pacing targets before day 1

Divide total monthly budget by calendar days for the baseline daily target, then reweight by the seasonality curve. Set a daily tolerance band of ±15% around each day's target. Hold back a reallocation reserve of 10-15% of the monthly budget, unallocated to any channel - this reserve is what funds mid-month shifts when one channel outperforms. Do not pre-commit all budget to channels at month start.

### Step 3: Monitor spend AND conversion daily

Pacing spend without tracking conversions is half the job. Every weekday morning check four numbers: spend vs. daily target, cumulative spend vs. cumulative target, CAC vs. target, and projected month-end spend at current pace.

Decision rules:

- Spend outside the ±15% band on consecutive days: adjust bids or campaign budgets within 48 hours. Never wait until day 25 to react.
- CAC above target while spend is on pace: slow spend rather than letting the month "average out" - it won't.
- CAC below target and volume below target: the green light to accelerate. Push reserve budget into the channels and campaigns demonstrating efficiency.

### Step 4: Reallocate across channels, not just within them

Reallocation cadence: channel-level decisions weekly, campaign-level decisions daily. The allocation rule: the channel with the best trailing 7-day CAC and available impression volume gets the next dollar. Both conditions must hold - a channel with great CAC but saturated inventory just inflates its own auction. Fund shifts from the reserve first; only cut a channel's committed budget when its trailing 7-day CAC has been above target for a full week.

### Step 5: Handle end-of-month pressure correctly

Do not increase bids or relax targeting to zero out the budget line in the final week. Underspending slightly beats inflating CAC to look fully spent. If budget remains in the final 5 days, move it only into campaigns already performing at or under CAC target - never into experiments or cold audiences, which need learning time the month no longer has. Document why budget went unspent; that note feeds the next planning cycle.

### Step 6: Escalate on the red lines

Escalate to stakeholders immediately when any of these trips:

- Projected month-end spend will miss target by more than 10% in either direction.
- CAC trends more than 20% above target for 5 consecutive days.
- A single channel exceeds 60% of total spend without prior approval (concentration risk).

These are starting points - calibrate to the business's risk tolerance gathered in Step 1, but never remove them entirely.

## Worked artifact: mid-month pacing check

```
PACING CHECK - day 14 of 30 - monthly budget $60,000 - CAC target $85

Cumulative target (day 14, seasonality-adjusted): $27,600
Cumulative actual:                                $31,900  (+15.6% - OUT OF BAND)
Blended CAC month-to-date:                        $92      (+8.2% vs target)
Projected month-end spend at pace:                $68,400  (+14% - ESCALATE, >10%)

Trailing 7-day CAC by channel:
  Google Search   $71   volume available: yes   → gets next dollar
  Meta            $104  volume available: yes   → cut daily budgets 20%
  LinkedIn        $128  above target 6 straight days → pause worst 2 campaigns

Actions (within 48h):
  1. Reduce Meta daily budgets 20%; keep Search flat.
  2. Move $3,000 of reserve into top-3 Search campaigns (all under $75 CAC).
  3. Escalation note to stakeholders: projected +14% spend, plan above,
     revised projection $61,500 by day 17.
```

Bad explanation: "We're a bit over but it should even out by month end."
Good explanation: "Cumulative spend is 15.6% over the seasonality-adjusted target, driven by Meta CPMs up 30% week-over-week; cutting Meta budgets 20% today and shifting reserve to Search, which is 16% under CAC target with inventory available."

## Deliverable

Produce a pacing dashboard and operating note containing: daily spend vs. daily target, cumulative spend vs. cumulative target, trailing 7-day CAC by channel, projected month-end spend at current pace, the reserve balance, and this week's reallocation decisions with rationale. Update it from the ad platform APIs where possible; review it every weekday morning and with stakeholders weekly. The dashboard must surface exceptions on its own - no hunting.

## Do NOT

- Do not pace spend without pacing conversions; a perfectly spent budget at 40% over CAC target is a failure that looks like success.
- Do not pre-commit 100% of budget to channels on day 1 - with no reserve, every mid-month reallocation requires clawing money out of a live campaign.
- Do not chase full spend in the final week by loosening targeting; the CAC damage outlives the month.
- Do not shift budget on a single hot day's CAC; use the trailing 7-day figure to avoid whipsawing the algorithms.
- Do not park end-of-month money in experiments; they burn budget during their learning phase and report nothing usable.

## Quality bar

- Daily targets exist before the month starts and reflect seasonality, not a flat divide, wherever history exists.
- Every out-of-band day has a corrective action logged within 48 hours.
- Reallocation decisions cite trailing 7-day CAC and available volume, both.
- All three escalation red lines are actively monitored and any trip produced a stakeholder note the same day.
- Unspent budget at month end carries a documented reason.

For the retrospective explanation of why the month landed where it did, hand off to budget-vs-actual. For questioning whether a channel belongs in the mix at all, use paid-acquisition-audit.
