---
name: Cold Email Deliverability
description: Sets up and protects cold-outbound sending infrastructure - dedicated lookalike domains, SPF/DKIM/DMARC authentication, 2-4 week mailbox warm-up, 30-50 sends/day per-mailbox caps, and continuous bounce/complaint monitoring, with a runnable DNS audit script. Use when someone says "my emails are going to spam", "set up cold email infrastructure", "warm up a new domain", "configure SPF DKIM DMARC", "how many cold emails can I send per day", or "my domain got blacklisted". Do NOT use for protecting an opted-in marketing or transactional program on your primary domain - use email-deliverability instead; do NOT use for writing the email copy - use cold-email-craft instead; do NOT use for designing the multi-step cadence - use outreach-sequence-designer instead; do NOT use for sizing the list - use prospect-list-builder instead.
---

# Cold Email Deliverability

Deliverability is not a copywriting problem; it is an infrastructure and reputation problem. The single most expensive mistake in cold outbound is sending it from the primary corporate domain - one spam-trap hit or complaint spike and invoices, hiring emails, and the founder's replies all start landing in spam, and that damage is slow and painful to reverse. Treat cold sending as a blast radius to isolate on purpose: separate domains, separate mailboxes, conservative volume, relentless monitoring.

Mailbox providers (Gmail, Outlook) score the *sending identity* - domain + IP + behavioral signals like complaints, bounces, and engagement - not the cleverness of the subject line. Primary-inbox placement is earned by behaving like a real human correspondent at low volume. Good copy on a burned domain still lands in spam. Note the scope: cold outreach to people who never opted in. An opted-in newsletter or transactional program follows different rules (subdomains of the primary domain, higher warm-up ramps, sunset policies) - that is email-deliverability territory.

## Inputs to collect

- Target daily send volume (or monthly prospect goal ÷ ~20 business days).
- Primary corporate domain, and confirmation that it will NOT be used for cold sends.
- Existing sending domains, their age, and any prior reputation damage (blacklist history, complaint spikes).
- ESP/mailbox provider (Google Workspace, Microsoft 365) and any existing SPF includes on the sending domains.
- List source and whether addresses are verified. If unverified, stop - route through lead-enrichment first.
- Label any volume or list-quality figure the user estimates rather than measures as a guess.

## The workflow

1. **Isolate the blast radius - separate sending domains.** Never send cold from the primary domain (acme.com). Register lookalike domains (trygetacme.com, acme-team.com, getacme.io) dedicated to outbound. Redirect them (301) to the main site so they resolve. If one gets burned, pause and rotate it without touching corporate mail.

2. **Authenticate DNS - all three, or you are getting filtered.**
   - **SPF** (TXT record): authorizes which servers may send for the domain. Publish exactly **one** SPF record; multiple SPF TXT records = permerror = fail. Stay under the **10 DNS-lookup limit** (each include: counts; nested includes count too). Example: `v=spf1 include:_spf.google.com ~all`.
   - **DKIM** (TXT at a selector subdomain, e.g. selector._domainkey.domain): cryptographically signs each message so the receiver verifies it was not altered and the domain authorized it. Use a per-domain **2048-bit** key (1024 is legacy/weak).
   - **DMARC** (TXT at _dmarc.domain): tells receivers what to do when SPF/DKIM fail and requires **alignment** (visible From domain matches the authenticated domain). Start at `p=none` to monitor via aggregate reports, then tighten to `p=quarantine` and eventually `p=reject` once legitimate mail passes cleanly. Example: `v=DMARC1; p=none; rua=mailto:dmarc@domain; adkim=s; aspf=s`.
   - Together: SPF says "this server is allowed," DKIM says "this message is intact and authorized," DMARC ties them to the visible From and sets enforcement. Gmail/Yahoo bulk-sender rules effectively require all three.

3. **Age and warm before sending a single real prospect.** Brand-new domains have zero reputation and get filtered hard. Register at least 2 weeks before launch, then **warm each mailbox for 2-4 weeks**: start at ~5 sends/day and ramp gradually toward the cap, mixed with received and replied mail (warmup tools or a manual network) so providers see natural two-way engagement before volume climbs.

4. **Cap per-mailbox volume and scale horizontally.** Treat **~30-50 cold sends/day/mailbox** as the ceiling, not a target. To send more, add mailboxes (2-3 per domain) and rotate across a pool. Size the list to capacity: 5 mailboxes × 40/day ≈ 200 new prospects/day. Sequencing volume against list size is prospect-list-builder territory.

5. **List hygiene - verify before sending.** Every address goes through verification first (lead-enrichment). Remove invalids, role accounts (info@, sales@), and risky catch-all domains, or sample them carefully. **Bounce rate is the fastest way to torch a new domain** - keep it under 2-3%. A dirty list burns warm infrastructure in a single batch.

6. **Content hygiene - look like a person, not a campaign.** Plain text beats heavy HTML. **No images and at most one link in the first touch** (links are the top silent spam trigger). Avoid spam-trigger phrasing ("free", "guarantee", "act now", excessive caps/exclamation). Use a **custom tracking domain** if tracking opens, or turn open-tracking off - shared tracking domains are widely blacklisted. Include a real, plain-text opt-out.

7. **Monitor the signals providers actually measure.** Watch **bounce rate** (<2-3%), **spam-complaint rate** (keep well under 0.1% - the red line; Gmail enforces at 0.3%), and **reply rate** as a health proxy (a sudden drop usually means placement collapsed, not worse copy). Run seed/inbox-placement tests across Gmail/Outlook/Yahoo before and during campaigns. Enroll every sending domain in Google Postmaster Tools for real reputation and spam-rate data.

8. **When a domain gets burned: pause, diagnose, rotate.** Stop sending from it immediately. Check blacklists (MXToolbox), Postmaster reputation, recent bounce/complaint spikes, and whether DNS auth silently broke. Light damage: drop volume to near-zero and re-warm for weeks. Heavy damage (blacklisted, complaint spike): retire the domain, rotate to a fresh warmed one, and fix the upstream cause - bad list or aggressive volume - before reusing the pattern.

## Pre-flight checklist

```
INFRASTRUCTURE
[ ] Sending domain(s) SEPARATE from primary corporate domain
[ ] Each sending domain 301-redirects to the main site and resolves
[ ] Domain registered >= 2 weeks ago (aged, not same-day)

AUTHENTICATION (verify with the audit script below)
[ ] Exactly ONE SPF TXT record, ends in ~all or -all, under 10 DNS lookups
[ ] DKIM published at selector._domainkey, 2048-bit key, signs outbound
[ ] DMARC at _dmarc, p=none to start, rua= reporting address set
[ ] SPF + DKIM align with the visible From domain (DMARC passes)

WARMUP & VOLUME
[ ] Each mailbox warmed 2-4 weeks, volume ramped gradually from ~5/day
[ ] Per-mailbox daily cap set (<= 30-50 cold sends/day)
[ ] Mailbox pool + rotation configured; list sized to total capacity

LIST & CONTENT
[ ] Every address verified; invalids/role/catch-all removed
[ ] Projected bounce rate < 2-3%
[ ] First touch: plain text, <= 1 link, no images, no spam-trigger words
[ ] Custom tracking domain configured, or open-tracking OFF
[ ] Real plain-text unsubscribe / opt-out present

MONITORING
[ ] Google Postmaster Tools enrolled for each sending domain
[ ] Seed/inbox-placement test passed (primary, not spam/Promotions)
[ ] Dashboards for bounce %, complaint %, reply % with alert thresholds
```

## Worked example: infrastructure for 200 cold sends/day

Goal: reach ~4,000 net-new prospects/month (~200/business day).

```
Capacity math:  200/day ÷ 40 cold sends/mailbox/day = 5 mailboxes needed.
Domains:        2 sending domains, 3 mailboxes each = 6 mailboxes (1 spare).
                trygetacme.com  -> ava@, ava.chen@, hello@   (301 -> acme.com)
                acme-team.com   -> ava@, ava.chen@, hello@   (301 -> acme.com)
DNS per domain: SPF   TXT @              v=spf1 include:_spf.google.com ~all
                DKIM  TXT google._domainkey   (2048-bit, from Google Admin)
                DMARC TXT _dmarc  v=DMARC1; p=none; rua=mailto:dmarc@acme.com; adkim=s; aspf=s
Timeline:       Wk 0 register + DNS + redirect.  Wk 1-3 warm all 6 mailboxes
                (start ~5/day, ramp to ~40).  Wk 4 begin real sends at full cap.
Real volume:    5 active mailboxes × 40 = 200/day.  Hold 1 mailbox in reserve.
List sizing:    queue ~200 verified prospects/day; bounce budget < 6/day (3%).
Guardrails:     complaint-rate alert at 0.1%; if any domain spikes, pause it,
                shift load to the spare, re-warm, diagnose before resuming.
```

## Authentication audit script (Node, zero deps)

Run before launch and on a schedule: `node check-deliverability.mjs trygetacme.com [dkim-selector]`

```javascript
#!/usr/bin/env node
// check-deliverability.mjs - sanity-check SPF/DKIM/DMARC for a sending domain.
// Usage: node check-deliverability.mjs <domain> [dkimSelector=google]
import { resolveTxt } from 'node:dns/promises';

const domain = process.argv[2];
const selector = process.argv[3] || 'google';
if (!domain) { console.error('usage: node check-deliverability.mjs <domain> [dkimSelector]'); process.exit(2); }

const out = [];
const log = (level, label, msg) => out.push({ level, label, msg });

async function txt(name) {
  try {
    // resolveTxt returns string[][]; join each record's chunks (DNS 255-char splits).
    return (await resolveTxt(name)).map(chunks => chunks.join(''));
  } catch (e) {
    if (e.code === 'ENOTFOUND' || e.code === 'ENODATA') return [];
    throw e;
  }
}

// Count SPF DNS lookups (include/a/mx/ptr/exists/redirect) one level deep.
async function countSpfLookups(record, seen = new Set(), depth = 0) {
  if (depth > 5) return 0; // guard runaway recursion
  let count = 0;
  for (const term of record.split(/\s+/)) {
    const m = term.match(/^(include|a|mx|ptr|exists|redirect)[:=]?(.*)$/i);
    if (!m) continue;
    count++;
    const target = m[2];
    if (m[1].toLowerCase() === 'include' && target && !seen.has(target)) {
      seen.add(target);
      const [nested] = (await txt(target)).filter(r => /^v=spf1/i.test(r));
      if (nested) count += await countSpfLookups(nested, seen, depth + 1);
    }
  }
  return count;
}

async function checkSpf() {
  const spf = (await txt(domain)).filter(r => /^v=spf1/i.test(r));
  if (spf.length === 0) return log('FAIL', 'SPF', 'no v=spf1 record found');
  if (spf.length > 1) return log('FAIL', 'SPF', `${spf.length} SPF records (must be exactly 1 - multiple = permerror)`);
  const rec = spf[0];
  if (!/[~\-]all\s*$/.test(rec)) log('WARN', 'SPF', 'does not end in ~all or -all (open policy)');
  const lookups = await countSpfLookups(rec);
  if (lookups > 10) log('FAIL', 'SPF', `${lookups} DNS lookups (limit is 10 - exceeding = permerror)`);
  else log('PASS', 'SPF', `1 record, ${lookups} lookups, ${(/-all/.test(rec) ? 'hard' : 'soft')} fail policy`);
}

async function checkDkim() {
  const name = `${selector}._domainkey.${domain}`;
  const recs = (await txt(name)).filter(r => /v=DKIM1|p=/i.test(r));
  if (recs.length === 0) return log('FAIL', 'DKIM', `no record at ${name} (try the right selector)`);
  const rec = recs[0];
  const p = (rec.match(/p=([A-Za-z0-9+/=]*)/) || [])[1] || '';
  if (p === '') return log('FAIL', 'DKIM', 'key revoked/empty (p=)');
  // RSA 2048-bit public key in base64 DER is ~392 chars; 1024-bit is ~216.
  const strength = p.length > 300 ? '~2048-bit' : '~1024-bit (weak - upgrade)';
  log(p.length > 300 ? 'PASS' : 'WARN', 'DKIM', `key present at selector "${selector}", ${strength}`);
}

async function checkDmarc() {
  const recs = (await txt(`_dmarc.${domain}`)).filter(r => /^v=DMARC1/i.test(r));
  if (recs.length === 0) return log('FAIL', 'DMARC', `no record at _dmarc.${domain}`);
  const rec = recs[0];
  const policy = (rec.match(/\bp=(none|quarantine|reject)\b/) || [])[1];
  const hasRua = /rua=mailto:/i.test(rec);
  if (!policy) return log('FAIL', 'DMARC', 'no p= policy tag');
  if (!hasRua) log('WARN', 'DMARC', `p=${policy} but no rua= (you get no aggregate reports)`);
  else log(policy === 'none' ? 'WARN' : 'PASS', 'DMARC',
    `p=${policy}${policy === 'none' ? ' (monitor-only - tighten toward quarantine/reject)' : ''}, reporting on`);
}

await Promise.all([checkSpf(), checkDkim(), checkDmarc()]);
const order = { FAIL: 0, WARN: 1, PASS: 2 };
out.sort((a, b) => order[a.level] - order[b.level]);
for (const r of out) console.log(`[${r.level.padEnd(4)}] ${r.label.padEnd(5)} ${r.msg}`);
const fails = out.filter(r => r.level === 'FAIL').length;
console.log(`\n${fails ? `${fails} blocking issue(s) - fix before sending.` : 'No blocking auth issues.'}`);
process.exit(fails ? 1 : 0);
```

Expected output on a healthy domain:

```
[PASS] SPF   1 record, 3 lookups, soft fail policy
[PASS] DKIM  key present at selector "google", ~2048-bit
[WARN] DMARC p=none (monitor-only - tighten toward quarantine/reject), reporting on

No blocking auth issues.
```

## Deliverable

Produce a launch-ready infrastructure plan: the domain and mailbox pool sized to the target volume with the capacity math shown, the three DNS records per domain, a week-by-week warm-up ramp, the completed pre-flight checklist, and the audit script output showing zero FAILs.

## Common failure modes

- **Sending cold from the primary domain.** The classic catastrophe - one complaint spike poisons all corporate mail. Always isolate on dedicated domains.
- **Two SPF records, or blowing the 10-lookup limit.** Both produce a permerror and silently fail SPF - common after adding a second ESP. Flatten or consolidate; the script catches both.
- **DMARC stuck at p=none forever.** It only monitors, never enforces. Move to quarantine/reject once mail passes cleanly.
- **Skipping warmup.** Blasting a brand-new domain at full volume is the fastest way to land in spam permanently.
- **Treating the daily cap as a target.** ~40/mailbox is a ceiling for cold mail; scale with more mailboxes, not more per mailbox.
- **Unverified lists / catch-all domains.** One high-bounce batch torches well-warmed infrastructure. Verify first; keep bounces under 2-3%.
- **Open-tracking on a shared tracking domain, or image-heavy HTML.** Both scream bulk campaign. Custom tracking domain or none.
- **No monitoring until replies dry up.** By then the domain is burned. Watch complaint and bounce rates continuously; enroll in Postmaster Tools from day one.

## Quality bar

The setup ships only when: every pre-flight checkbox is ticked; the audit script reports zero FAILs on every sending domain; each mailbox has completed its 2-4 week ramp; the seed test lands in the primary inbox on Gmail and Outlook; and alerts exist for bounce >2%, complaints >0.1%, and reply-rate collapse. If any sending domain is also the corporate domain, do not launch - that configuration belongs to email-deliverability rules and must never carry cold volume.
