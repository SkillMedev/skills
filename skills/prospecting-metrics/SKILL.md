---
name: prospecting-metrics
description: Use when you need to turn outbound prospecting into a measurable, backsolvable funnel instead of guesswork - for quota planning, activity targets, funnel diagnosis, and A/B test sizing. Triggers include "how many emails do I need to send", "backsolve my quota", "what's a good reply rate", "how many meetings per 100 contacts", "my outbound isn't converting", "where is my funnel breaking", "is this A/B test significant", "how many contacts to hit pipeline target", "what activity do I need this week", and "what conversion rates should I expect for cold outbound". Workflow: define the canonical funnel, plug in your real (or benchmark) conversion rates, backsolve required weekly activity from a quota, diagnose which stage is broken, and size tests honestly. Do NOT use for writing the actual messages - use [[cold-email-craft]]; Do NOT use for designing the multi-step cadence itself - use [[outreach-sequence-designer]]; Do NOT use for fixing inbox placement - use [[cold-email-deliverability]].
---

# Measure & Backsolve Your Prospecting Funnel

Outbound is a math problem wearing a vibes costume. Every rep "feels" like things are working or not; almost none can tell you how many contacts it takes to book one held meeting, or how many contacts per week they need to hit quota. That gap is where pipeline targets quietly die. The core insight: a funnel is a chain of multiplied conversion rates, so a target at the bottom *dictates* the activity at the top - you don't guess your way to quota, you backsolve it.

The common trap is optimizing the wrong stage. Reps obsess over open rates (now nearly meaningless post-Apple Mail Privacy Protection, which pre-fetches and inflates opens) while ignoring that their *positive* reply rate is 0.4% because they're emailing the wrong persona. Measure the stages that actually predict revenue - positive reply rate, meetings-held-per-100-contacts, contact-to-SQO - and treat the rest as diagnostics, not goals.

## When to use this skill
- You have a pipeline or meetings quota and need to know the weekly activity (contacts, sequences) required to hit it.
- Your funnel is underperforming and you need to localize *which* stage is broken and what that points to.
- You want to project outcomes from a planned activity level ("if I send 500/week, what do I get?").
- You're about to call an A/B test a winner and want to know if you have the sample size to.
- You need realistic benchmark ranges to sanity-check whether a rate is good, bad, or fantasy.

## The funnel (and what each rate roughly is)

These are **rough industry ranges for cold B2B outbound, not promises** - yours depend on ICP, offer, and channel. Use them only to sanity-check, then replace with your own data ASAP.

| Stage | Transition | Rough cold-email range |
|---|---|---|
| Contacts loaded | → delivered | 92-99% (rest = bounces) |
| Delivered | → opened | *de-emphasize; MPP inflates it* |
| Delivered | → reply (any) | 1-5% |
| Reply | → positive reply | 20-35% of replies |
| Positive reply | → meeting booked | 50-70% |
| Meeting booked | → meeting held | 65-85% (no-shows kill you) |
| Meeting held | → SQO | 40-60% |
| SQO | → closed-won | 15-30% |

The ratios that actually matter (track these weekly):
1. **Positive reply rate** = positive replies / delivered. The cleanest signal of targeting + copy quality. Healthy cold: ~0.5-1.5%.
2. **Meetings-held per 100 contacts** = your single best efficiency number. Cold typically lands 0.5-2 per 100.
3. **Meeting-held rate** = held / booked. Below ~70% means a scheduling/confirmation problem, not a top-of-funnel one.
4. **Contact-to-SQO** = SQOs / contacts. The number you backsolve quota from.

## The workflow
1. **Define your funnel** end to end with the stages above. Drop stages you can't reliably measure (opens) rather than fake them.
2. **Populate conversion rates** - use your trailing 60-90 days if you have ≥ ~300 contacts of history; otherwise start with the benchmark ranges and label them ASSUMPTION.
3. **Backsolve the quota.** Start from the bottom target (meetings held, SQOs, or pipeline $) and divide back up through each rate to get required contacts, then ÷ weeks to get weekly activity.
4. **Sanity-check feasibility.** Required contacts/week ÷ realistic per-rep capacity (~250-500 fresh cold contacts/week/rep at quality) = headcount or rate-improvement needed. If the number is absurd, the fix is conversion rate, not more sends.
5. **Diagnose breaks** against benchmarks (see failure modes) and route to the right sibling skill.
6. **Test one variable**, sized for significance, before declaring anything.

## Funnel model template

```
TARGET (pick one, per quarter):
  meetings_held_target = ___   OR   pipeline_target_$ = ___ , avg_deal_$ = ___
weeks = 13

CONVERSION RATES (yours or benchmark - LABEL which):
  deliverability        = 0.97
  reply_rate            = 0.030   # replies / delivered
  positive_of_replies   = 0.28
  book_of_positive      = 0.60
  held_of_booked        = 0.75
  sqo_of_held           = 0.50
  win_of_sqo            = 0.22
  avg_deal_$            = 18000

DERIVED:
  contacts_per_held_meeting = 1 / (deliverability*reply_rate*positive_of_replies*book_of_positive*held_of_booked)
  required_contacts = (target chained back up) ; weekly = required_contacts / weeks
```

## WORKED EXAMPLE - backsolve from a pipeline number

Target: **$1,000,000 in new pipeline this quarter (13 weeks)**, avg deal $18k → defined as SQO value, so **SQOs needed = 1,000,000 / 18,000 = 55.6 → 56 SQOs.**

Walk *up* the funnel, dividing by each rate:
- Held meetings = 56 / 0.50 (sqo_of_held) = **112**
- Booked meetings = 112 / 0.75 (held_of_booked) = **149.3 → 150**
- Positive replies = 112 / (0.75 × 0.60) = 112 / 0.45 = **248.9 → 249**
- Total replies = 249 / 0.28 = **889**
- Delivered = 889 / 0.030 = **29,630**
- Contacts loaded = 29,630 / 0.97 = **30,546**

So: **≈ 30,550 contacts / 13 weeks ≈ 2,350 contacts/week.** At ~400 quality cold contacts/week/rep, that's **~6 reps** - or you fix conversion. Note the leverage: lifting positive-of-replies from 0.28 → 0.40 cuts required contacts by ~30% with zero extra sends. That's why copy/targeting beats volume almost every time.

Headline efficiency: contacts_per_held_meeting = 1 / (0.97×0.030×0.28×0.60×0.75) = 1 / 0.00367 = **~273 contacts per held meeting.**

## Runnable artifact - funnel solver (Node, no deps)

Save as `funnel.mjs`. Forward mode projects outcomes from activity; backsolve mode computes required activity from a target.

```js
#!/usr/bin/env node
// Usage:
//   node funnel.mjs backsolve --pipeline 1000000 --deal 18000 --weeks 13
//   node funnel.mjs backsolve --held 112 --weeks 13
//   node funnel.mjs forward   --contacts-week 2350 --weeks 13
// Override any rate, e.g. --reply_rate 0.04 --positive_of_replies 0.40
const RATES = {
  deliverability: 0.97, reply_rate: 0.030, positive_of_replies: 0.28,
  book_of_positive: 0.60, held_of_booked: 0.75, sqo_of_held: 0.50,
  win_of_sqo: 0.22, avg_deal: 18000,
};
const a = process.argv.slice(2);
const mode = a[0];
const opt = (k, d) => { const i = a.indexOf(`--${k}`); return i > -1 ? Number(a[i + 1]) : d; };
for (const k of Object.keys(RATES)) RATES[k] = opt(k, RATES[k]);
const weeks = opt("weeks", 13);
const r = RATES;
const c2held = r.deliverability * r.reply_rate * r.positive_of_replies * r.book_of_positive * r.held_of_booked;
const fmt = (n) => Number.isInteger(n) ? n : Math.ceil(n);
const money = (n) => "$" + Math.round(n).toLocaleString();

if (mode === "backsolve") {
  let held = opt("held", null);
  if (held == null) {
    const pipeline = opt("pipeline", null);
    const sqos = pipeline != null ? pipeline / r.avg_deal : opt("sqos", 56);
    held = sqos / r.sqo_of_held;
  }
  const booked   = held / r.held_of_booked;
  const positive = booked / r.book_of_positive;
  const replies  = positive / r.positive_of_replies;
  const delivered= replies / r.reply_rate;
  const contacts = delivered / r.deliverability;
  const sqos = held * r.sqo_of_held, won = sqos * r.win_of_sqo;
  console.log("=== BACKSOLVE ===");
  console.log("contacts/held meeting:", c2held ? (1 / c2held).toFixed(0) : "n/a");
  console.table({
    contacts_loaded:{total:fmt(contacts), per_week:fmt(contacts/weeks)},
    delivered:{total:fmt(delivered)}, replies:{total:fmt(replies)},
    positive_replies:{total:fmt(positive)}, meetings_booked:{total:fmt(booked)},
    meetings_held:{total:fmt(held)}, sqos:{total:fmt(sqos)},
    closed_won:{total:fmt(won)}, pipeline:{total:money(sqos*r.avg_deal)},
  });
} else if (mode === "forward") {
  const cw = opt("contacts-week", 400);
  const contacts = cw * weeks;
  const delivered = contacts * r.deliverability;
  const replies = delivered * r.reply_rate;
  const positive = replies * r.positive_of_replies;
  const booked = positive * r.book_of_positive;
  const held = booked * r.held_of_booked;
  const sqos = held * r.sqo_of_held;
  const won = sqos * r.win_of_sqo;
  console.log("=== FORWARD PROJECTION ===");
  console.table({
    contacts:{total:fmt(contacts)}, delivered:{total:fmt(delivered)},
    replies:{total:fmt(replies)}, positive_replies:{total:fmt(positive)},
    meetings_booked:{total:fmt(booked)}, meetings_held:{total:fmt(held)},
    sqos:{total:fmt(sqos)}, closed_won:{total:fmt(won)},
    pipeline:{total:money(sqos*r.avg_deal)},
  });
} else {
  console.log("modes: backsolve | forward (see header for flags)");
}
```

## A/B testing without lying to yourself
Cold-outbound reply rates are low, so you need *big* samples to detect small lifts. Rough intuition: to reliably tell a 2% reply rate from a 4% one you need on the order of ~1,000+ sends per variant; to split-hair a 2.0% vs 2.4% you need several thousand. **Never call a winner on 40 sends** - at a 2% base rate that's an expected 0.8 replies; the "winner" is noise. Rules: change exactly one variable (subject OR first line OR CTA, never all three), run both variants concurrently to the same ICP slice (channel/timing/list quality confound everything), and pre-commit the sample size. Hand sequence-structure tests to [[outreach-sequence-designer]].

## Deliverable

Produce a funnel model the team can rerun: the filled funnel template with every rate labeled MEASURED or ASSUMPTION, the backsolve from the quarter's target down to required contacts/week (with the feasibility check against per-rep capacity), the headline contacts-per-held-meeting number, and the `funnel.mjs` artifact configured with those rates so the model updates in one command when the real data lands.

## Quality bar

- Every conversion rate is labeled as measured (with the trailing window and sample size) or as a benchmark assumption.
- The backsolve chain is shown stage by stage, not just the final contacts number.
- The weekly activity requirement is checked against realistic rep capacity (~250-500 quality contacts/week/rep) and the gap, if any, is named.
- No goal or test is gated on open rates.
- Any A/B claim carries its per-variant sample size.

## Common failure modes
- **Low delivery (<90%) / bounces high.** Not a copy problem - it's infrastructure. Domain reputation, warmup, list hygiene. Go to [[cold-email-deliverability]]. Sending more into a deliverability hole just burns the domain faster.
- **Low reply rate (<1%) with good delivery.** Wrong people or wrong message. Re-check ICP/persona fit ([[icp-persona-builder]]); then the message itself ([[cold-email-craft]]). Don't add volume on top of a broken message.
- **Decent replies, low *positive* reply rate.** You're reaching humans but the offer/relevance misses - usually targeting drift or a generic value prop. [[icp-persona-builder]] + [[cold-email-craft]].
- **Positive replies that don't become booked meetings.** Slow/ambiguous follow-up or weak CTA. Tighten the booking step in [[outreach-sequence-designer]].
- **Meeting-held rate <70%.** No-shows. This is a scheduling/confirmation problem (reminders, calendar holds, shorter book-to-meeting gap), not top-of-funnel - adding contacts won't fix it.
- **Held but no SQOs.** Qualification problem: you're booking the wrong meetings. Tighten ICP and pre-call qualification, not volume.
- **Optimizing opens.** Stop. Post-MPP, opens are inflated and noisy; never gate a test or a goal on them. Use reply-based metrics.
- **Lagging-only review.** Closed-won lags weeks. Review *leading* indicators weekly (contacts sent, reply rate, positive reply rate, meetings booked); review *lagging* (SQO, win rate, pipeline $) monthly/quarterly. A sane cadence: weekly activity-and-reply standup, monthly conversion-rate recalibration, quarterly quota backsolve.
