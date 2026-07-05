---
name: apollo-prospecting
description: Use when executing your B2B prospecting strategy inside Apollo.io specifically — turning an ICP into stacked Apollo search filters, building and tiering Apollo Lists, finding and verifying emails with credit discipline, tracking buying signals via Apollo filters and saved-search alerts, and running multi-step Apollo Sequences. Trigger on "Apollo", "Apollo.io", "set up Apollo filters", "Apollo saved search", "Apollo list", "Apollo sequence", "find emails in Apollo", "Apollo credits", "export from Apollo to my CRM", "Apollo Chrome extension", "Apollo intent data", "how do I prospect in Apollo". This skill assumes the strategy is already done in the methodology skills and shows how to operationalize it in Apollo. Do NOT use for designing the ICP itself — use [[icp-persona-builder]]. Do NOT use for writing the actual email copy — use [[cold-email-craft]]. Do NOT use for fixing domain authentication / SPF / DKIM / DMARC — use [[cold-email-deliverability]]. Do NOT use for reading funnel metrics conceptually — use [[prospecting-metrics]].
---

# Prospect in Apollo.io

This is the tool-specific execution layer for the **B2B Prospecting Engine** pack. The methodology skills decide *what* to do; this skill shows you how to do it *in Apollo.io*. It assumes you have already built your ICP and personas in [[icp-persona-builder]], decided your list strategy in [[prospect-list-builder]], chosen your enrichment and verification rules in [[lead-enrichment]], picked the signals you care about in [[buying-signal-tracker]], structured your cadence in [[outreach-sequence-designer]], hardened your sending infrastructure in [[cold-email-deliverability]], and defined your funnel math in [[prospecting-metrics]].

If you have not done that work, stop and do it first. Apollo will happily let you blast a poorly-targeted list from your primary domain and torch both your deliverability and your data budget. The tool is a power amplifier, not a strategy.

Apollo changes its UI often, so this skill stays conceptual on *where* things live and concrete on *what to do and why*. When a specific menu or label is uncertain, confirm it in-product rather than trusting a click path from memory.

## When to use this skill

- You have an approved ICP and you need to translate it into Apollo People/Company Search filters.
- You are building or tiering lists in Apollo and need to suppress existing customers and open opportunities.
- You need to find and verify emails in Apollo without burning credits on garbage.
- You want Apollo to surface buying signals (job changes, hiring, funding, intent) and alert you.
- You are building an outbound cadence as an Apollo Sequence and wiring in LinkedIn/call task steps.

Do **not** use this skill to invent the strategy — that lives in the methodology siblings above.

## The workflow

### 1. Translate your ICP into Apollo filters ([[icp-persona-builder]] → People/Company Search)

Build the **company** layer first, then the **person** layer on top. In Apollo's People Search and Company Search you stack filters; each filter narrows the set, so add them deliberately and watch the result count. A useful sanity band: a persona-per-tier search that returns more than ~5,000 people is almost certainly under-filtered, and one returning under ~100 may be over-constrained for a sustained sequence.

- **Firmographic:** industry/keywords, employee headcount band, revenue (where available), HQ geography. Map these straight from your ICP's account definition.
- **Technographic:** "uses technology X" filters — powerful for ICPs defined by a tech stack (e.g. targets running a specific CRM, cloud, or e-commerce platform).
- **Persona (person layer):** title keywords, seniority, and department/function. Prefer **seniority + department** over raw title strings, because titles are inconsistent across companies; layer specific title keywords only to sharpen.
- **Exclusions:** exclude the industries, sizes, and titles your ICP explicitly rules out. Exclusions are as important as inclusions for keeping credits honest.

Save the result as a **Saved Search** so it is reproducible and can drive alerts. One saved search per persona-per-tier, not one mega-search.

### 2. Build and tier lists, then suppress ([[prospect-list-builder]] → Apollo Lists)

Promote saved-search results into **Lists** that mirror your tiers (e.g. `Tier A — VP Eng — fintech`, `Tier B — …`). Keep lists small and named by ICP slice so you can run different sequences and read metrics per slice.

Suppression is non-negotiable. Before anyone enters a sequence:

- Exclude contacts already in your CRM (current customers, open opportunities, recent conversations). Use Apollo's CRM integration/enrichment so existing records are flagged, or export and dedupe against a suppression list.
- Exclude do-not-contact and previously-bounced addresses.

A prospect who is already a customer or an open opp landing in a cold sequence is an own-goal that the rep and the data both pay for.

### 3. Enrich and verify with credit discipline ([[lead-enrichment]] → email finder + verification)

Apollo spends **credits** to reveal/verify contact data and to export. Treat credits as cash:

- Reveal/verify emails only for contacts that survived filtering and suppression. Never reveal an entire broad search.
- Respect the **email verification state**. Send to verified/valid addresses; hold or route catch-all/unknown/guessed states differently (lower volume, or skip). Sending to unverified addresses is how you manufacture bounces — and a bounce rate above roughly 2% is the red line where mailbox providers start throttling you; past 5% you are in serious reputation damage.
- Pull "Net New" leads (not already in your data) deliberately, in batches sized to what you can actually work.
- Decide your CRM sync path up front: CSV export for one-offs, or the **Apollo API / native CRM integration** for repeatable syncing. Keep the field mapping consistent so downstream reporting in [[prospecting-metrics]] holds together.

### 4. Track buying signals and wire alerts ([[buying-signal-tracker]] → filters + saved-search alerts)

Encode the signals you chose into Apollo where the data exists:

- **Job changes** — your champion moved to a new account; that is a warm opening.
- **Hiring** — open roles in the relevant function signal budget and pain.
- **Funding / growth** — new capital often unlocks new initiatives.
- **Intent topics** — where available, filter or prioritize by accounts researching your category.

Turn the highest-value saved searches into **alerts** so new matches surface automatically instead of you re-running searches. Route signal hits into a dedicated, higher-priority sequence.

### 5. Build the cadence as an Apollo Sequence ([[outreach-sequence-designer]] → Sequences)

Build the cadence you designed as an Apollo **Sequence** — multi-step, mixing **automated email** steps with **manual tasks** (LinkedIn touch, call). Do not let Apollo auto-send everything; manual task steps are where reps add the human judgment that earns replies.

- Write the email copy in [[cold-email-craft]] — this skill only places it into steps.
- Set sane delays between steps and cap daily send volume per mailbox (see step 6).
- A/B test at the **step** level (subject or first line), and read results against [[prospecting-metrics]] — not vanity opens. For calibration: a healthy cold sequence typically lands a 1–5% reply rate; if you are under 1% after ~200 sends, stop and fix targeting or copy before spending more list.

### 6. Protect deliverability before you press start ([[cold-email-deliverability]])

Apollo sends through **mailboxes you connect** — it does not fix bad DNS. Before any sequence goes live:

- Connect **dedicated sending mailboxes on a separate sending domain**, not your primary corporate domain. Cold-sequence reputation damage should never touch the domain your company runs its real email on.
- Make sure those domains/mailboxes are authenticated (SPF/DKIM/DMARC) and warmed — do this in [[cold-email-deliverability]], not here. Budget 2–4 weeks of warmup for a fresh mailbox before it carries cold volume.
- Respect conservative per-mailbox daily limits — roughly 20–50 cold sends per mailbox per day is the practitioner range; scale by adding mailboxes, not by pushing one past it — and let Apollo throttle.

## Apollo filter recipe (ICP → stacked filters)

```
ICP: Mid-market fintech, Series B+, running a modern data warehouse,
     buyer = data/analytics leadership.

COMPANY LAYER (Company Search)
  Industry/keywords ........ financial services, fintech, payments
  Employee headcount ....... 201–1000
  Geography (HQ) ........... United States, Canada
  Technographic ............ uses Snowflake OR BigQuery
  Exclude .................. industry = staffing, education
                            headcount < 50
  → Save as: "Co — MM fintech — modern DWH"

PERSON LAYER (People Search, on top)
  Seniority ................ VP, Head, Director
  Department/function ...... Data / Analytics / Engineering
  Title keywords ........... "data", "analytics", "platform"
  Exclude titles ........... intern, contractor, "sales"
  → Save as: "P — Tier A — Data leaders — MM fintech"

SIGNAL OVERLAY (separate saved search + alert)
  Same as above, plus: hiring for data roles  OR  funding in last 90d
  → Save as: "P — Tier A — Data leaders — SIGNAL" (alert ON)
```

## Apollo Sequence outline (cadence skeleton)

```
Sequence: "Tier A — Data leaders — MM fintech"
Mailboxes: 2 dedicated boxes on outbound.example-go.com (warmed, authed)
Daily cap: conservative per mailbox (~20–50 cold sends); split across both

Day 1  · Auto email   · Step A1 — problem-led opener (copy from cold-email-craft)
Day 2  · Manual task  · LinkedIn — view + connect, no pitch
Day 4  · Auto email   · Step A2 — reply-thread bump, new angle/proof
Day 6  · Manual task  · Call — reference the trigger/signal
Day 9  · Auto email   · Step A3 — short, specific CTA
Day 13 · Manual task  · LinkedIn — soft value share
Day 16 · Auto email   · Step A4 — breakup / permission-to-close

A/B: test Step A1 subject line only; hold everything else constant.
Read: reply rate + positive-reply rate per step (NOT open rate).
```

## Worked example (end to end, one ICP)

**ICP (from [[icp-persona-builder]]):** data/analytics leaders at US/Canada mid-market fintechs (201–1000) running Snowflake or BigQuery.

1. **Filters.** In Company Search I stack the firmographic + technographic filters above and exclude staffing/education and sub-50 headcount. ~600 accounts. I save it. On the person layer I add seniority VP/Head/Director + Data/Analytics/Engineering function, exclude sales/contractor titles. ~900 people. Saved as the Tier A person search.
2. **List + suppress.** I promote the results into a `Tier A — Data leaders — MM fintech` list. Using the CRM integration, I drop everyone already a customer or in an open opp, plus prior bounces and do-not-contact. ~720 remain.
3. **Enrich + verify.** I reveal/verify emails only for that suppressed list. I keep verified/valid, route catch-all to a lower-volume track, and skip unknown/guessed. ~540 send-ready. I export the verified set to the CRM via the API with consistent field mapping.
4. **Signals.** I clone the search into a SIGNAL variant (hiring for data roles OR funding in last 90d), turn the alert on, and these hits feed a higher-priority sequence.
5. **Sequence.** I build the 7-step cadence above across two warmed mailboxes on a dedicated outbound domain, paste copy from [[cold-email-craft]], set conservative caps, and A/B the opener subject.
6. **Read.** A week in, I judge by reply and positive-reply rate per step in [[prospecting-metrics]] — and ignore open rate entirely (see below).

## Deliverable

Produce a working Apollo setup plus a one-page run sheet documenting it: the saved searches (one per persona-per-tier, plus signal variants with alerts on), the tiered and suppressed Lists with their counts at each stage (raw → suppressed → verified send-ready), the verification routing rule (verified send / catch-all low-volume / unknown skip), the CRM sync path and field mapping, and the live Sequence outline with mailboxes, daily caps, step schedule, and the single A/B variable. The run sheet is what lets a second rep — or you in a month — reproduce and audit the setup.

## Quality bar

- Every saved search maps to exactly one persona-per-tier from the ICP; none is an untierable mega-search.
- No contact enters a sequence without passing CRM suppression and holding a verified email state.
- All sending mailboxes are on a dedicated outbound domain, authenticated and warmed, with daily caps inside the 20–50 range.
- The sequence mixes auto emails with manual LinkedIn/call tasks; success is read in replies and positive replies, never opens.

## Common failure modes

- **Blasting sequences from your primary domain.** The fastest way to poison the email your whole company depends on. Always send from dedicated, authenticated, warmed domains/mailboxes. Apollo connects the mailbox; it does not fix your DNS — that is [[cold-email-deliverability]].
- **Ignoring verification states.** Treating catch-all/unknown/guessed like verified manufactures bounces, which wrecks reputation and corrupts your metrics. Send to verified; route or skip the rest. Watch the 2% bounce red line.
- **Over-broad filters that burn credits.** Revealing a 50k-row search "to see who's there" spends your data budget on contacts you will never work. Filter and suppress *before* you reveal/export; reveal only what you can actually sequence.
- **Not suppressing customers and open opps.** Cold-emailing a current customer or an active deal embarrasses the rep and pollutes reporting. Suppress against the CRM every time, before sequencing.
- **Relying on open rate.** Apple Mail Privacy Protection and image proxies inflate opens into noise. Optimize for replies, positive replies, and meetings — the funnel math in [[prospecting-metrics]] — not opens.
- **One mega saved search.** A single giant search you can't tier or alert on becomes unmaintainable. Keep one saved search per persona-per-tier, plus signal variants.
- **Letting Apollo auto-send every step.** Removing the manual LinkedIn/call tasks turns a thoughtful cadence into spam. Keep human task steps in the sequence.
