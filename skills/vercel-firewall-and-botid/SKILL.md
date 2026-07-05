---
name: Vercel Firewall and BotID
description: Harden a Vercel app at the platform edge — the Vercel WAF (custom rules, IP blocking, managed rulesets like OWASP CRS + bot_protection + ai_bots, rate limiting), Attack Challenge Mode, system bypass rules, automatic DDoS mitigation, and BotID bot verification on sensitive routes. Use when someone says "protect my Vercel app", "add a WAF rule", "rate limit my API", "block this IP / country / user-agent", "I'm getting DDoSed", "stop bots / scrapers / AI crawlers", "turn on Attack Mode", "verify human vs bot on checkout/signup/login", or "set up BotID". Do NOT use for app-level auth/session logic — that is application code; do NOT use for env vars or secrets — use vercel-env-management; do NOT use for the deploy/promote/rollback flow — use vercel-deploy-pipeline.
---

# Vercel Firewall and BotID

This is the security stage of the ship-a-Next.js-app-on-Vercel workflow. You make
the app hard to attack at the edge — before a single request reaches a Function —
and you make abuse-prone routes prove the caller is human. Two systems do this,
and they are not interchangeable:

- The **Vercel WAF / Firewall** filters traffic by request shape (IP, path,
  geo, user-agent, headers, JA4) and by rate. It runs on the Edge Network in
  front of every deployment, included on all plans, and you pay nothing extra to
  deny a request before it bills compute. Configure it once per project.
- **BotID** is invisible, per-route bot verification you call from server code on
  the specific endpoints that get abused (checkout, signup, login, "add to cart",
  LLM-backed routes). It is a JS challenge plus deep analysis, and it does not
  show a CAPTCHA to humans.

Use both, in that order: the WAF is the blanket, BotID is the lock on the doors
that matter. This skill is opinionated and sequenced; it is deliberately **not** a
restatement of the official `vercel firewall` CLI plugin — it tells you what to
turn on, in what order, and what NOT to do.

Trigger eagerly on: "protect my Vercel app", "add a WAF rule", "rate limit",
"block an IP / country / bot", "I'm under attack" / "DDoS", "stop scrapers or AI
crawlers", "Attack Mode", or "verify human on `<route>`" / "set up BotID".

Where it fits in the pack: you should already have a green deploy
(`vercel-deploy-pipeline`) and managed secrets (`vercel-env-management`). Hand
back to `next-on-vercel-perf` if a firewall rule changes cache behavior, and to
`vercel-edge-and-isr` if you start gating ISR/static routes. LLM endpoints fronted
by `vercel-ai-gateway` are exactly the ones that deserve BotID.

## Mental model: five layers, outermost first

A request hits these in order. Stop abuse at the outermost layer that can see it —
the further out, the cheaper.

1. **Automatic DDoS mitigation** — always on, nothing to configure. Vercel's Edge
   Network absorbs and mitigates volumetric L3/L4 and many L7 floods globally. You
   do not turn this on; you escalate past it with the layers below when an attack
   is application-shaped.
2. **Managed rulesets** — Vercel-maintained rule bundles you toggle: the **OWASP
   Core Rule Set** (`owasp`) for SQLi/XSS/traversal patterns, **bot_protection**
   for known bad bots, **ai_bots** for AI scrapers/crawlers, and traffic-source
   rules. Each is set to an action: `log`, `challenge`, or `deny`.
3. **Custom rules** — your own conditions (path, IP, geo, user-agent, header,
   query, JA4) → an action. This is most of the day-to-day work.
4. **Rate limiting** — per-key request budgets, either as a custom-rule action or
   precisely in code via `@vercel/firewall`.
5. **BotID** — in-Function verification of the actual caller on named routes.

Attack Challenge Mode is a global override that sits on top of all of these: when
on, every visitor gets a managed challenge at the edge.

## Operating procedure

Run in order. Do not jump to BotID before the WAF baseline is set — most abuse
never needs application code to stop.

### Step 1 — Establish the WAF baseline (managed rulesets)

Turn on the Vercel-managed rulesets before writing any custom rule. In the
dashboard (Project → Firewall) or via SDK, enable and set actions:

- **OWASP CRS** (`owasp`): start at `log` and soak for 3–7 days of representative
  traffic (long enough to cover a weekly cycle of real usage) to learn its false
  positives, then move to `deny`. Going straight to `deny` on a busy app risks
  blocking legitimate requests you have not profiled.
- **bot_protection**: `challenge` or `deny` for known malicious bots.
- **ai_bots**: decide deliberately — `deny` if you do not want LLM crawlers
  training on or scraping your content; `log` if you are fine with it.

Managed rulesets are toggles with one of three actions each: `log` (observe),
`challenge` (managed edge challenge), `deny` (block). See
`references/managed-rulesets`.

### Step 2 — Write custom rules for your known-bad traffic

A custom rule is **condition(s) → action**. Conditions combine request
attributes; actions are `deny`, `challenge`, `bypass` (skip system
mitigations), `rate_limit`, or `redirect`. Author from real signal — your logs,
not hypotheticals.

Order matters: rules evaluate first-to-last and the first match wins, so put
**bypass/allow rules for trusted traffic ABOVE deny rules**, and specific before
broad. A common ordering mistake is a broad geo-deny above a bypass for your own
office IP — the deny fires first and locks you out.

Stage, review, publish (changes are draft until published):

```bash
# block a user-agent substring
vercel firewall rules add "Block scraper UA" \
  --condition '{"type":"user_agent","op":"sub","value":"crawler"}' \
  --action deny --yes

vercel firewall diff           # review staged draft
vercel firewall publish --yes  # promote to production
```

Discard a bad draft with `vercel firewall discard --yes`. See
`references/custom-rules`.

### Step 3 — Block individual IPs (the fast lever)

IP blocks are separate from custom rules and are the quickest response to a single
abusive source. They publish like rules:

```bash
vercel firewall ip-blocks block 1.2.3.4 --yes
vercel firewall ip-blocks block 1.2.3.4 --hostname app.example.com --yes  # scoped
vercel firewall ip-blocks unblock 1.2.3.4 --yes
vercel firewall ip-blocks list
```

Reach for IP blocks for a handful of bad actors. For a *pattern* of bad actors
(an ASN, a country, a UA family), write a custom rule instead — IP blocks do not
scale to a botnet.

### Step 4 — Rate limit abuse-prone paths

Two ways, and they are complementary:

**(a) WAF rate-limit rule** — declarative, at the edge, no code. Good for blanket
path protection:

```bash
vercel firewall rules add "Rate limit API" \
  --condition '{"type":"path","op":"pre","value":"/api"}' \
  --action rate_limit \
  --rate-limit-window 60 \
  --rate-limit-requests 100 \
  --rate-limit-keys ip \
  --rate-limit-action deny --yes
```

**(b) `@vercel/firewall` in code** — when the limit must key on something only your
app knows (an org id, an authenticated user, a tenant), call `checkRateLimit`
inside the handler:

```ts
import { checkRateLimit } from '@vercel/firewall'

export async function POST(request: Request) {
  const { rateLimited } = await checkRateLimit('update-object', {
    request,
    rateLimitKey: auth.orgId, // key on the org, not the raw IP
  })
  if (rateLimited) {
    return new Response(JSON.stringify({ error: 'Rate limit exceeded' }), {
      status: 429,
      headers: { 'Content-Type': 'application/json' },
    })
  }
  // ... handler
}
```

Rule of thumb: key on IP for anonymous routes, on a stable identity (user/org) for
authenticated ones — IP keys punish whole offices and mobile carriers sharing a
NAT. Starting budgets that hold up in practice: ~100 requests/60s per IP for a
general anonymous API, ~10 requests/60s per IP on login and password-reset (brute
force has no legitimate reason to exceed that), and single-digit requests per
minute per user on expensive mutating routes like checkout or LLM calls. Set the
budget from your p99 legitimate usage plus headroom, then tighten from the logs.
See `references/rate-limiting`.

### Step 5 — Add a system bypass for trusted callers

Some legitimate traffic must skip system-level mitigations — a verified mobile app,
a partner integration, an internal monitor. Create a bypass rule (action
`bypass` with `bypassSystem: true`) and place it ABOVE your deny rules so it wins
the first-match evaluation. Scope it as tightly as possible (a specific UA regex,
a header secret, an IP allowlist) — a loose bypass is a hole punched straight
through your firewall. See `references/system-bypass`.

### Step 6 — Keep Attack Challenge Mode ready (use it, then turn it off)

When you are actively under an L7 attack the WAF rules are not catching, flip
Attack Challenge Mode: every visitor gets a managed challenge at the edge, which
sheds automated load while letting humans through.

```bash
vercel firewall attack-mode enable --yes               # default ~1 hour
vercel firewall attack-mode enable --duration 24h --yes
vercel firewall attack-mode disable --yes
```

It is a blunt instrument — it adds friction for *every* human too. Treat it as a
fire extinguisher: enable for the duration of the incident, write a precise custom
rule for the attack signature while it is on, then disable Attack Mode and let the
rule carry the load. Do not leave it on indefinitely.

### Step 7 — Lock the critical routes with BotID

The WAF cannot tell a sophisticated headless browser from a human. BotID can.
Protect the specific routes where a bot causes real damage — checkout, signup,
login, coupon/credit redemption, "contact sales", and LLM-backed endpoints.

1. Install and wire the config wrapper:

```bash
npm i botid
```

```ts
// next.config.ts
import { withBotId } from 'botid/next/config'
const nextConfig = { /* your config */ }
export default withBotId(nextConfig)
```

2. Declare protected routes on the client so BotID attaches its classification
   headers (App Router: in the root layout `<head>`):

```tsx
import { BotIdClient } from 'botid/client'

const protectedRoutes = [{ path: '/api/checkout', method: 'POST' }]

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en">
      <head>
        <BotIdClient protect={protectedRoutes} />
      </head>
      <body>{children}</body>
    </html>
  )
}
```

3. Verify server-side in the handler and reject bots:

```ts
import { checkBotId } from 'botid/server'
import { NextResponse } from 'next/server'

export async function POST(request: Request) {
  const verification = await checkBotId()
  if (verification.isBot) {
    return NextResponse.json({ error: 'Access denied' }, { status: 403 })
  }
  // ... protected logic
}
```

The client `protect` list and the server `checkBotId()` call must cover the same
route and (if used) the same `checkLevel` — a mismatch blocks real users or lets
bots through. Use `checkLevel: 'deepAnalysis'` for the highest-value routes and
`'basic'` for lighter ones. See `references/botid`.

### Step 8 — Verify, then audit

Run the checklist tool below to confirm coverage, then watch the Firewall
observability tab through the soak window (3–7 days): rulesets at `log` reveal
what `deny` would have blocked, and BotID's dashboard shows the human/bot split
per protected route. Promote `log` → `deny` only after the log is clean — as a
working bar, a false-positive rate visibly above ~0.1% of legitimate traffic in
the log means the rule needs narrowing before it denies.

## Quality bar

A finished hardening pass is A+ only when all hold:

- Managed OWASP, bot_protection, and ai_bots are each explicitly set to a chosen
  action — none left at the default by omission, and the choice is justified.
- Every custom `deny`/`challenge` rule was profiled at `log` first, OR there is a
  stated reason it is safe to deny immediately (e.g. a known attack IP).
- Bypass/allow rules sit ABOVE deny rules; rule order has been read top-to-bottom
  and the author's own access path is not accidentally denied.
- Rate limits key on a stable identity for authenticated routes and on IP only for
  anonymous ones — no IP-keyed limit on a route behind a shared NAT.
- Every route that mutates money, accounts, or credits is BotID-protected, with the
  client `protect` list and server `checkBotId` `checkLevel` matching exactly.
- Attack Challenge Mode is OFF in steady state; if on, an incident is open and a
  specific rule is being written to replace it.
- All firewall changes were reviewed with `vercel firewall diff` before
  `publish` — nothing shipped straight from a draft unread.

## Do NOT

- Do NOT set OWASP CRS straight to `deny` on a live app without a `log` soak —
  you will block legitimate requests that merely look like an attack pattern.
- Do NOT order a broad geo/UA deny above your own bypass/allow rule — first match
  wins and you will lock yourself (or partners) out.
- Do NOT rate-limit authenticated routes by IP — corporate/mobile NAT means one
  abuser rate-limits an entire building. Key on user/org via `@vercel/firewall`.
- Do NOT leave Attack Challenge Mode on as a "default secure" posture — it taxes
  every human visitor and tanks conversion; it is an incident tool only.
- Do NOT rely on BotID for authorization. `isBot` answers "human or bot", not "is
  this user allowed" — keep your auth checks. BotID complements, never replaces,
  app-level authz.
- Do NOT scope a system-bypass rule loosely (e.g. bypass on a common UA substring)
  — that is a tunnel through your own firewall for anyone who spoofs it.
- Do NOT put secrets or block-list values in `next.config` / `vercel.ts`; rule
  config lives in the firewall, and secrets belong in `vercel-env-management`.
- Do NOT hand-edit a managed ruleset rule-by-rule expecting it to persist — managed
  rulesets are Vercel-maintained toggles; encode YOUR exceptions as custom rules.

## Checklist tool

Self-contained Node script — no dependencies, no network. It scores a project's
edge-security posture from a small config object you fill in, and prints the gaps
in priority order. Save as `firewall_audit.mjs` and run `node firewall_audit.mjs`.

```javascript
// Vercel edge-security audit. Edit the posture object, then: node firewall_audit.mjs
// Describe what is ACTUALLY configured on the project, honestly.
const posture = {
  managed: {
    owasp: 'log',          // 'off' | 'log' | 'challenge' | 'deny'
    botProtection: 'deny', // 'off' | 'log' | 'challenge' | 'deny'
    aiBots: 'off',         // 'off' | 'log' | 'challenge' | 'deny'
  },
  rateLimitedApiPaths: true,    // is /api (or hot paths) rate limited at all?
  authRoutesKeyedByIdentity: false, // auth'd rate limits key on user/org (not IP)?
  bypassAboveDeny: true,        // do allow/bypass rules sit above deny rules?
  attackModeOn: false,          // is Attack Challenge Mode enabled right now?
  incidentOpen: false,          // is there an active attack right now?
  // Routes that mutate money/accounts/credits, and whether BotID guards each.
  criticalRoutes: [
    { path: '/api/checkout', botid: true },
    { path: '/api/signup',   botid: false },
    { path: '/api/redeem',   botid: false },
  ],
}

const findings = []
const add = (sev, msg) => findings.push({ sev, msg })
const RANK = { CRITICAL: 0, HIGH: 1, MEDIUM: 2, LOW: 3 }

// Managed rulesets
if (posture.managed.owasp === 'off')
  add('HIGH', 'OWASP CRS is off — enable it (start at log, then deny after a soak).')
if (posture.managed.owasp === 'log')
  add('MEDIUM', 'OWASP CRS is at log — graduate to deny once the log is clean.')
if (posture.managed.botProtection === 'off')
  add('HIGH', 'bot_protection is off — set it to challenge or deny.')
if (posture.managed.aiBots === 'off')
  add('LOW', 'ai_bots is off — decide deliberately: deny to keep AI crawlers out, or log if intentional.')

// Rate limiting
if (!posture.rateLimitedApiPaths)
  add('HIGH', 'No rate limit on API paths — add a WAF rate_limit rule on /api.')
if (posture.rateLimitedApiPaths && !posture.authRoutesKeyedByIdentity)
  add('MEDIUM', 'Authenticated routes appear IP-keyed — key on user/org via @vercel/firewall so shared NAT is not punished.')

// Rule ordering
if (!posture.bypassAboveDeny)
  add('CRITICAL', 'Bypass/allow rules are NOT above deny rules — first match wins; you may be locking out trusted traffic (or yourself).')

// Attack mode hygiene
if (posture.attackModeOn && !posture.incidentOpen)
  add('HIGH', 'Attack Challenge Mode is ON with no open incident — it taxes every human; disable it and rely on rules.')
if (posture.incidentOpen && !posture.attackModeOn)
  add('HIGH', 'Active incident but Attack Mode is off — enable it for the incident window while you write a targeted rule.')

// BotID coverage on critical routes
for (const r of posture.criticalRoutes) {
  if (!r.botid) add('CRITICAL', `Critical route ${r.path} has no BotID — wire checkBotId() (client protect + server check).`)
}

findings.sort((a, b) => RANK[a.sev] - RANK[b.sev])
const covered = posture.criticalRoutes.filter((r) => r.botid).length
console.log('Edge-security audit')
console.log('Critical routes with BotID: ' + covered + '/' + posture.criticalRoutes.length)
console.log('Findings: ' + findings.length)
if (findings.length === 0) {
  console.log('  Clean — baseline, rate limits, ordering, and BotID all check out.')
} else {
  for (const f of findings) console.log('  [' + f.sev + '] ' + f.msg)
}
const blocked = findings.some((f) => f.sev === 'CRITICAL')
console.log('\nVerdict: ' + (blocked ? 'NOT SHIP-READY — resolve CRITICAL findings first.' : 'Ship-ready; address remaining findings on a soak.'))
```

### Worked example output

With the posture above the script prints:

```
Edge-security audit
Critical routes with BotID: 1/3
Findings: 5
  [CRITICAL] Critical route /api/signup has no BotID — wire checkBotId() (client protect + server check).
  [CRITICAL] Critical route /api/redeem has no BotID — wire checkBotId() (client protect + server check).
  [MEDIUM] OWASP CRS is at log — graduate to deny once the log is clean.
  [MEDIUM] Authenticated routes appear IP-keyed — key on user/org via @vercel/firewall so shared NAT is not punished.
  [LOW] ai_bots is off — decide deliberately: deny to keep AI crawlers out, or log if intentional.

Verdict: NOT SHIP-READY — resolve CRITICAL findings first.
```

Read it: the WAF baseline is mostly in place, but two money/account routes
(`/api/signup`, `/api/redeem`) have no BotID, which is the real exposure — a
headless browser sails past the WAF and hammers signup. Fix those first, then
graduate OWASP from `log` to `deny` and re-key the authenticated rate limits.
Re-run after each change.

## Template: edge-security plan

Copy this, fill the FILL fields, and keep it next to the project as the record of
what is protected and why.

```
EDGE SECURITY PLAN. [FILL: project] on Vercel. [FILL: date]

MANAGED RULESETS (action + why)
  OWASP CRS:        [FILL: log|challenge|deny]  — [FILL: reason]
  bot_protection:   [FILL: challenge|deny]      — [FILL: reason]
  ai_bots:          [FILL: log|deny]            — [FILL: keep out / allow]

CUSTOM RULES (top-to-bottom = evaluation order; allow/bypass ABOVE deny)
  1. [FILL: allow/bypass]  condition: [FILL]  action: bypass
  2. [FILL]                condition: [FILL]  action: [deny|challenge|rate_limit]
  (add rows; first match wins)

IP BLOCKS
  [FILL: ip / "none"]                            — [FILL: reason]

RATE LIMITS
  Anonymous (/[FILL] paths):  [FILL] req / [FILL]s, key=ip, on-exceed=deny
  Authenticated:              [FILL] req / [FILL]s, key=[user|org] via @vercel/firewall

SYSTEM BYPASS
  [FILL: who / "none"]   scope: [FILL: tight condition]

ATTACK MODE
  Steady state: OFF.  Runbook: enable --duration [FILL] during an incident,
  write a targeted rule, then disable.

BOTID-PROTECTED ROUTES (client protect + server checkBotId match)
  [FILL: /api/checkout]   checkLevel: [deepAnalysis|basic]
  [FILL: /api/signup]     checkLevel: [deepAnalysis|basic]
  (every route that moves money / accounts / credits)

REVIEW: all changes via 'vercel firewall diff' before 'publish'. Soak log-mode
rulesets [FILL] days before promoting to deny.
```

## references/managed-rulesets

Managed rulesets are Vercel-maintained bundles you toggle per project, each with a
single action: `log`, `challenge`, or `deny`. The ones that matter:

- **owasp** — the OWASP Core Rule Set: signatures for SQL injection, XSS, path
  traversal, and other common web attacks. The standard first line. Soak at `log`
  to learn its false positives against your real traffic, then promote to `deny`.
- **bot_protection** — known malicious/abusive bots. `challenge` or `deny`.
- **ai_bots** — AI scrapers and training crawlers. A content/policy decision:
  `deny` to keep them out, `log` if you intend to be indexed by them.
- **traffic-source / vercel rulesets** — additional Vercel-maintained categories;
  enable per your risk tolerance.

Set actions in the dashboard (Project → Firewall) or via the SDK
(`vercel.security.updateFirewallConfig` with `action: 'managedRules.update'`, the
ruleset id, and `{ active, action }`). Managed rules are toggles — encode your own
exceptions as custom rules (next section) rather than expecting to edit the bundle.

## references/custom-rules

A custom rule = one or more **conditions** combined into condition groups, mapped
to an **action**. Condition `type`s include `path`, `ip`, geo, `user_agent`,
`header`, `query`, and JA4 fingerprint; `op` is the match operator (`pre` prefix,
`sub` substring, `re` regex, `inc` includes, `eq`, etc.). Actions: `deny`,
`challenge`, `bypass`, `rate_limit`, `redirect`.

Author from real signal in the Firewall observability tab, not guesses. Three
things to get right every time:

1. **Order.** Rules evaluate first-to-last; the first match wins. Allow/bypass
   rules for trusted traffic go ABOVE deny rules. Specific before broad.
2. **Stage then publish.** Every change is a draft until published. Review with
   `vercel firewall diff`, then `vercel firewall publish --yes`; abandon a bad
   draft with `vercel firewall discard --yes`. Reorder with
   `vercel firewall rules reorder <name-or-id>`.
3. **Profile before denying.** A new broad rule starts at `challenge` or `log`,
   not `deny`, until you have watched it against live traffic.

CLI mirror of the dashboard: `vercel firewall rules add|list|inspect|enable|
disable|remove|reorder`, `vercel firewall overview`. The same config is available
via `@vercel/sdk` (`vercel.security.updateFirewallConfig`,
`action: 'rules.insert'`) for programmatic/IaC management.

## references/rate-limiting

Two surfaces, pick by what the key needs to be:

- **WAF rate-limit rule** (declarative, edge): a custom rule with action
  `rate_limit`, a window, a request count, keys (e.g. `ip`), and an on-exceed
  action (`deny` or `challenge`). Best for blanket path protection where IP is a
  fine key — anonymous `/api` traffic, login brute-force defense by IP.
- **`@vercel/firewall` `checkRateLimit`** (in code): call it inside the handler
  with a named limit and a `rateLimitKey` you compute — `auth.orgId`,
  `session.userId`, a tenant id. You can also pair it with a dashboard rule
  condition so the limit only applies under specific request shapes. Returns
  `{ rateLimited }`; respond `429` when true.

Keying guidance: anonymous → IP. Authenticated/tenant → stable identity. Never
IP-key an authenticated route — a single building or mobile carrier behind one NAT
will trip the limit for everyone. Choose windows by route cost: tight on
expensive/mutating endpoints (checkout, LLM calls), loose on cheap reads.

## references/system-bypass

A bypass rule lets specific trusted traffic skip system-level mitigations —
useful for a verified mobile app, a partner integration, or an internal uptime
monitor that would otherwise trip a managed rule. It is a custom rule whose action
is `bypass` with `bypassSystem: true` (via SDK,
`action.mitigate = { action: 'bypass', bypassSystem: true }`).

Two non-negotiables:

1. **Place it above deny rules** so first-match evaluation reaches it.
2. **Scope it tightly** — a header secret, a narrow UA regex, a small IP
   allowlist. A bypass on a common substring is a tunnel through your firewall that
   any attacker can walk through by spoofing the match. The narrower, the safer.

## references/botid

BotID is invisible bot verification you call per route. It runs a
client-side JS challenge plus server-side deep analysis and returns a verdict —
no CAPTCHA shown to humans. It catches sophisticated automation (headless
browsers, scripted clients) that the WAF cannot fingerprint by request shape
alone. It is a complement to the WAF, not a replacement, and it is **not**
authorization.

Wiring (Next.js App Router):

1. `npm i botid`, then wrap config with `withBotId` in `next.config` so the
   proxy rewrites are installed.
2. Declare protected routes client-side with `<BotIdClient protect={[...]} />` in
   the root layout `<head>` (Nuxt: `initBotId` in a plugin; SvelteKit and other
   frameworks have equivalents). This attaches the classification headers.
3. In the handler call `await checkBotId()` and reject when `verification.isBot`
   (respond `403`). For Server Actions, throw on `isBot`.

Tuning and gotchas:

- **`checkLevel`**: `'deepAnalysis'` for the highest-value routes, `'basic'` for
  lighter ones. The client `advancedOptions.checkLevel` and the server
  `checkBotId({ advancedOptions: { checkLevel } })` MUST match per route — a
  mismatch blocks real users or lets bots through.
- **Verified bots**: `checkBotId()` also returns `isVerifiedBot`,
  `verifiedBotName`, and `verifiedBotCategory`, so you can allow, say,
  `chatgpt-operator` while denying the rest.
- **Separate frontend/backend domains**: pass
  `advancedOptions.extraAllowedHosts` on the server so cross-domain requests are
  accepted.
- **Local dev**: simulate verdicts with
  `checkBotId({ developmentOptions: { bypass: 'BAD-BOT' } })` (default
  `'HUMAN'`) to exercise the bot branch without deploying.

Protect the routes where a bot causes real loss — checkout, signup, login,
credit/coupon redemption, and LLM-backed endpoints (the ones `vercel-ai-gateway`
fronts are prime targets, since each automated call costs real inference money).
Leave read-only/public routes alone; BotID adds a round trip you do not want on
every page.
