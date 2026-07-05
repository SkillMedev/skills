---
name: Vercel AI Gateway
description: Route every LLM call through one unified API on Vercel - plain "provider/model" strings via the AI SDK, automatic provider routing and model fallbacks, observability and per-key cost tracking, and zero data retention. Use when you ask "how do I add an LLM to my Vercel app", "route OpenAI and Anthropic through one API", "add model failover / fallback", "track LLM cost and spend on Vercel", "set a budget per API key", "switch providers without changing code", "AI_GATEWAY_API_KEY", or "stop installing @ai-sdk/openai and @ai-sdk/anthropic separately". Do NOT use for general AI SDK app code (chat UI, tool calling, structured output) - that is the AI SDK itself; do NOT use for deploy config, env wiring, or edge/runtime choice - use vercel-deploy-pipeline, vercel-env-management, and vercel-edge-and-isr instead; do NOT use to rate-limit or block abusive AI traffic - use vercel-firewall-and-botid; and do NOT use for app-level latency/caching tuning of the route - use next-on-vercel-perf.
---

# Vercel AI Gateway

You wire an app's LLM calls through **Vercel AI Gateway**: one endpoint, one API
key, every provider behind it. The opinionated rule of this pack is *never reach
for a provider-specific package by default*. Instead of `@ai-sdk/openai` +
`@ai-sdk/anthropic` + `@ai-sdk/google` (each with its own SDK, its own key, its
own billing), you pass a plain `"provider/model"` string to the AI SDK and let
the Gateway resolve it. That single decision buys you provider failover, model
fallbacks, unified cost tracking, and a one-line provider swap - for free, with
no code change at the call site.

This is the AI step of the curated *ship-a-Next.js-app-on-Vercel* path. It is
deliberately **not** a clone of the official Vercel AI Gateway docs or CLI: it
sequences the Gateway into the rest of the workflow. Provisioning the key and
promoting it across environments is `vercel-env-management`; the function it runs
in (Fluid Compute, full Node.js - the legacy Edge runtime is deprecated) is
`vercel-edge-and-isr`; shipping it is `vercel-deploy-pipeline`. Rate-limiting or
blocking abusive traffic to the AI route is `vercel-firewall-and-botid`, and
app-level latency/caching of the route is `next-on-vercel-perf`. This skill owns
exactly one thing: the call should go through the Gateway as a bare model string.

Trigger eagerly whenever someone is adding an LLM to a Vercel app, juggling
multiple providers, wants failover or cost caps, or is about to `npm install` a
provider-specific AI package. Catch loose phrasings: "make it call GPT and Claude",
"add a fallback model", "why is my LLM bill a surprise", "one API for all my AI".

## Workflow

Run these in order. Do not skip Step 2 - the whole value of the Gateway is that
the call site is provider-agnostic, and a provider-specific package at the call
site throws that away.

### Step 1 - Authenticate once, two-line fallback

The Gateway needs exactly one credential. Locally that is an `AI_GATEWAY_API_KEY`
(create it in the Vercel dashboard under AI Gateway → API Keys, put it in
`.env.local`). On a Vercel deployment you do not need a key at all - Fluid Compute
injects a `VERCEL_OIDC_TOKEN` automatically and the AI SDK's gateway provider
picks it up. Write code that works in both places with one fallback:

```ts
// Works locally (API key) AND on Vercel (OIDC, auto-injected).
const apiKey = process.env.AI_GATEWAY_API_KEY || process.env.VERCEL_OIDC_TOKEN
```

When you use the AI SDK's default gateway provider you usually do not even touch
this - the SDK reads `AI_GATEWAY_API_KEY` / `VERCEL_OIDC_TOKEN` for you. Hand the
actual provisioning and per-environment promotion of `AI_GATEWAY_API_KEY` to
`vercel-env-management`; do not paste keys into `vercel.ts` or commit `.env.local`.

### Step 2 - Call with a plain "provider/model" string (the core move)

Install **only** the AI SDK. Do **not** install `@ai-sdk/openai`,
`@ai-sdk/anthropic`, `@ai-sdk/google`, etc. - the Gateway is the provider.

```bash
npm install ai          # the AI SDK. That's it. No provider packages.
```

Then pass the model as a string `"<provider>/<model>"`. The AI SDK routes any
bare model string through the Gateway by default:

```ts
import { streamText } from 'ai'

const result = streamText({
  model: 'anthropic/claude-opus-4.7',   // <-- provider/model, resolved by the Gateway
  prompt,
})
return result.toUIMessageStreamResponse()
```

Switching providers is now editing one string - `'openai/gpt-5.5'`,
`'google/gemini-3.1-pro-preview'` - with no new import, no new key, no new SDK.
That is the entire reason this skill exists. If you find yourself typing
`import { openai } from '@ai-sdk/openai'`, stop: you have left the Gateway.

(For the wider AI SDK surface - tool calling, structured output, chat UI,
embeddings - that is the AI SDK itself, a sibling concern, not this skill.)

### Step 3 - Add model fallbacks and provider routing

A single model string can silently fail (provider outage, rate limit, region
blip). Make the call resilient with `providerOptions.gateway`:

- **`models`** - an ordered list of *fallback models* tried if the primary fails.
- **`order`** - preferred *provider order* for a model offered by several
  providers (e.g. Claude via `anthropic`, `bedrock`, or `vertex`).
- **`only`** - restrict routing to an allow-list of providers (compliance / BYOK).

```ts
import { streamText } from 'ai'

const result = streamText({
  model: 'openai/gpt-5.5',                       // primary
  prompt,
  providerOptions: {
    gateway: {
      models: ['anthropic/claude-opus-4.7',      // fallback 1 (different provider)
               'google/gemini-3.1-pro-preview'], // fallback 2
      order: ['azure', 'openai'],                // provider preference per model
    },
  },
})
```

Keep the fallback chain to 2-3 models: each failed hop can add up to a full
request timeout to worst-case latency, and if three models across different
providers all fail, the outage is systemic - a fourth entry buys nothing. Put at
least one fallback on a *different provider* than the primary, or a provider-wide
outage takes the whole chain down.

The Gateway records each hop in response metadata (`modelAttempts` →
`providerAttempts`) so you can see exactly which provider/model served the request
and why an earlier one was skipped. Use fallbacks for anything user-facing; a
single bare model string is fine only for internal/batch work that can retry.

### Step 4 - Turn on zero data retention where data is sensitive

For PII or regulated content, set `zeroDataRetention: true` so the Gateway routes
only to providers that honor ZDR and deletes payloads after processing:

```ts
const result = await streamText({
  model: 'anthropic/claude-opus-4.7',
  prompt: 'Summarize this patient intake form...',
  providerOptions: { gateway: { zeroDataRetention: true } },
})
```

Pair this with `only: [...]` when a contract restricts you to specific compliant
providers. ZDR narrows the routable provider set, so confirm your fallbacks in
Step 3 are all ZDR-capable, or the fallback may be dropped.

### Step 5 - Observe spend and cap budgets per key

The Gateway is also your cost-tracking layer - usage, latency, and spend per model
land in the AI Gateway dashboard with no instrumentation. For programmatic control,
each API key carries an optional budget (`limitAmount` + `refreshPeriod`) and you
can read live spend from the quotas endpoint:

```bash
# Live budget + spend for one AI Gateway key.
curl "https://ai-gateway.vercel.sh/v1/quotas?quotaEntityId=api_key_id_<your_key_id>" \
  -H "Authorization: Bearer $AI_GATEWAY_API_KEY"
# -> { "limitAmount": 10, "currentSpend": 1.04, "refreshPeriod": "monthly", "active": true }
```

Give each environment its own key (prod, preview) via `vercel-env-management` so
spend is attributable and a runaway preview cannot drain the prod budget. Keep
preview/dev key budgets small - $5-25/month is plenty for testing, and a runaway
loop then trips at pocket change instead of at the invoice. Size the prod budget
about 20% above estimated spend (the calculator below does this) so normal
traffic never hits the cap. Note that per-token prices span roughly two orders of
magnitude across models - always read the primary *and* fallback models' pricing
from `gateway.getAvailableModels()` rather than assuming they are comparable.

## Quality bar

A Gateway integration is A+ only when all hold:

- The call site uses a **plain `"provider/model"` string** through the AI SDK. No
  `@ai-sdk/<provider>` package is installed or imported anywhere.
- Exactly **one** credential path: `AI_GATEWAY_API_KEY` locally, `VERCEL_OIDC_TOKEN`
  on Vercel, expressed as the `||` fallback - keys provisioned via
  `vercel-env-management`, never committed.
- Any user-facing call has **`models` fallbacks** (2-3 entries, at least one on a
  different provider); a bare model string is used only for retryable internal work.
- Sensitive paths set **`zeroDataRetention: true`** and confirm every fallback
  provider is ZDR-capable.
- Each environment key has a **budget**, and the launch budget was sized from the
  cost estimator, not guessed.
- Switching the primary model is a **one-string edit** - proven by the fact that
  no provider import exists to change.

## Do NOT

- Do NOT install or import `@ai-sdk/openai`, `@ai-sdk/anthropic`, `@ai-sdk/google`,
  or any other provider package "just to start." That is the anti-pattern this
  skill exists to prevent - it re-introduces per-provider keys, billing, and a
  hard-coded provider at the call site.
- Do NOT scatter raw provider API keys (`OPENAI_API_KEY`, `ANTHROPIC_API_KEY`)
  through the app. The Gateway holds provider credentials; your app holds one key.
- Do NOT hardcode the model string in ten files. Read it from config/env so a
  swap or a fallback edit is one change (still a bare string, still the Gateway).
- Do NOT ship a single bare model string for user-facing traffic with no
  `models` fallback - one provider outage becomes a user-visible outage.
- Do NOT target the deprecated Edge runtime for AI routes to "save cold starts."
  Default to Fluid Compute (full Node.js, instance reuse cuts cold starts at the
  same price/regions) - that runtime choice belongs to `vercel-edge-and-isr`.
- Do NOT enable `zeroDataRetention` and then list a non-ZDR provider in `models`;
  the fallback will be silently unavailable.
- Do NOT hand-roll cost logging. The Gateway dashboard and `/v1/quotas` already
  track spend per key - set a budget instead of building a meter.

## Calculator

Self-contained Node script. Save as `gateway_cost.js` and run with
`node gateway_cost.js`. No dependencies. Edit the inputs for your model and
traffic; per-token prices come from the Gateway's own model list
(`gateway.getAvailableModels()` returns `pricing.input` / `pricing.output` per
token) - paste current numbers in. It estimates monthly spend and the per-key
budget to set, and warns if a tight budget will throttle traffic.

```javascript
// AI Gateway monthly cost + budget estimator. Edit inputs, then: node gateway_cost.js
const inputs = {
  requestsPerDay: 5000,        // expected requests/day for this key/environment
  inputTokensPerReq: 1200,     // avg prompt tokens (context + user input)
  outputTokensPerReq: 400,     // avg completion tokens
  // Per-token USD prices from gateway.getAvailableModels() -> model.pricing.*
  inputPricePerToken: 0.000003,   // e.g. $3.00 / 1M input tokens
  outputPricePerToken: 0.000015,  // e.g. $15.00 / 1M output tokens
  fallbackOverhead: 0.05,      // +5% headroom for retries/fallback attempts
  budgetRefresh: 'monthly',    // matches the API key refreshPeriod you set
}

function estimate(i) {
  const reqsPerMonth = i.requestsPerDay * 30
  const inputCost = reqsPerMonth * i.inputTokensPerReq * i.inputPricePerToken
  const outputCost = reqsPerMonth * i.outputTokensPerReq * i.outputPricePerToken
  const base = inputCost + outputCost
  const withFallback = base * (1 + i.fallbackOverhead)
  // Set the key budget a bit above expected spend so normal traffic isn't capped.
  const suggestedBudget = Math.ceil((withFallback * 1.2) / 5) * 5 // round up to $5
  const costPerThousandReqs = (withFallback / reqsPerMonth) * 1000
  return { reqsPerMonth, inputCost, outputCost, withFallback, suggestedBudget, costPerThousandReqs }
}

const r = estimate(inputs)
const usd = (n) => '$' + n.toFixed(2)
console.log('Requests / month:            ', r.reqsPerMonth.toLocaleString())
console.log('Input token cost:            ', usd(r.inputCost))
console.log('Output token cost:           ', usd(r.outputCost))
console.log('Est. monthly spend (w/ fallback):', usd(r.withFallback))
console.log('Cost per 1,000 requests:     ', usd(r.costPerThousandReqs))
console.log('Suggested key budget (' + inputs.budgetRefresh + '): ', usd(r.suggestedBudget))
console.log('Set via POST /v1/api-keys aiGatewayQuota.limitAmount =', r.suggestedBudget)
```

### Worked example output

With the inputs above the script prints:

```
Requests / month:             150,000
Input token cost:             $540.00
Output token cost:            $900.00
Est. monthly spend (w/ fallback): $1512.00
Cost per 1,000 requests:      $10.08
Suggested key budget (monthly):  $1815.00
Set via POST /v1/api-keys aiGatewayQuota.limitAmount = 1815
```

Read it: 5,000 requests/day at this model costs about $1,512/month, roughly $10
per thousand requests. Set the key's `limitAmount` to ~$1,815 (20% headroom) so
normal traffic is never capped but a runaway loop trips the budget instead of the
invoice. Re-run with the cheaper fallback's prices to see the floor if the primary
is failing over - and if `cost per 1,000 requests` is too high, the lever is the
primary model, not the Gateway.

## Template: gateway-integration-checklist

Copy this into the PR description for the AI route. Every box must be checked.

```
AI GATEWAY INTEGRATION. [FILL: route / feature]

CALL SITE
  [ ] Model passed as plain "provider/model" string via the AI SDK
  [ ] Model string read from config/env: [FILL: var name]
  [ ] NO @ai-sdk/<provider> package installed (grep package.json to confirm)
  [ ] NO raw provider keys in env (only AI_GATEWAY_API_KEY)

AUTH (provisioned by vercel-env-management)
  [ ] Local: AI_GATEWAY_API_KEY in .env.local (gitignored)
  [ ] Vercel: relies on auto-injected VERCEL_OIDC_TOKEN (no key needed)
  [ ] Fallback expressed: AI_GATEWAY_API_KEY || VERCEL_OIDC_TOKEN

RESILIENCE
  [ ] Primary model:            [FILL]
  [ ] Fallback models (order):  [FILL, FILL]
  [ ] Provider order if multi-provider: [FILL]
  [ ] Sensitive data? zeroDataRetention: true  -> all fallbacks ZDR-capable

COST
  [ ] Ran gateway_cost.js - est. monthly spend: $[FILL]
  [ ] Per-environment key budget set: $[FILL] / [daily|weekly|monthly]
  [ ] Dashboard reviewed after first day of real traffic

RUNTIME (owned by vercel-edge-and-isr)
  [ ] Route runs on Fluid Compute (Node.js), not deprecated Edge
```

## references/why-bare-strings

Provider-specific packages (`@ai-sdk/openai`, etc.) were the original AI SDK
pattern: import a provider factory, call `openai('gpt-...')`, supply that
provider's key. It works, but it hard-codes the provider at the call site and
forces a separate key and bill per provider. The Gateway inverts this: the AI SDK
treats any bare `"provider/model"` string as "resolve through the configured
gateway provider," so the call site no longer names an SDK at all. The provider
becomes data (a string, a config value), not code (an import). That is what makes
failover (`models`), provider routing (`order`), one-key auth, and unified billing
possible without touching the call. The only time you reach back for a
provider-specific package is a genuinely provider-exclusive feature the Gateway
does not surface - rare, and worth flagging explicitly when you do.

## references/provider-options-cheatsheet

All live under `providerOptions.gateway` on any AI SDK call:

```
models: ['provider/model', ...]   ordered fallback models if primary fails
order:  ['azure','openai', ...]   preferred provider order for a multi-provider model
only:   ['bedrock','anthropic']   allow-list - restrict routing to these providers
zeroDataRetention: true           route only to ZDR providers; delete after processing
byok:   { azure: [{ apiKey, ... , modelMappings }] }   bring your own provider key/deployment
```

Per-provider reasoning/thinking options nest under the provider name alongside
`gateway` (e.g. `providerOptions.anthropic.thinking`, `providerOptions.bedrock.reasoningConfig`)
so the matching config is applied to whichever provider the Gateway selects.

The OpenAI-compatible REST surface is `https://ai-gateway.vercel.sh/v1` with
`Authorization: Bearer $AI_GATEWAY_API_KEY` - handy for non-JS callers (Python,
curl, coding agents) that cannot use the AI SDK. Same routing/fallback semantics
via the same `providerOptions.gateway` body.
