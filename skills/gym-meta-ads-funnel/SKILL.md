---
name: gym-meta-ads-funnel
description: Write gym Meta (Facebook/Instagram) ad creative, structure the lead-to-consult funnel, set the budget, and read funnel costs against the challenge economics. Use when a gym owner asks to write Facebook/Instagram ads, says ads aren't converting or cost per lead is too high, asks what to spend on ads, or wants to build or scale a Meta ad funnel that fills a challenge or front-end offer. Do NOT use for general A/B creative-testing methodology — use ad-creative-testing instead; do NOT design the offer being sold or the consult close — use gym-transformation-challenge and closer-sales-script instead.
---

# Gym Meta Ads Funnel

Sell the challenge or front-end offer in the ad, route strangers into a short funnel that books a consult, and price the spend against what the offer can afford. The ad sells the challenge, never the gym itself.

## Workflow

1. **Set the offer and price first.** Ads amplify a good offer and cannot rescue a weak one. If the challenge, price, and guarantee are not defined, stop and define them (see gym-transformation-challenge for the offer, gym-pricing-and-guarantees for price and guarantee). Do not write a single ad before this is fixed.

2. **Build the shortest funnel that converts.** Every extra step loses people.
   - Ad: sells the challenge with a clear hook and offer.
   - Lead capture: a native Meta lead form (fast, low CPL, lower intent) or a landing page (higher intent, needs copy). Define the conversion event as the lead submission.
   - Automated booking: the instant a lead submits, fire a text plus a booking link for the intro consult.
   - Consult: the low-pressure intro where the challenge or membership is sold.
   - Track the full path — ad to lead to booked to shown to sold — not just to lead.

3. **Write the creative as hook + angle stack.** Every ad is hook, problem, mechanism, proof, offer, call to action.
   - Hook: stop the scroll; call out the avatar and the pain ("Busy mom with no time to train?", "Down to your last 15 pounds and stuck?").
   - Problem: name the pain in their words.
   - Mechanism: why this challenge works (small group, done-for-you plan, coaching).
   - Proof: a result, a number, a before-and-after.
   - Offer: the challenge, price, deadline, CTA.
   Produce 5 to 10 variations across angles (busy professional, postpartum, strength/over 40, weight loss, bridal/event, beginner, corporate, testimonial-led). Use the ad-script-pack below to draft, references/ad-anatomy for finished examples.

4. **Set targeting and budget.**
   - Targeting: a local radius around the gym (commonly 5 to 10 miles), the avatar's age range, broad interests. Let Meta optimize; do not over-narrow.
   - Budget: start with enough daily spend to gather signal, then let data accumulate. Run `ad_budget_planner.js` to turn the fill goal into daily spend, expected leads, and the breakeven cost per lead the offer allows.

5. **Read funnel costs, not vanity CPL.** Track cost per lead, cost per booked, cost per show, cost per sale. Judge against the breakeven from the planner. A higher CPL that produces members beats a cheap lead that never shows. See references/funnel-and-tracking.

6. **Scale or kill on signal, not on a single day.**
   - Scale a winner by raising budget 20 to 30 percent at a time once cost per booked is stable across several days. Large jumps reset learning.
   - Kill a creative whose cost per lead runs well above the set after enough spend to judge fairly. Replace it with a fresh angle.
   - Keep a queue of new angles so a winner's fatigue never leaves you dark.

7. **Hand the lead off immediately.** A booked lead is worthless if no one calls. The instant a lead submits, the follow-up clock starts; route it to objection-handling-and-speed-to-lead, where the rule is contact within minutes.

## Quality bar

- The offer, price, and guarantee are fixed before any ad is written.
- Every ad carries the full stack: hook, problem, mechanism, proof, offer, CTA — plus scarcity and urgency.
- The funnel tracks ad to lead to booked to shown to sold; decisions are made on cost per sale against breakeven, never on CPL alone.
- Scale and kill decisions wait for several days of stable signal, not one good or bad day.

## Do NOT

- Do not sell the gym, the equipment, or the trainers in the ad. Sell the challenge outcome.
- Do not over-narrow targeting or stack interests; broad plus a local radius lets Meta optimize.
- Do not judge ads by cost per lead alone — a cheap lead that never shows costs more than an expensive one that closes.
- Do not raise budget in large jumps or kill a creative on a single bad day; both throw away learning.
- Do not run ads against an undefined or weak offer expecting volume to fix it.

## Calculator

Self-contained Node script. Save as `ad_budget_planner.js` and run with `node ad_budget_planner.js`. No dependencies.

```javascript
// Meta ad budget planner. Edit inputs, then: node ad_budget_planner.js
const inputs = {
  monthlyFillGoal: 20,           // paid challengers wanted this month
  targetCpl: 25,                 // target cost per lead
  monthlyBudget: 1500,           // ad budget for the month
  leadToFill: 0.4,               // share of leads that become paid challengers
  challengePrice: 499,           // for the breakeven CPL
  fulfillmentPerChallenger: 120, // delivery cost per challenger
}

function plan(i) {
  const dailySpend = i.monthlyBudget / 30
  const expectedLeads = i.monthlyBudget / i.targetCpl
  const expectedFills = expectedLeads * i.leadToFill
  const leadsNeeded = i.monthlyFillGoal / i.leadToFill
  const budgetForGoal = leadsNeeded * i.targetCpl
  // Front-end breakeven CPL: price = cpl/leadToFill + fulfillment  ->  solve cpl.
  const breakevenCpl = (i.challengePrice - i.fulfillmentPerChallenger) * i.leadToFill
  return {
    dailySpend, expectedLeads, expectedFills, leadsNeeded, budgetForGoal, breakevenCpl,
  }
}

const r = plan(inputs)
const m = (n) => '$' + n.toFixed(2)
console.log('Daily spend:               ', m(r.dailySpend))
console.log('Expected leads at target:  ', Math.round(r.expectedLeads))
console.log('Expected challengers:      ', Math.round(r.expectedFills))
console.log('Leads needed for goal:     ', Math.ceil(r.leadsNeeded))
console.log('Budget to hit fill goal:   ', m(r.budgetForGoal))
console.log('Breakeven cost per lead:   ', m(r.breakevenCpl))
console.log(
  inputs.targetCpl <= r.breakevenCpl
    ? 'Verdict: target CPL is below breakeven. Front-end self-funds.'
    : 'Verdict: target CPL exceeds breakeven. Back-end must carry the loss.'
)
```

### Worked example output

```
Daily spend:                $50.00
Expected leads at target:   60
Expected challengers:       24
Leads needed for goal:      50
Budget to hit fill goal:    $1250.00
Breakeven cost per lead:    $151.60
Verdict: target CPL is below breakeven. Front-end self-funds.
```

Read it: $1,500 a month is $50 a day, which at a $25 cost per lead buys 60 leads and about 24 challengers, past the goal of 20. You only need $1,250 to hit 20, so there is room. Breakeven cost per lead is $151.60, so even if leads got six times more expensive the front-end would still cover itself — plenty of margin to test.

## Template: ad-script-pack

Eight fill-in ad skeletons. Each has two hook options; pick one and complete the stack.

```
AD 1. Avatar: busy professional
  Hook A: "[FILL: No time to train but tired of feeling out of shape?]"
  Hook B: "[FILL: Work 50 hours a week and still want to be fit?]"
  Problem:   [FILL]
  Mechanism: [FILL: 30-minute small-group sessions, done-for-you plan]
  Proof:     [FILL: client result]
  Offer:     [FILL: 6-week challenge, price, deadline] -> [CTA]

AD 2. Avatar: postpartum
  Hook A: "[FILL: Months after baby and your body still doesn't feel like yours?]"
  Hook B: "[FILL: Ready to feel strong again after baby?]"
  Problem / Mechanism / Proof / Offer: [FILL]

AD 3. Avatar: weight loss
  Hook A: "[FILL: Stuck on the same 15 pounds?]"
  Hook B: "[FILL: Tried every diet and nothing sticks?]"
  Problem / Mechanism / Proof / Offer: [FILL]

AD 4. Avatar: strength / over 40
  Hook A: "[FILL: Over 40 and losing strength every year?]"
  Hook B: "[FILL: Want to be the strong dad who keeps up?]"
  Problem / Mechanism / Proof / Offer: [FILL]

AD 5-8. Repeat the structure for: bridal/event, beginner/intimidated,
  corporate team, and a testimonial-led ad (lead with the client's words).
```

## Template: landing-page-copy

```
CHALLENGE LANDING PAGE. [FILL: challenge name]

HEADLINE:    [FILL: the promise, who it's for, the time frame]
SUBHEAD:     [FILL: the mechanism in one line]
BULLETS:     [FILL: 4-6 inclusions, each tied to an obstacle removed]
PROOF:       [FILL: 2-3 before/after results or testimonials]
GUARANTEE:   [FILL: from gym-pricing-and-guarantees]
SCARCITY:    [FILL: spots per cohort]
URGENCY:     [FILL: start date / doors close]
CTA:         [FILL: "Claim your spot" -> lead form]
```

## references/ad-anatomy

Every ad is hook, problem, mechanism, proof, offer, call to action. Twelve compact examples across four angles:

Busy professional:
1. "No time to train but sick of feeling unfit? Our 30-minute small-group sessions fit a lunch break. James, an accountant, dropped 18 pounds in 6 weeks. 6-Week Challenge, 20 spots, starts Monday. Book your spot."
2. "You schedule everything but your health. We schedule it for you. Coaching, plan, and sessions that fit a packed week. Start the 6-Week Challenge."
3. "Out of shape and out of time? 3 sessions a week, done-for-you nutrition, real coaching. See results in 6 weeks or we keep coaching you free."

Postpartum:
4. "Months after baby and your body still feels foreign? A coach, a plan, and moms in the same place. Sarah lost her baby weight in 6 weeks. Join the next cohort."
5. "Strong-mom comeback in 6 weeks. Small group, real support, child-friendly times. Spots are limited."
6. "You took care of everyone else first. Now it's your turn. Postpartum 6-Week Challenge, starts soon."

Strength / over 40:
7. "Over 40 and feeling weaker every year? Rebuild strength with a coach who programs for your body. 6-Week Strength Reset, 20 spots."
8. "Be the dad who keeps up, not the one on the sideline. Strength and energy in 6 weeks. Book a free intro."
9. "Lifting since forever but stuck? Coached programming and accountability. Start the 6-Week Reset."

Weight loss:
10. "Stuck on the same 15 pounds? It's not willpower, it's the plan. Done-for-you nutrition and coaching. Drop a size in 6 weeks or we coach you free."
11. "Tried every diet and nothing sticks? This time you get a coach. 6-Week Challenge, real results, real support."
12. "Down to your last stubborn pounds? Small-group coaching that finishes the job. Limited spots, starts Monday."

Each names the avatar, the pain, the mechanism, a proof point, and a clear next step with scarcity and urgency.

## references/funnel-and-tracking

Track the whole path, not the cheapest top-of-funnel number:

- Cost per lead. Ad spend divided by leads. Useful only alongside the rates below.
- Cost per booked. Spend divided by booked consults. Reveals whether leads are real.
- Cost per show. Spend divided by consults that show. Reveals follow-up quality.
- Cost per sale. Spend divided by members. The number that matters.

Rough local-gym benchmarks: cost per lead $5 to $40 depending on market, lead to booked around 50 percent, booked to show around 70 percent, show to sale 30 to 50 percent. Use your own numbers once you have two weeks of data. Compare cost per sale to the breakeven from `ad_budget_planner.js`, not to a vanity CPL.

## references/budget-and-scaling

- Starting budget: enough daily spend to gather signal, commonly $20 to $50 a day for one local gym. Too little and the data is noise.
- When to scale: raise budget 20 to 30 percent at a time once cost per booked is stable across several days. Large jumps reset learning.
- When to kill: cut a creative whose cost per lead runs well above the set after enough spend to judge fairly. Do not kill on one bad day.
- Keep a queue of new angles so there is always a fresh test when a winner fatigues.
