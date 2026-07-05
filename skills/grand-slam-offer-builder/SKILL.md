---
name: grand-slam-offer-builder
description: Build or sharpen a gym's core offer into a one-page canvas — Value-Equation score, problem-to-solution stack, scarcity/urgency/bonus amplifiers, and a MAGIC name — so the right prospect feels stupid saying no. Use when an owner says "build my offer", "my offer isn't converting", "what should I include", "make my offer irresistible", "design my challenge offer", or "name my program". Do NOT use when the task is choosing the price point or writing guarantee language — use gym-pricing-and-guarantees instead; do NOT use when the task is the day-by-day run-of-show for a challenge — use gym-transformation-challenge instead.
---

# Grand Slam Offer Builder

Turn a gym's front-end challenge and core membership into offers so strong the right prospect feels stupid saying no, by raising delivered value and lowering the cost in time and effort, then stacking solutions until perceived value dwarfs the price. Deliverable: a filled one-page offer canvas.

## Workflow

Run these steps in order. Do not skip the Value-Equation score; it tells you what to fix first.

1. **Score the current offer on the Value Equation.** Value rises with the dream outcome and the prospect's belief they will reach it, and falls with the time and effort it costs: `Value = (Dream Outcome x Perceived Likelihood) / (Time Delay x Effort and Sacrifice)`. Rate the offer 1-10 (10 = best) on four levers: Dream outcome (how badly they want the result), Perceived likelihood (how believable the result is for them), Speed (how fast results feel — inverse of time delay), Ease (how little effort it demands — inverse of effort). Run `value_score.js` (below). It multiplies the four and names the lowest lever. Fix that lever first: the equation is multiplicative, so the weakest lever caps the whole offer no matter how strong the others are. See references/value-equation for how to raise each one.

2. **Build the offer from problems, not features.** Name the dream outcome in the prospect's own words ("drop two dress sizes before the wedding and keep it off"). List every problem on the path (no time, no idea what to eat, intimidated by the gym floor, tried before and quit, no accountability). Turn each problem into a solution stated as the thing you provide ("no idea what to eat" → "a done-for-you meal plan with a swap list"). Choose a delivery vehicle for each (group session, 1:1 check-in, app, printed guide, private community, text support) that fits the value and your cost to deliver. Trim and stack: cut anything low-value or expensive, then stack the survivors into one offer that reads as overwhelming value for the price. See references/offer-construction for full worked examples on both the challenge and the membership.

3. **Enhance with the four amplifiers, only after the core stack is strong.** They multiply a good offer and do nothing for a weak one. Scarcity: a real capacity limit ("20 challengers per cohort because that is what our coaches can serve well"). Urgency: a real deadline ("cohort starts the 6th; doors close the 3rd"). Bonuses: extra wins that each remove a specific objection, each with a name and a stated value. Guarantee: hand off to gym-pricing-and-guarantees to choose the type and write the language, then drop the result into the canvas guarantee slot.

4. **Name the offer with MAGIC.** Pick the clearest two or three elements — you rarely need all five: Magnetic reason why (the hook or occasion: "Summer Shred", "New Year"), Avatar (who it is for: "Busy Moms", "Men Over 40"), Goal (the outcome: "Drop a Dress Size", "First Pull-Up"), Interval (the time frame: "6-Week", "28-Day"), Container word (the format: "Challenge", "Project", "Bootcamp", "Intensive"). See references/naming-magic for 15 worked gym names.

5. **Fill the offer canvas.** Complete the one-page canvas below. That is the deliverable the owner takes to their designer, landing page, and sales team.

## Quality bar

- The weakest Value-Equation lever is identified and explicitly addressed before any bonus is added.
- Every kept solution traces back to a named problem on the prospect's path; nothing in the stack starts from a feature.
- Stacked value visibly exceeds the price, and the canvas states the value-to-price framing.
- Scarcity and urgency reference a real capacity limit and a real deadline the owner will actually hold.
- The name passes the cold-read test: a stranger knows who it is for, what they get, and how long it takes.
- The price point and the guarantee language come from gym-pricing-and-guarantees, not invented here.

## Do NOT

- Do not start from features or a list of what the gym does; start from the outcome and work backward through obstacles.
- Do not add bonuses, scarcity, or urgency to prop up a weak core stack — fix the lowest lever first.
- Do not fake scarcity or urgency; a deadline you do not hold trains prospects to ignore you.
- Do not promise unsafe or unbelievable speed; engineer a believable fast first win instead.
- Do not set the price or draft the guarantee text here — defer both to gym-pricing-and-guarantees.

## Calculator

Self-contained Node script. Save as `value_score.js` and run with `node value_score.js`. No dependencies.

```javascript
// Value Equation scorer. Rate each lever 1-10 (10 = best). Run: node value_score.js
const ratings = {
  dreamOutcome: 8,        // how badly they want the result
  perceivedLikelihood: 4, // how believable the result is for them
  speed: 6,               // how fast results feel (inverse of time delay)
  ease: 5,                // how little effort it takes (inverse of effort)
}

const levers = {
  dreamOutcome: 'Dream outcome: make the promised result bigger or more specific',
  perceivedLikelihood: 'Perceived likelihood: add proof, testimonials, and a guarantee',
  speed: 'Speed: show or deliver a faster first win',
  ease: 'Ease: remove steps, do more for them, lower the effort asked',
}

const score = Object.values(ratings).reduce((a, b) => a * b, 1)
const pct = (score / 10000) * 100
const weakest = Object.keys(ratings).reduce((a, b) => (ratings[a] <= ratings[b] ? a : b))

console.log('Value score:', score, 'of 10000  (' + pct.toFixed(1) + '% of ceiling)')
console.log('Weakest lever:', ratings[weakest], '/10 ->', weakest)
console.log('Fix first:', levers[weakest])
```

With the ratings above the script prints:

```
Value score: 960 of 10000  (9.6% of ceiling)
Weakest lever: 4 /10 -> perceivedLikelihood
Fix first: Perceived likelihood: add proof, testimonials, and a guarantee
```

Read it: the offer promises a result people want (8) but they do not believe they will get it (4). No bonus fixes that. Raise belief with before-and-after proof, a results guarantee, and named coaches, and the whole score jumps because the equation multiplies.

## Template: offer-canvas

```
GRAND SLAM OFFER CANVAS. [FILL: offer name]

DREAM OUTCOME (in their words)
  [FILL]

PROBLEMS ON THE PATH            ->  SOLUTION  ->  DELIVERY VEHICLE
  [FILL problem]                ->  [FILL]    ->  [FILL]
  [FILL problem]                ->  [FILL]    ->  [FILL]
  [FILL problem]                ->  [FILL]    ->  [FILL]
  (add rows until every problem has a solution)

THE STACK (kept solutions, in value order)
  1. [FILL]                     value $[FILL]
  2. [FILL]                     value $[FILL]
  3. [FILL]                     value $[FILL]
  Total stacked value:          $[FILL]

PRICE
  Price:                        $[FILL from gym-pricing-and-guarantees]
  Value-to-price framing:       "[FILL: e.g. $2,400 of coaching for $499]"

AMPLIFIERS
  Scarcity:                     [FILL: real capacity limit]
  Urgency:                      [FILL: real deadline]
  Bonuses:                      [FILL: name + value, one per objection]
  Guarantee:                    [FILL from gym-pricing-and-guarantees]

NAME (MAGIC)
  [FILL: e.g. 6-Week Summer Shred Challenge for Busy Moms]
```

## references/value-equation

How to raise each lever for a gym:

- **Dream outcome.** Sell the identity and the life, not the workout. "Be the parent who keeps up with their kids" beats "lose 10 pounds." Make it specific and vivid. Tie it to an event the prospect already cares about.
- **Perceived likelihood.** Usually the weakest lever and the cheapest to fix. Add before-and-after photos of people who look like the prospect, written and video testimonials, named and credentialed coaches, a clear simple plan, and a guarantee. Each one raises belief.
- **Speed (lower time delay).** Engineer an early win in week one: a measurable result, a body-composition scan, a personal record. Show the timeline so the result feels close. Show a believable fast first win; never promise unsafe speed.
- **Ease (lower effort and sacrifice).** Do more for them. Provide the meal plan rather than nutrition theory. Book their sessions. Lay out the exact steps. Every decision you remove lowers the effort they feel.

Because the equation multiplies, a 2 on any lever caps the offer no matter how strong the other three are. Always fix the lowest lever first.

## references/offer-construction

Worked example, challenge offer:

- Dream outcome: drop a dress size and feel confident in 6 weeks.
- Problems and solutions: no time → 30-minute semi-private sessions; no diet plan → a done-for-you meal plan with swaps; intimidation → a small same-goal group and a dedicated coach; quitting → weekly check-ins and a group chat; no proof of progress → a start and finish InBody scan.
- Vehicles: semi-private sessions, printed plus app meal plan, private group chat, weekly 1:1 check-in, two body scans.
- Stack and price: the stack reads as well over a thousand dollars of coaching; the challenge is priced (by gym-pricing-and-guarantees) and anchored against that stacked value.

Worked example, membership offer (the ascension):

- Dream outcome: keep the result and make it a lifestyle.
- Stack: 3 or 4 semi-private sessions per week, quarterly body scans, ongoing nutrition adjustments, the community, member events, and a re-test guarantee.
- Vehicle and price: monthly recurring membership, priced on the ongoing transformation and the community, not per session.

The pattern is the same for both: outcome first, problems next, solutions and vehicles, then trim and stack.

## references/naming-magic

The MAGIC formula combines a Magnetic reason why, the Avatar, the Goal, the time Interval, and a Container word. You rarely need all five; pick the clearest two or three. Fifteen gym examples:

1. 6-Week Summer Shred Challenge
2. 28-Day Dress Size Project for Busy Moms
3. Men Over 40 Strength Reset
4. New Year First Pull-Up Challenge
5. Postpartum Comeback 8-Week Program
6. Bridal Body 90-Day Intensive
7. Beach Body Bootcamp for Beginners
8. Drop-a-Size 6-Week Challenge
9. Dad Bod Demolition 42-Day Project
10. Holiday Hold-the-Line Maintenance Challenge
11. Couch to Confident 6-Week Start
12. Strong Mom Semi-Private Program
13. Back-to-School Busy Parent Reset
14. First Responders 6-Week Fitness Challenge
15. Over-50 Mobility and Strength Project

Test a name by reading it cold: a stranger should know who it is for, what they get, and how long it takes.
