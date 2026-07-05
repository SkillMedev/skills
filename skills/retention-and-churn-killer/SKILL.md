---
name: retention-and-churn-killer
description: Build a gym member-retention system — a first-100-days onboarding sprint, results-tracking cadence, attendance-based churn-save triggers, win-back sequences, and a churn-impact calculator. Use when a gym owner says members are quitting, retention is weak, onboarding is ad hoc, a member wants to cancel, or they want to win back cancelled members ("reduce churn", "first 100 days", "save a member", "win back"). Do NOT use for general SaaS or subscription churn analysis — use churn-reduction instead; do NOT model CAC/LTV economics here — use gym-money-model.
---

# Retention and Churn Killer

Engineer gym member retention: onboard new members into a habit, make results visible, catch and save members before they quit, and win back the ones who left.

## Workflow

Run these steps in order. Skip a step only when the owner already has that piece working.

### Step 1: Engineer the first-100-days onboarding sprint

Early usage predicts tenure: the more a member trains in their first month, the more they value the gym. Make the start a fixed sequence, not a hope. Produce the onboarding-runbook below with a named owner and date for each milestone.

1. Day 0: send a welcome message, book the first session, send the plan.
2. First session: run a baseline scan, confirm the goal, deliver one guaranteed early win. The member should leave proud they came.
3. Week 1: get three sessions booked and attended, introduce them to the group, surface one measurable result. Frequency builds the habit.
4. Day 7, 14, 30 check-ins: a short, specific coach conversation about progress and obstacles — never a vague "how's it going."
5. Day 30: review a visible, measured result. This converts a trial mindset into a member mindset.
6. Day 60–100: re-scan, celebrate the trend, set the next goal so the member always has a reason to keep going.

### Step 2: Track and show results

Members churn when they stop feeling the outcome they bought.

1. Measure: scans, measurements, lifts, attendance. What gets measured gets felt.
2. Check in on a schedule: regular coach conversations about progress and obstacles.
3. Make progress visible to the member: a trend graph of their scan results or a personal-record board turns effort into evidence. A member who can see they are winning does not quit.

### Step 3: Catch churn signals and run saves

Most members signal before they quit. Build the triggers below and the save play in save-and-winback-scripts.

1. Attendance trigger: no visit in 7–10 days fires a save outreach. Earlier is better — a member who has drifted two weeks is half gone.
2. Run the save conversation warm and curious, not desperate: find what changed, remove the obstacle, rebook the next session on the spot. Usually it is a schedule change or life event, not the gym.
3. For the other leading signals (missed check-ins, stalled results, life events, payment failures), apply the matching intervention in churn-signals below.

### Step 4: Build community and accountability

Community raises switching cost: a member who would cancel a service will not abandon friends and a coach who knows them.

1. Accountability: pair members, run small same-goal groups, set shared targets.
2. Belonging: member events, a private group, milestone celebrations, branded moments.
3. Recognition: celebrate results publicly. Recognition retains the recognized member and shows everyone else the path.

### Step 5: Quantify the prize

Run churn_model.js to show what a small churn improvement is worth in retained members, monthly and annual revenue, and LTV. The number usually dwarfs the cost of the onboarding and save work, making the case to invest in retention over more ads.

## Quality bar

- Every onboarding and check-in milestone has a named owner and a date, not just an intention.
- A churn save fires from an objective trigger (days since last visit), not a coach's gut feeling.
- Every retention claim made to the owner is backed by the calculator's numbers using their real member count, churn rate, and dues — not generic percentages.

## Do NOT

- Do NOT lead retention with discounts or freezes before fixing onboarding and results delivery — price cuts retain the wrong members and erode margin.
- Do NOT wait for a cancellation request to act; by then the member has usually already decided. Act on the attendance trigger.
- Do NOT run a desperate or guilt-based save conversation — it confirms the member's instinct to leave.
- Do NOT model CAC, LTV-to-CAC, or whether growth pays for itself here; that is gym-money-model's job.
- Do NOT apply this to non-gym subscription or SaaS churn; use churn-reduction.

## Calculator

Self-contained Node script. Save as `churn_model.js` and run with `node churn_model.js`. No dependencies.

```javascript
// Churn improvement impact. Edit inputs, then: node churn_model.js
const inputs = {
  memberCount: 150,
  currentChurnRate: 0.06,  // monthly
  improvedChurnRate: 0.04, // monthly, the target
  avgMembershipValue: 159, // monthly dues per member
}

function model(i) {
  const curTenure = 1 / i.currentChurnRate
  const newTenure = 1 / i.improvedChurnRate
  const lostNow = i.memberCount * i.currentChurnRate
  const lostNew = i.memberCount * i.improvedChurnRate
  const savedPerMonth = lostNow - lostNew
  const monthlyRevenueRetained = savedPerMonth * i.avgMembershipValue
  const annualRevenueRetained = monthlyRevenueRetained * 12
  const ltvCur = i.avgMembershipValue * curTenure
  const ltvNew = i.avgMembershipValue * newTenure
  return {
    curTenure, newTenure, savedPerMonth, monthlyRevenueRetained,
    annualRevenueRetained, ltvCur, ltvNew,
  }
}

const r = model(inputs)
const m = (n) => '$' + Math.round(n).toLocaleString('en-US')
console.log('Current avg tenure:    ', r.curTenure.toFixed(1), 'months')
console.log('Improved avg tenure:   ', r.newTenure.toFixed(1), 'months')
console.log('Members saved / month: ', r.savedPerMonth.toFixed(1))
console.log('Monthly revenue kept:  ', m(r.monthlyRevenueRetained))
console.log('Annual revenue kept:   ', m(r.annualRevenueRetained))
console.log('LTV per member:        ', m(r.ltvCur), '->', m(r.ltvNew))
```

### Worked example output

```
Current avg tenure:     16.7 months
Improved avg tenure:    25.0 months
Members saved / month:  3.0
Monthly revenue kept:   $477
Annual revenue kept:    $5,724
LTV per member:         $2,650 -> $3,975
```

Read it: dropping monthly churn from 6 to 4 percent stretches average tenure from 17 to 25 months. On 150 members that keeps 3 members and $477 every month — about $5,700 a year — and raises each member's lifetime value by over $1,300. That return usually beats spending the same effort on more ads, which is why retention is a growth lever, not just a defense.

## Template: onboarding-runbook

```
NEW-MEMBER ONBOARDING RUNBOOK. [FILL: gym name]

DAY 0 (owner: [FILL])
  [ ] Welcome message, first session booked, plan sent
DAY 1 / FIRST SESSION (owner: [FILL])
  [ ] Baseline scan, goal confirmed, a guaranteed early win
WEEK 1 (owner: [FILL])
  [ ] 3 sessions attended, group intro, first measurable result
DAY 7 CHECK-IN (owner: [FILL])
  [ ] Coach conversation: progress, obstacles, rebook
DAY 14 CHECK-IN (owner: [FILL])
  [ ] Adjust plan, confirm habit forming
DAY 30 CHECK-IN (owner: [FILL])
  [ ] Visible result reviewed, next goal set, referral ask if a win
DAY 60 / 100 (owner: [FILL])
  [ ] Re-scan, celebrate progress, set the next milestone
```

## Template: save-and-winback-scripts

```
SAVE (attendance dropped, still a member)
  Trigger: no visit in [FILL: 7-10] days
  TEXT:  "Hi [name], missed you this week. Everything ok? Let's get your next
          session on the calendar, what day works?"
  CALL (if no reply): warm check-in, find the obstacle, rebook on the call.
  IN-PERSON save: "What changed since you started? Let's fix that together."

WIN-BACK (already cancelled)
  Week 1:  "Hi [name], no pressure at all, just checking in. How are you doing
            with your training since you left?"
  Week 2:  value or result story matching their old goal.
  Week 4:  "We're starting a new [challenge] on [date]. I'd love to have you
            back, here's a spot if you want it: [link]."
```

## Reference: churn-signals

Leading indicators and the intervention for each:

- Attendance drop. The clearest signal: no visit in 7–10 days. Intervention: a warm save outreach and a fast rebook.
- Missed check-ins or ghosted messages. Intervention: switch channel (call instead of text) and ask directly, "what changed?"
- Stalled results. A member not seeing progress will quit. Intervention: a coach resets the plan and finds a fresh win.
- Life events (new job, baby, injury, move). Intervention: offer flexibility — a hold option instead of a cancel, or a modified plan. Often saves a member who would otherwise quit outright.
- Payment failures. Intervention: a prompt, friendly fix before it becomes a silent cancel.

The save conversation is curious, not desperate: find what changed, remove the obstacle, book the next session. Most "I want to quit" conversations are really "something got in the way," and the gym can fix the something.
