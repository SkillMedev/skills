---
name: Email Deliverability
description: Audits and protects sender reputation and inbox placement for permissioned marketing and lifecycle email - SPF, DKIM, and DMARC authentication, domain warmup, bounce and complaint thresholds, and engagement sunsetting - and delivers a scored deliverability audit checklist with a remediation order. Use when someone asks "why are my newsletters going to spam", "how do I set up SPF, DKIM, and DMARC", "is my bounce rate too high", "should I send marketing from a subdomain", or is launching a new sending domain or auditing an existing opted-in email program. Do NOT use for cold outbound infrastructure - lookalike sending domains, mailbox pools, per-mailbox volume caps - use cold-email-deliverability instead; do NOT use to write the emails themselves - use email-drip-builder or email-newsletter-pro instead.
---

# Email Deliverability

Sender reputation takes months to build and days to destroy, and most teams discover a deliverability problem only after open rates have already cratered. This skill audits and protects inbox placement for permissioned marketing and lifecycle email - authentication, warmup, list hygiene, and engagement - and returns a scored audit with fixes in dependency order. It exists to catch the silent killers (an unenforced DMARC policy, a stale segment, a skipped warmup) before an inbox provider does.

## Operating procedure

Work the steps in order: authentication failures poison every downstream metric, so fix identity before hygiene, and hygiene before engagement.

### Step 1: Gather inputs

Collect these before auditing anything. If a number is estimated, label it a guess and verify against the ESP dashboard before the audit ships.

- Sending domain(s), and whether marketing and transactional mail share one identity.
- ESP(s) in use; shared or dedicated sending IP.
- List size, acquisition sources (signup form, import, purchase - a purchased source is an automatic red flag), and list age.
- Last 30 days of: delivered volume, hard and soft bounce rates, spam complaint rate, open and click rates by segment.
- Google Postmaster Tools access. If the domain is not enrolled, enrolling is the first remediation item - it is the only direct view of how Gmail scores the domain.
- DMARC report destination, if one exists.

### Step 2: Verify authentication by DNS lookup, not by the ESP settings screen

SPF, DKIM, and DMARC are not optional; Gmail and Yahoo bulk-sender requirements effectively mandate all three plus one-click unsubscribe.

- SPF: exactly one v=spf1 TXT record (two records is a permerror and silently fails), ending in ~all or -all. SPF declares which servers may send on the domain's behalf.
- DKIM: cryptographically signs each message; confirm every marketing send is signed.
- DMARC: tells receivers what to do with unauthenticated mail and where to send reports. Ramp the policy: start at p=none to monitor, move to p=quarantine within 30 days of launch, and p=reject within 90 days once aggregate reports confirm no legitimate mail is failing. A p=none policy left in place indefinitely is monitoring without protection.

### Step 3: Separate mail streams

Send marketing from a dedicated subdomain (mail.example.com, not example.com) configured as a distinct sending identity with its own SPF and DKIM records. This insulates transactional mail - receipts, password resets - from reputation damage caused by campaigns. If both streams share one identity today, flag it as a structural finding.

### Step 4: Warm up anything new

A new IP or domain has no sending history. Start at 200 to 500 sends per day, doubling volume every three to five days while watching bounce and complaint rates. Skipping warmup gets the domain blocked before the list has been reached. Use a structured warmup plan from the ESP or a dedicated warmup tool, and keep weekly volume consistent afterward - 10x spikes look like a compromised account.

### Step 5: Audit list hygiene against hard thresholds

- Hard bounce rate below 0.5 percent. A hard bounce means the address does not exist; remove it immediately and automatically after the first occurrence, and never retry it.
- Total bounce rate above 2 percent is a list-quality emergency: pause sends to the offending acquisition source and re-verify the segment before resuming.
- No purchased or scraped addresses in any segment, ever.

### Step 6: Check complaints and engagement

- Spam complaint rate below 0.1 percent on every segment - that is the threshold at which most major inbox providers begin filtering, and Gmail's enforcement red line sits at 0.3 percent. A spike above 0.1 percent requires pausing sends, auditing the segment, and diagnosing the cause before resuming.
- Sender Score (Validity) of 80 or above is healthy; below 70, expect filtering and cut volume while diagnosing.
- Sunset unengaged subscribers: anyone with no open or click in 180 days drags down the engagement signals providers use to score quality. Run a re-permission campaign before the 180-day mark and suppress non-responders. A smaller, engaged list consistently outperforms a large, stale one.

### Step 7: Score the audit and order the fixes

Complete the checklist below, mark each item pass or fail, and order remediation by dependency: authentication, then stream separation, then hygiene, then complaints, then engagement.

## Worked artifact: deliverability audit checklist

```
DELIVERABILITY AUDIT - [FILL: domain] - [FILL: auditor]

AUTHENTICATION
[ ] Exactly ONE SPF (v=spf1) TXT record, ending ~all or -all
[ ] DKIM signing verified on every marketing send
[ ] DMARC published at p=quarantine or p=reject
    (p=none acceptable only during initial monitoring window)
[ ] DMARC rua= reporting address set and reports reviewed

STREAM SEPARATION
[ ] Marketing sends from a dedicated subdomain (e.g. mail.example.com)
[ ] Subdomain has its own SPF and DKIM records
[ ] Transactional mail isolated on a separate identity

VOLUME & WARMUP
[ ] New domain/IP warmed from 200-500/day, doubling every 3-5 days
[ ] Weekly volume consistent - no unexplained spikes

LIST HYGIENE
[ ] Hard bounce rate < 0.5%; hard bounces suppressed after first occurrence
[ ] Total bounce rate < 2% on every segment
[ ] No purchased or scraped addresses; acquisition source recorded per subscriber

COMPLAINTS & ENGAGEMENT
[ ] Spam complaint rate < 0.1% on every segment
[ ] Google Postmaster Tools enrolled; domain reputation Medium or High
[ ] One-click unsubscribe (List-Unsubscribe header) present
[ ] 180-day unengaged segment identified; re-permission campaign scheduled
[ ] Sunset policy suppresses non-responders automatically

VERDICT: [FILL: healthy / at risk / failing]
TOP 3 FIXES IN ORDER: [FILL]
RE-CHECK IN 30 DAYS: [FILL: the 2-3 metrics to re-measure]
```

## Deliverable

Produce the completed audit checklist plus a remediation plan: every failed item, its fix, an owner, and the dependency order - with the two or three metrics to re-measure 30 days later.

## Do NOT

- Do not retry hard bounces - the address does not exist, and repeated attempts signal poor hygiene to providers.
- Do not leave DMARC at p=none past the monitoring window - it reports failures but blocks nothing.
- Do not blast a re-permission campaign to the entire stale segment at once; send it in slices so a complaint spike from the least-engaged cohort cannot torch the domain.
- Do not send marketing and transactional mail from the same identity - one bad campaign then delays password resets.
- Do not treat deliverability as one-time configuration; complaint and reputation data need a standing review cadence.
- Do not confuse this with cold outbound. Cold sending uses separate lookalike domains and per-mailbox caps precisely because it is expected to burn identities - those rules live in cold-email-deliverability.

## Quality bar

- Every metric in the audit is pulled from the ESP or Postmaster Tools, not estimated; any guess is labeled as a guess.
- Authentication verified by actual DNS lookup, not the ESP's green checkmark.
- The verdict cites the numbers against the thresholds (0.5% hard bounce, 2% total bounce, 0.1% complaints, DMARC enforcement state).
- Remediation is ordered by dependency and every item has an owner.

## Scope and escalation

This skill covers permissioned marketing and lifecycle sending from the brand's own domain. For cold outbound infrastructure, use cold-email-deliverability. For sequence content, use email-drip-builder; for newsletter craft, email-newsletter-pro; for re-engaging lapsed customers as a campaign, win-back-campaign. If the domain is on public blocklists or under a provider enforcement action, treat recovery as its own project: cut volume to the most-engaged decile and rebuild slowly rather than sending through the damage.
