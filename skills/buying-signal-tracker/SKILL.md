---
name: buying-signal-tracker
description: Use when you need to decide WHO IN YOUR ICP TO HIT NOW based on timing and trigger events rather than static fit - "who should I reach out to this week", "what's a good reason to reach out", "track buying signals", "find trigger events", "champion changed jobs", "they just raised a round", "new VP just started", "they're hiring for X", "they installed a competitor", "intent data spiked", "why now opener", "warm up our outbound", "score and prioritize accounts by signal". Workflow: enumerate the six signal categories (company triggers, people/job-change triggers, hiring reqs, technographics, third-party intent, first-party engagement), map each signal to a "why now" opening line and the play/sequence it should fire, then rank accounts with a fit×signal scoring matrix so you work the hottest first. Do NOT use for defining who fits in the abstract (firmographics, personas) - use [[icp-persona-builder]]; do NOT use for the tool mechanics of pulling/saving signals - use [[apollo-prospecting]].
---

# Track Buying Signals & Trigger Events

Fit tells you WHO could buy; signals tell you WHO TO CALL TODAY. The single biggest lever in outbound is not a better list or a cleverer line - it is timing. A perfectly-fit account contacted in a dead quarter ignores you; the same account contacted the week a new VP inherits a broken process replies. ICP is static and slow-moving ([[icp-persona-builder]]); signals are dynamic and perishable. This skill is about catching the perishable window.

The common trap is treating all signals as equal and "spraying" the whole ICP whenever any signal fires. Signals vary wildly in strength: a champion who moves to a new in-ICP company is near-deterministic (someone who already bought you, now with budget and a mandate). A third-party intent surge is directional noise at best. Weight them, decay them by recency, multiply by fit - then work the top of the ranked list, not the whole thing.

## When to use this skill
- You have an ICP and a list but no sense of which accounts are *ripe* right now.
- A specific event just happened (funding, exec hire, layoff, job change) and you need the right "why now" angle and play.
- Your outbound is high-volume, low-reply and you suspect it's a timing problem, not a copy problem.
- You want to build a repeatable account-prioritization score instead of working alphabetically.

Do NOT use this to define firmographics or personas - that's [[icp-persona-builder]]. Do NOT use it for the click-path of saving signals in a tool - that's [[apollo-prospecting]].

## The workflow

1. **Enumerate the six signal categories and their strength tier.** (1) *Company triggers* - funding rounds, exec/leadership hires, M&A, new office/expansion, layoffs, earnings/10-K commentary [medium-strong]. (2) *People triggers* - **job change of a known champion [strongest]**, new-in-role (first 90 days = mandate to change things) [strong], promotions [medium]. (3) *Hiring signals* - open reqs reveal initiatives, tooling, and team growth [medium]. (4) *Technographic* - installing or removing a competitor/complementary tool [medium-strong]. (5) *Third-party intent* - topic surges, account-level, noisy [weak, directional]. (6) *First-party engagement* - site visits, content downloads, repeated opens [strong, because it's *their* action].

2. **For each live signal, write the "why now" opener.** The signal must be visible in the first line and must be about *them*, not you. "Saw you just brought on a VP of RevOps - usually means the comp/territory model is getting rebuilt" beats "I wanted to reach out about our platform." If you can't write a non-creepy "why now," the signal is too weak to act on.

3. **Map signal → play → sequence.** Strong personal signals (champion move, first-party engagement) earn a high-touch, low-volume play and a fast human sequence ([[outreach-sequence-designer]]). Weaker account-level signals (intent surge) earn a lighter, batch sequence with a softer ask ([[cold-email-craft]]). Never fire a 9-touch aggressive cadence off a single noisy intent point.

4. **Score accounts: fit × Σ(weighted, recency-decayed signals).** Fit gates the whole thing (no fit = don't bother, however hot the signal). Decay matters: a funding round from 9 months ago is stale; a champion who moved last week is white-hot. See the artifact below.

5. **Work the ranked list top-down, in batches.** Take the top N each week, not the whole ICP. Re-run the score on a cadence so freshly-fired signals float to the top and decayed ones sink.

6. **Log outcomes per signal type to learn your real weights.** Track reply/meeting rate by signal category over time ([[prospecting-metrics]]). Your weights are hypotheses - let conversions correct them. Most teams discover champion-moves and first-party engagement crush everything and intent data underperforms its hype.

7. **Set freshness SLAs.** A signal acted on 3 weeks late is barely a signal. Champion job changes and inbound engagement should be touched within 48 hours; company triggers within a week.

## Signal → play mapping template

```
SIGNAL:        <what happened, e.g. "champion job change">
CATEGORY:      company | people | hiring | technographic | intent | first-party
STRENGTH:      strong | medium | weak
SOURCE:        <where detected - see [[apollo-prospecting]] / [[lead-enrichment]]>
FRESHNESS SLA: <act within X hrs/days>
WHY-NOW LINE:  "<opener that leads with THEIR event>"
PLAY:          high-touch 1:1 | warm batch | light nurture
SEQUENCE:      <-> [[outreach-sequence-designer]] template name
DISQUALIFY IF: <fit fails / wrong persona / already in pipeline>
```

## Worked example: champion job change

```
SIGNAL:        Dana Reyes (bought us at Acme, was a power user) is now
               VP Ops at Northwind (in-ICP: 600-person logistics co)
CATEGORY:      people  STRENGTH: strong (strongest single signal)
SOURCE:        job-change alert on saved contacts ([[apollo-prospecting]])
FRESHNESS SLA: 48 hours - new-in-role goodwill is highest in week one
WHY-NOW LINE:  "Dana - congrats on Northwind. Last time we worked together
                you cut Acme's onboarding time in half. Happy to hand you
                a setup so you don't rebuild from scratch in your first 90."
PLAY:          high-touch 1:1, personal, no marketing scaffolding
SEQUENCE:      "warm-champion-reactivation" (3 touches, human, no automation
                feel) -> [[outreach-sequence-designer]]
DISQUALIFY IF: Northwind already a customer, or Dana's new role has no
                authority/budget over our category
```

Why it ranks #1: fit is high (in-ICP account), the signal is the strongest tier, it's days old (no decay), and the relationship is pre-built. This single account outranks fifty cold-but-fitting accounts. That's the whole point of the score.

## Scoring artifact

Save as `score-accounts.mjs`, run `node score-accounts.mjs`. Edit the inline data or pipe your own JSON in.

```js
// node score-accounts.mjs  - ranks accounts by fit × recency-decayed signals
const WEIGHTS = { // signal strength by category
  champion_move: 100, first_party: 70, new_in_role: 55, technographic: 45,
  funding: 40, exec_hire: 40, hiring_req: 30, expansion: 30, layoff: 25,
  intent: 15,
};
const HALF_LIFE_DAYS = 45; // a signal loses half its weight every 45 days
const decay = (ageDays) => Math.pow(0.5, ageDays / HALF_LIFE_DAYS);

function score(account) {
  if (!account.fit || account.fit <= 0) return { ...account, signalScore: 0, total: 0 };
  const signalScore = (account.signals || []).reduce((sum, s) => {
    const w = WEIGHTS[s.type] ?? 10;
    return sum + w * decay(s.ageDays ?? 0);
  }, 0);
  // fit (0..1) gates everything; round for readability
  return { ...account, signalScore: Math.round(signalScore), total: Math.round(account.fit * signalScore) };
}

const accounts = [
  { name: "Northwind", fit: 0.95, signals: [{ type: "champion_move", ageDays: 2 }] },
  { name: "Globex",    fit: 0.90, signals: [{ type: "funding", ageDays: 10 }, { type: "hiring_req", ageDays: 5 }] },
  { name: "Initech",   fit: 0.80, signals: [{ type: "intent", ageDays: 1 }, { type: "intent", ageDays: 3 }] },
  { name: "Soylent",   fit: 0.85, signals: [{ type: "funding", ageDays: 200 }] }, // stale → decayed away
  { name: "BadFit",    fit: 0.00, signals: [{ type: "champion_move", ageDays: 1 }] }, // hot signal, no fit → 0
];

accounts.map(score)
  .sort((a, b) => b.total - a.total)
  .forEach((a, i) => console.log(
    `${String(i + 1).padStart(2)}. ${a.name.padEnd(10)} total=${String(a.total).padStart(4)}  (fit ${a.fit}, signal ${a.signalScore})`
  ));
```

Expected ordering: Northwind (fresh champion move) tops it, Globex next (two medium signals), Initech mid (noisy intent), Soylent low (decayed funding), BadFit zero (gated by fit). Tune `WEIGHTS` and `HALF_LIFE_DAYS` from your own conversion data per step 6.

## Deliverable

Produce a signal-driven prioritization pack containing: (1) the signal inventory - every signal category you can actually detect, each with its strength tier, source, and freshness SLA; (2) a filled signal → play mapping (template above) for each live signal, including the written "why now" opener; (3) the ranked account list from the scoring artifact, with fit, signal score, and total shown per account; and (4) the week's working batch - the top N accounts with their assigned play and sequence, ready to hand to [[outreach-sequence-designer]].

## Quality bar

- Every account on the working batch has at least one signal fresher than its SLA - no stale triggers dressed up as news.
- Every "why now" line leads with *their* event and would survive being read aloud to the prospect.
- Fit gates the score: no zero-fit account appears in the batch, however hot its signal.
- Signal weights are either backed by your own conversion data or explicitly labeled as starting hypotheses.
- The batch is sized to what reps can actually touch this week, not to how many accounts scored above zero.

## Common failure modes
- **Spraying on any signal.** Treating a weak intent ping like a champion move. Weight and gate by fit, then work top-down.
- **Stale signals.** Acting on a 6-month-old funding round as if it's news. Decay everything; set freshness SLAs.
- **"Why now" that's about you.** "I wanted to introduce our platform" wastes the trigger. Lead with their event, every time.
- **Trusting intent data deterministically.** It's account-level and noisy - directional, not a buy signal. Pair it with a second signal before going high-touch.
- **No feedback loop.** Guessing weights forever instead of logging reply/meeting rate by signal type ([[prospecting-metrics]]) and correcting.
- **Ignoring first-party signals** in favor of exotic third-party data - your own visitors and openers are often the hottest and cheapest signal you have.
- **Confusing this with fit.** Re-running ICP work here, or skipping fit entirely and chasing hot signals at non-ICP accounts. Fit gates; signal ranks.
