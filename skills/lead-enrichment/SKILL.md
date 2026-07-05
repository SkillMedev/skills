---
name: Lead Enrichment
description: Turns a raw or partial prospect list into a send-safe dataset by filling missing fields (work email, direct or mobile phone, title, firmographics, technographics) through a per-field provider waterfall, verifying every email, and stamping freshness. Use when someone says "enrich this list", "find the email for these people", "verify these emails before we send", "what do I do with catch-all domains", "this list is a year old, is it still good", "dedupe and clean this CSV", or "why is my bounce rate spiking". Do NOT use for selecting WHO to target and building the list itself - use prospect-list-builder. Do NOT use for tool-specific credit mechanics and filter syntax - use apollo-prospecting. Pair with cold-email-deliverability for the inbox-protection side of verification.
---

# Lead Enrichment

Enrichment is two jobs that people collapse into one and regret it: **filling** missing fields (a logistics problem - which provider has this contact, at what cost) and **verifying** the ones that touch deliverability (a risk problem - which addresses will bounce or hit a spam trap and torch the sending domain). A list is not "enriched" because the email column is full; it is enriched when every address intended for sending has been independently verified and stamped with a state, and every record carries a freshness date. The costly mistake this skill prevents: treating provider-supplied emails as send-ready. Vendors return pattern-generated guesses (first.last@domain) that were never SMTP-checked - send to those blind and expect a 12-20% bounce rate, which is how domains get throttled or blocklisted.

## Operating procedure

Follow in order: field definition gates the waterfall, the waterfall gates verification, and verification gates everything downstream. Skipping ahead means paying for fields you will not use or sending to addresses you never checked.

### Step 1: gather inputs

Collect before running anything. Label any guessed value as a guess.

- The list: row count, columns present, and source (CRM export, purchase, scrape, conference badge scan). Source predicts quality.
- The downstream play: email sequence, calls, LinkedIn, or multi-channel. This decides which fields are required.
- Domain reputation posture: warming a new domain (default: strict thresholds) or established and warmed (looser).
- Providers available and their per-credit cost for each field type.
- Cost ceiling: max credits, estimated cost per verified record, and a stop-if-over figure.

### Step 2: define the required field set

List only the fields the play actually needs - do not enrich what will not be used; every field has a credit cost. Typical: work_email (required, verified), company_domain (required), title, seniority, company_name (standardized), employee_count, industry, linkedin_url; optional multi-channel: direct_phone, mobile_phone; optional technographics: tech_stack (only if the pitch references it, e.g. "uses Salesforce" for an integration play). For each field decide: required-to-send, nice-to-have, or skip.

### Step 3: build the waterfall - order providers by hit-rate-per-dollar per field

Different providers win different fields: one is strong on mobile, another on EU work emails, another cheap on firmographics. Order them per field, not globally. Rule: query provider 1; if it returns a verified value, stop and bank it; fall through to provider 2 only on a miss or unverified result. Never fan out in parallel - that pays N times for one answer. Tool-specific credit and refund mechanics live in apollo-prospecting.

### Step 4: verify every email and record its state

Run each address through verification (provider-native or a dedicated verifier) and store one of four states: **valid** (SMTP-confirmed mailbox), **catch-all** (domain accepts everything - unverifiable at mailbox level), **risky** (role address like info@ or sales@, full inbox, or low-confidence), **invalid** (NXDOMAIN, no MX, rejected mailbox). Never silently overwrite the raw provider guess - keep both the value and its state, plus provenance (which provider, what date).

### Step 5: apply an explicit send threshold and route by state

Decide before sending: valid → send; invalid → drop, never send; risky → drop, or send only from a separate warmed IP at low volume. Catch-all is the judgment call - valid cannot be distinguished from invalid, so never blanket-send. Either (a) skip when domain reputation is the priority, (b) send only to high-value catch-all records corroborated by a second source or signal, or (c) route them to a low-risk channel (LinkedIn, phone). Warming posture = strict; established = looser. See cold-email-deliverability.

### Step 6: backfill firmographics and technographics, then normalize

Fill employee_count, industry, revenue_band, HQ region, and tech stack where the play uses them. Then run hygiene: normalize titles to a controlled vocabulary (VP Eng, V.P. of Engineering, Head of Engineering → one seniority+function pair), standardize company names (strip Inc, LLC, leading "the"), canonicalize domains (apex, not www).

### Step 7: dedupe and merge on a stable key

Key on company_domain + normalized_email (or LinkedIn URL when present), never on display name - "Bob Smith" and "Robert Smith" at the same domain are one person. On merge, keep the most-recently-verified value per field and preserve provenance so a junk-feeding provider can be audited out.

### Step 8: stamp freshness and set a re-verify TTL

B2B contact data decays roughly **30% per year** - people change jobs, mailboxes die. Write last_verified_at on every record. Before re-sequencing anything older than **90 days**, re-run verification and re-check the title (a stale title often means they changed companies). Freshness is a first-class field, not forgotten metadata.

## Enrichment plan template

```
ENRICHMENT PLAN: [FILL: list name] ([FILL: rows] rows, source: [FILL: export/purchase/scrape])

FIELDS NEEDED
  required-to-send:  work_email(verified), company_domain
  nice-to-have:      [FILL: title, seniority, employee_count, industry, linkedin_url, mobile_phone]
  skip:              [FILL: fields the play will not use]

WATERFALL (per field, stop at first VERIFIED hit)
  work_email:    1) [FILL: provider A]  2) [FILL: provider B]  3) pattern+verify fallback
  mobile_phone:  1) [FILL]              2) [FILL]
  firmographics: 1) [FILL: cheap provider]  2) [FILL: fallback]

VERIFICATION
  verifier:           [FILL: provider-native | dedicated]
  send threshold:     valid=SEND, catch-all=[FILL: skip|corroborate|other-channel],
                      risky=DROP, invalid=DROP
  reputation posture: [FILL: warming = strict | established = looser]

HYGIENE
  title vocab:    controlled seniority+function mapping
  company name:   strip suffixes, standardize
  dedupe key:     company_domain + normalized_email (fallback: linkedin_url)
  merge rule:     keep most-recently-verified per field; preserve provenance

FRESHNESS
  stamp:          last_verified_at on every row
  re-verify TTL:  90 days before any re-sequence

COST CEILING
  max credits: [FILL]   est. cost/verified record: $[FILL]   stop-if-over: $[FILL]
```

## Worked example

Inbound: a 4,200-row CSV from a conference attendee export - name, company, sometimes a personal Gmail, no work emails, no firmographics. Goal: a send-safe list for a quarterly sequence, on a newish (warming) domain.

1. Fields: required work_email(verified) + company_domain; nice-to-have title, seniority, employee_count, linkedin_url. Skip phone - this is an email play.
2. Waterfall: resolve company_domain from company name first (cheap), then work_email via provider A → provider B → pattern-generate-and-verify. Firmographics from a cheap firmographic provider, falling through to A.
3. Run and verify: 4,200 rows → providers return 3,050 work emails. Verification buckets them: 1,990 valid, 520 catch-all, 310 risky, 230 invalid. The 1,150 with no email found are parked for a LinkedIn-only sub-play.
4. Route by threshold (warming → strict): send the 1,990 valid; drop the 230 invalid and 310 risky. Of the 520 catch-all, 90 are target-account contacts → corroborate via LinkedIn plus a second provider, recovering 60 into send. Send list = 2,050.
5. Hygiene: normalize titles, standardize company names, dedupe on domain+email - 140 dupes found (same person from two badge scans). Net 1,930 unique send-ready.
6. Stamp last_verified_at = today on all; the list needs re-verification before any reuse next quarter.

Outcome: instead of blasting 3,050 unverified addresses and eating a ~16% bounce, 1,930 verified records go out - projected bounce under 2%, domain reputation intact.

## Triage calculator

Save as enrich-triage.mjs and run node enrich-triage.mjs leads.csv. It does heuristic bucketing and stale-flagging for fast triage - it does NOT replace real SMTP verification (no network mailbox check), so treat its "valid" as "passes basic hygiene, still must be verified."

```js
import { readFileSync } from "node:fs";

const ROLE = /^(info|sales|support|admin|contact|hello|team|office|billing|hr|jobs|noreply|no-reply)@/i;
const FREE = /@(gmail|yahoo|hotmail|outlook|icloud|aol|proton(mail)?|gmx)\./i;
const EMAIL = /^[^@\s]+@[^@\s]+\.[^@\s]+$/;
const STALE_DAYS = 90;

const rows = readFileSync(process.argv[2], "utf8").trim().split(/\r?\n/);
const head = rows.shift().split(",").map(s => s.trim().toLowerCase());
const col = n => head.indexOf(n);
const ei = col("email"), vi = col("last_verified_at"), di = col("company_domain");

const now = Date.now(), DAY = 864e5;
const bucket = { valid: [], risky: [], invalid: [], free: [], stale: [], missing: [] };
const seen = new Map(); let dupes = 0;

for (const line of rows) {
  const c = line.split(",").map(s => s.trim());
  const email = (c[ei] || "").toLowerCase();
  if (!email) { bucket.missing.push(line); continue; }
  if (!EMAIL.test(email)) { bucket.invalid.push(email); continue; }

  const domain = di >= 0 ? (c[di] || "").toLowerCase() : email.split("@")[1];
  const key = `${domain}|${email}`;
  if (seen.has(key)) { dupes++; continue; }
  seen.set(key, true);

  if (FREE.test(email)) bucket.free.push(email);          // personal inbox, not work email
  else if (ROLE.test(email)) bucket.risky.push(email);    // role address
  else bucket.valid.push(email);                          // passes hygiene - STILL must SMTP-verify

  if (vi >= 0 && c[vi]) {
    const age = (now - Date.parse(c[vi])) / DAY;
    if (Number.isFinite(age) && age > STALE_DAYS) bucket.stale.push(`${email} (${Math.round(age)}d)`);
  }
}

const n = rows.length;
const pct = x => `${((x / n) * 100).toFixed(1)}%`;
console.log(`\n${n} rows | ${dupes} dupes removed\n`);
for (const k of ["valid", "risky", "invalid", "free", "missing"])
  console.log(`  ${k.padEnd(8)} ${String(bucket[k].length).padStart(5)}  ${pct(bucket[k].length)}`);
console.log(`\n  STALE (>${STALE_DAYS}d, re-verify before send): ${bucket.stale.length}`);
if (bucket.stale.length) console.log("   " + bucket.stale.slice(0, 10).join("\n   "));
console.log(`\nNOTE: "valid" = passed hygiene only. Run a real verifier before sending.\n`);
```

## Deliverable

Produce three artifacts: (1) the filled enrichment plan (fields, per-field waterfall, send threshold, hygiene rules, cost ceiling), (2) the routed dataset - every record carrying email state, provenance, and last_verified_at, split into send / drop / corroborate / other-channel buckets, and (3) a one-paragraph summary with counts per bucket and projected bounce rate.

## Do NOT

- Do not trust provider emails as send-ready. Pattern-generated guesses are not SMTP-checked; always verify before sending (cold-email-deliverability owns the inbox side).
- Do not blanket-send to catch-all domains. Valid cannot be told from invalid there; mass-sending guarantees hidden bounces. Corroborate or reroute.
- Do not run the waterfall in parallel. Fanning out to all providers for one field burns credits on answers already found. Sequence, stop at first verified hit.
- Do not dedupe on display name. Bob/Robert at the same domain are one person; name-only keys both over-merge and under-merge. Key on domain+email or LinkedIn URL.
- Do not skip the freshness stamp. Without last_verified_at, a fresh record and a two-year-old one look identical, so rot gets re-sequenced. Re-verify past 90 days.
- Do not enrich fields the play will not use - every field costs credits. Mobile only if dialing; technographics only if the pitch references them.
- Do not overwrite the raw value with the verified one. Keep value, state, and provenance so a provider feeding junk can be audited and dropped.
- Do not use this skill to decide who belongs on the list - that is prospect-list-builder, scoped against icp-persona-builder.

## Quality bar

- Every send-bucket address is verifier-confirmed valid, not merely provider-supplied.
- Every record carries an email state (valid / catch-all / risky / invalid), provenance, and last_verified_at.
- The waterfall is sequenced per field with a stop-at-first-verified rule, and total spend stayed under the cost ceiling.
- Dedupe ran on a stable key and the merge preserved the most-recently-verified value per field.
- Projected bounce for the send bucket is under 2%; anything older than 90 days was re-verified before inclusion.
