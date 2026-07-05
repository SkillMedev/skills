---
name: prospect-list-builder
description: Use when you have an ICP and need to turn it into an actual list of accounts and contacts to put into a sequence. Trigger phrases include "build me a prospect list", "who should we target", "turn this ICP into a list", "find accounts that match", "build a target account list", "how many contacts per account", "tier these accounts", "pull a list for the SDRs", "clean this list before we send", "right-size the list for sending", "multi-thread the buying committee". Workflow: translate ICP criteria into concrete firmographic/technographic/title filters, decide account-first vs contact-first, tier accounts (T1 manual / T2-3 scaled), set contacts-per-account, suppress against CRM, QA, and size to deliverability limits. Do NOT use for filling in or verifying missing data on a list you already have — use [[lead-enrichment]] instead. Do NOT use for prioritizing the list by timing/intent — use [[buying-signal-tracker]] instead. The button-clicking inside a specific tool lives in [[apollo-prospecting]].
---

# Build a Targeted Prospect List

A prospect list is where strategy meets reality. Your [[icp-persona-builder]] output is a hypothesis about who buys; the list is the first place that hypothesis touches the real world and either holds up or falls apart. The core insight: a list is not "everyone who matches the filters." It is a *tiered, deduplicated, suppression-checked, right-sized* set of accounts and contacts you can actually work this quarter. The most common trap is volume worship — pulling 12,000 "matches" because the tool says they exist, dumping them into a sequence, and burning your domain reputation and your ICP in the same week. A great list is usually smaller than the one you first want to build.

The second trap is single-threading: pulling one contact per account because it's easy. B2B deals are won across a buying committee. If you only have the champion, one job change or one "not a priority right now" kills the account. Build for the committee from the start.

## When to use this skill

- You have a defined ICP and persona(s) and need the actual accounts/contacts to work.
- You're standing up a new sequence or campaign and need clean fuel for it.
- You're handing SDRs a target account list and need it tiered and de-duped.
- You're re-pulling a segment and need to suppress current customers and open opps.
- You need to size a list to what your sending infrastructure can actually handle.

If your data is already gathered but dirty or incomplete, that's [[lead-enrichment]]. If you need to rank an existing list by who's in-market *right now*, that's [[buying-signal-tracker]].

## The workflow

1. **Translate ICP criteria into hard filters — in three buckets.** Don't free-associate. Map every ICP attribute to a concrete, filterable field across: (a) **firmographic** — industry/SIC/NAICS, headcount band, revenue band, geo, funding stage; (b) **technographic** — installed tools that imply fit or pain (e.g., "uses Segment + Snowflake" = data maturity); (c) **person** — title, seniority, department, and *exclusions* (drop "intern", "student", "retired", "consultant" unless that's the play). Write filters down before you touch a tool so you can audit why each account is on the list.

2. **Decide account-first vs contact-first — and default to account-first.** Account-first (build the target account universe, *then* find people inside each) is correct for considered B2B sales because it lets you multi-thread and tier deliberately. Contact-first (search people by title across all companies) is faster but produces orphaned contacts and accidental account overlap. Use contact-first only for low-ACV, high-velocity motions where the account doesn't matter much.

3. **Tier the accounts before pulling a single contact.** Not all matches deserve equal effort. **Tier 1** = best-fit, highest-value, *manual* research and personalization (cap it at what your reps can actually research — often 25-50/rep). **Tier 2** = solid fit, semi-scaled with light personalization. **Tier 3** = fits the filters, fully scaled / automated. The tier determines how many contacts you pull and how much you invest per account.

4. **Set contacts-per-account by tier to multi-thread the committee.** Tier 1: 3-5 contacts spanning the committee (economic buyer, champion, 1-2 influencers/users). Tier 2: 2-3. Tier 3: 1-2. Never pull the whole org chart — that's noise and a spam signal. Name the *roles* you want per account, not just a count, so you cover decision-maker + influencer rather than three peers.

5. **Suppress against the CRM — this is non-negotiable.** Before the list is "done," diff it against: existing customers, open opportunities (never let an SDR cold-prospect an account your AE is closing), leads worked or contacted within the past 90 days, do-not-contact / unsubscribed, and competitors/partners. A list that re-prospects an open opp is worse than no list — it actively damages deals and trust.

6. **QA the list before it touches a sequence.** Spot-check 20-30 rows by hand: do the people actually match the persona? Are titles real or junk ("Founder" at a 5,000-person co is suspicious)? Is the company actually in-ICP or did a filter overmatch? Check email presence/quality and role-based addresses. Run the cleaning artifact below. A 5% bad-data rate at 5,000 contacts is 250 bounces — enough to wreck deliverability.

7. **Right-size to deliverability, not to availability.** Work backward from sending capacity, not forward from match count. Per the limits in [[cold-email-deliverability]], a warmed inbox sends a limited number of cold emails/day. List size must fit (mailboxes × daily cap × sequence days), with buffer for bounces. If the addressable universe is bigger than you can safely send, that's a *good* problem — tier harder and work T1 first. Then hand the sized, clean list off to [[outreach-sequence-designer]].

## List build spec template

```
LIST BUILD SPEC — <segment / campaign name>
Source ICP/persona: <link to icp-persona-builder output>
Date / owner: <date> / <name>

FILTERS
  Firmographic:
    Industry:         <e.g., B2B SaaS, NAICS 5112>
    Headcount:        <e.g., 50–500>
    Revenue:          <e.g., $5M–$50M ARR>
    Geo:              <e.g., US + Canada, English-speaking>
    Funding/stage:    <e.g., Series A–C>
  Technographic:
    Must use:         <e.g., Salesforce OR HubSpot>
    Signals pain:     <e.g., no analytics tool detected>
  Person:
    Titles:           <e.g., VP/Dir/Head of RevOps, Sales Ops>
    Seniority:        <Manager+ / Director+ / VP+>
    Department:       <e.g., Sales, Revenue Operations>
    EXCLUDE:          <intern, student, retired, consultant, freelance>

BUILD METHOD:   [ ] account-first   [ ] contact-first   (default: account-first)

TIERS & CONTACTS-PER-ACCOUNT
  Tier 1 (manual):   <N accounts>  → 3–5 contacts: <econ buyer, champion, +influencer>
  Tier 2 (semi):     <N accounts>  → 2–3 contacts: <champion, +1>
  Tier 3 (scaled):   <N accounts>  → 1–2 contacts: <champion>

SUPPRESSION (diff against CRM before finalizing)
  [ ] current customers      [ ] open opportunities
  [ ] worked < 90 days       [ ] unsubscribed / DNC
  [ ] competitors/partners   [ ] duplicate accounts/contacts

QA GATE
  [ ] 20–30 rows hand-checked vs persona
  [ ] role-based + invalid emails removed (run clean-list.js)
  [ ] every account maps to a written filter reason

TARGET COUNT
  Addressable (raw matches):   <N>
  Sized to deliverability:     <N>   (mailboxes × daily cap × days, w/ bounce buffer)
  This pull (work first):      <Tier 1 + Tier 2>
```

## Worked example

**ICP:** Series A–C B2B SaaS, 50–500 employees, US/CA, uses Salesforce/HubSpot, no dedicated analytics tool. **Persona:** Director+ of RevOps/Sales Ops.

1. **Filters set:** NAICS 5112; headcount 50–500; US+CA; tech filter `(Salesforce OR HubSpot) AND NOT (Looker OR Tableau OR Mode)`; titles `Director/VP/Head of (Revenue Operations|Sales Operations|RevOps)`; exclude consultants/fractional.
2. **Account-first.** Raw match: **1,400 accounts**. Too many to work well.
3. **Tiering:** T1 = 60 accounts (best-fit: 200–500 headcount, Series B/C, recent ops hire). T2 = 240. T3 = the rest, parked.
4. **Contacts:** T1 → 4 each (VP RevOps = econ buyer, RevOps Mgr = champion, VP Sales + CFO = influencers) = 240 contacts. T2 → 2 each = 480. **Pull this quarter: 720 contacts** (T3 parked).
5. **Suppression:** diff vs CRM drops 38 accounts (12 customers, 9 open opps, 17 worked <90d). Net ~700 contacts.
6. **QA:** hand-check 25 rows → 2 mis-titled ("RevOps Consultant" slipped past), 1 acquired company. Run `clean-list.js` → drops 14 role-based addresses (`ops@`, `sales@`), flags 6 domain+name dupes. Clean list: **~680**.
7. **Deliverability sizing:** 3 mailboxes × 40 cold/day × 5 send-days = 600 capacity/cycle. 680 fits in ~1.2 cycles → start T1 immediately, feed T2 as capacity frees. Hand to [[outreach-sequence-designer]].

## Cleaning artifact (Node)

Run on a CSV with `email,first_name,last_name,company,domain` headers: `node clean-list.js leads.csv > clean.csv`. Normalizes emails, drops role-based addresses, flags suspected dupes by domain+name.

```js
#!/usr/bin/env node
// clean-list.js — normalize, drop role-based, flag dupes. Zero deps.
const fs = require('fs');
const ROLE = new Set(['info','sales','support','admin','contact','hello','team',
  'ops','marketing','billing','help','office','careers','jobs','hr','noreply',
  'no-reply','postmaster','webmaster','enquiries','inquiries','accounts']);

const file = process.argv[2];
if (!file) { console.error('usage: node clean-list.js <leads.csv>'); process.exit(1); }

// minimal CSV parse (handles quoted fields, commas, escaped quotes)
function parse(text) {
  const rows = []; let row = [], cur = '', q = false;
  for (let i = 0; i < text.length; i++) {
    const c = text[i];
    if (q) {
      if (c === '"' && text[i+1] === '"') { cur += '"'; i++; }
      else if (c === '"') q = false; else cur += c;
    } else if (c === '"') q = true;
    else if (c === ',') { row.push(cur); cur = ''; }
    else if (c === '\n') { row.push(cur); rows.push(row); row = []; cur = ''; }
    else if (c !== '\r') cur += c;
  }
  if (cur || row.length) { row.push(cur); rows.push(row); }
  return rows.filter(r => r.some(x => x.trim() !== ''));
}

const rows = parse(fs.readFileSync(file, 'utf8'));
const header = rows.shift().map(h => h.trim().toLowerCase());
const col = name => header.indexOf(name);
const iEmail = col('email'), iFirst = col('first_name'),
      iLast = col('last_name'), iDomain = col('domain');

const seen = new Map();           // domain|first|last -> first row #
let kept = 0, droppedRole = 0, droppedInvalid = 0, flaggedDupe = 0;
const out = [[...header, 'qa_flag']];

rows.forEach((r, idx) => {
  let email = (r[iEmail] || '').trim().toLowerCase();
  email = email.replace(/^mailto:/, '').replace(/\s+/g, '');
  const valid = /^[^@\s]+@[^@\s]+\.[^@\s]+$/.test(email);
  if (!valid) { droppedInvalid++; return; }
  const local = email.split('@')[0].replace(/\+.*/, ''); // strip +tag
  if (ROLE.has(local)) { droppedRole++; return; }

  const domain = ((r[iDomain] || email.split('@')[1]) || '').toLowerCase();
  const key = [domain, (r[iFirst]||'').trim().toLowerCase(),
                       (r[iLast]||'').trim().toLowerCase()].join('|');
  let flag = '';
  if (seen.has(key)) { flag = `dupe_of_row_${seen.get(key)}`; flaggedDupe++; }
  else seen.set(key, idx + 2);   // +2 for header + 1-index

  r[iEmail] = email;
  out.push([...r, flag]);
  kept++;
});

const esc = v => /[",\n]/.test(v) ? `"${String(v).replace(/"/g,'""')}"` : v;
process.stdout.write(out.map(r => r.map(esc).join(',')).join('\n') + '\n');
console.error(`kept=${kept} dropped_invalid=${droppedInvalid} ` +
  `dropped_role=${droppedRole} flagged_dupe=${flaggedDupe}`);
```

## Deliverable

Produce a filled list build spec (template above) plus the list itself: a tiered, CRM-suppressed, QA-passed CSV of accounts and contacts — each row carrying account tier, contact role on the buying committee, and the written filter reason it matched — sized to the sending infrastructure's capacity, with the raw-match, post-suppression, and final counts recorded so the shrinkage is auditable. Hand it to [[outreach-sequence-designer]] with the Tier 1 batch flagged to work first.

## Quality bar

- Every account maps to a written filter reason — nothing on the list "just seemed right."
- Suppression against customers, open opps, contacts worked in the past 90 days, and DNC is done and the counts removed are recorded.
- 20-30 rows are hand-checked against the persona and the miss rate is under ~5%.
- Tier 1 and Tier 2 accounts have named committee roles, not just a contact count.
- Final list size fits mailboxes × daily cap × send-days with a bounce buffer.

## Common failure modes

- **Volume worship.** Pulling everything the filter returns and sequencing it. Tier and size first; an unworkable list is a vanity metric. See [[prospecting-metrics]] for what to actually measure.
- **Single-threading.** One contact per account. The deal dies on a job change. Pull the committee on T1/T2.
- **No CRM suppression.** Cold-prospecting open opps and customers. This actively destroys deals and trust — make it a hard gate, not a nice-to-have.
- **Title overmatch.** "Head of" / "Founder" / consultant titles that look senior but aren't your buyer, or "Director" at a 12-person startup who's actually a rep. Hand-QA catches these; filters won't.
- **Ignoring deliverability ceilings.** Building a 10k list for an infrastructure that can safely send 600/cycle. The list outruns the mailboxes and you torch your domain. Size to [[cold-email-deliverability]] limits.
- **Stale technographic data.** Tech-install signals can be months old. Treat them as a tiebreaker, not gospel — verify on T1 during manual research.
- **Treating the list as done at pull time.** A list decays ~2-3%/month (job changes, departures). Re-suppress and re-QA before every re-use; don't resurrect a six-month-old pull.
