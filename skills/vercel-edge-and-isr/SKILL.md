---
name: Vercel Rendering and Caching
description: Choose a rendering, runtime, and caching strategy on Vercel - Fluid Compute as the default runtime (Edge Functions are deprecated), ISR/PPR, Next.js 16 Cache Components ('use cache', cacheLife, cacheTag, updateTag, revalidateTag), and the Vercel Runtime Cache API (getCache, expireTag, invalidateByTag) with tag-based invalidation. Use when someone asks "should this page be static or server rendered on Vercel", "how do I set up ISR in Next.js", "revalidate this page on demand", or "why is my Vercel page serving stale content" - including streaming vs PPR decisions, caching an API response across regions, and migrating off unstable_cache. Do NOT use to set env vars (use vercel-env-management), wire git/preview/promote deploys (use vercel-deploy-pipeline), route LLM calls (use vercel-ai-gateway), or block bots/rate-limit (use vercel-firewall-and-botid). For pure Core-Web-Vitals/bundle tuning, hand to next-on-vercel-perf.
---

# Vercel Rendering and Caching

This is the rendering-and-caching stage of shipping a Next.js app on Vercel. By the
time you are here the project deploys (vercel-deploy-pipeline) and its env vars are
set (vercel-env-management); now you decide, per route, **where it runs**, **how it
renders**, and **how long its output lives** - then you make invalidation
deliberate instead of accidental. Wrong defaults here are the difference between a
site that serves from cache in single-digit milliseconds and one that re-runs a
function on every request and still serves stale data.

The single most common mistake is carrying forward advice from the platform's
earlier era. Two defaults flipped: **Edge Functions are deprecated** - Fluid
Compute (full Node.js) is the default runtime - and **`unstable_cache` is
replaced** by Next.js 16 Cache Components. This skill encodes the platform
defaults; correct any older instinct against it.

## Workflow

Run these in order. Pick the runtime before the render mode, and the render mode
before reaching for the imperative cache - most routes never need step 4.

### Step 1 - Pick the runtime: Fluid Compute (Node.js), not Edge

Default every function and your middleware to **Fluid Compute on Node.js**. This
is the platform default and Edge Functions are deprecated - do not reach for
`export const runtime = 'edge'`.

What Fluid Compute gives you, and why the old Edge tradeoff is gone:

- **Full Node.js** (the default is Node.js 24 LTS; 18 is deprecated). The whole
  npm ecosystem, native modules, and full Web APIs - no Edge runtime subset to
  fight.
- **Reduced cold starts via instance reuse.** A warm instance serves multiple
  invocations and overlaps I/O-bound work, so the cold-start penalty that once
  pushed latency-sensitive code to Edge is largely gone.
- **Same regions, same price band.** You are not paying a premium to stay on
  Node.js. Billing is **Active CPU time + provisioned memory + invocations** - you
  are billed for CPU actually burned, not wall-clock GB-seconds, so an instance
  parked on `await` is cheap.
- **Default function timeout is 300s on all plans.** Long-running and streaming
  work fits without plan gymnastics.
- **Middleware runs full Node.js**, so interception logic can use real Node APIs.

Enable it in `vercel.ts` (the recommended config surface; `vercel.json` still works):

```typescript
// vercel.ts - import from '@vercel/config'
import type { VercelConfig } from '@vercel/config/v1';

export const config: VercelConfig = {
  fluid: true,
};
```

Reach for the Edge runtime only for a genuinely global, ultra-thin
redirect/rewrite that must run in every region with no Node dependency - and even
then, confirm it against current docs first, because it is on the deprecation path.
For request interception (rewrites, redirects, personalization) keep it in
Node.js middleware. Picking a runtime to dodge a *cold function on the request path*
is the wrong fix - the answer there is usually to cache the response (steps 2-4) so
the function does not run at all.

### Step 2 - Pick the render mode: static, ISR, streaming, or PPR

Decide per route. Default to the most static option the data allows, and add
dynamism only where the content actually demands it.

- **Fully static (prerender)** - content known at build time, identical for every
  visitor (marketing, docs). Served from the Edge Network; the function never runs
  on a request. Cheapest and fastest.
- **ISR (Incremental Static Regeneration)** - static output that **revalidates on a
  schedule or on demand**. The right default for content that changes but not
  per-request: blogs, product pages, catalogs. Works on Next.js, SvelteKit, Nuxt,
  and Astro. The first request after the window serves stale and regenerates in the
  background (stale-while-revalidate), so users never wait on a rebuild. Pick the
  window from editorial expectations, not superstition: content that must reflect
  edits within a minute gets `revalidate: 60` plus tag-based invalidation; a
  product catalog updated a few times a day is fine at 3600; near-static pages can
  take 86400. Anything below ~10s means the function runs nearly per-request -
  at that point be honest and go dynamic (or PPR).
- **Streaming** - dynamic, but flush the shell immediately and stream slow parts in
  with `<Suspense>`. Use when the page is personalized or live but you still want a
  fast first paint. Fluid Compute's long timeout and instance reuse make streaming
  cheap.
- **PPR (Partial Prerendering)** - the best of both: a **prerendered static shell**
  served instantly from the edge, with **dynamic holes streamed in** per request.
  Reach for it when one page is mostly static but has a few personalized regions (a
  product page with a static body and a live "recommended for you" rail). In Next.js
  16 PPR is part of **Cache Components** - what is *not* wrapped in `'use cache'` is
  the dynamic hole.

Next.js ISR by interval (App Router):

```typescript
// Revalidate this route's data every 60s; first request after expiry
// serves stale and regenerates in the background.
export default async function Page() {
  const res = await fetch('https://api.example.com/posts', {
    next: { revalidate: 60, tags: ['posts'] },
  });
  return <Posts data={await res.json()} />;
}
```

ISR on the other frameworks is config-driven via `routeRules` (Nuxt/Nitro) or the
adapter `isr` option (SvelteKit) - `isr: 60` for an interval, `isr: true` for cache
forever, `isr: false` for always-fresh. Verify the exact key against the framework
doc; the *concept* (interval, on-demand, forever) is identical across all four.

### Step 3 - Cache Components in Next.js 16: `'use cache'` and tags

For App Router on Next.js 16, **Cache Components replace `unstable_cache`**. If you
still have `unstable_cache`, migrating off it is the task - see the Do NOT section.

Turn it on in config, then cache at the function or component boundary with the
`'use cache'` directive (use `'use cache: remote'` for the Vercel Runtime Cache so
the entry is shared across regions and invocations):

```typescript
// next.config.ts
import type { NextConfig } from 'next';
const nextConfig: NextConfig = { cacheComponents: true };
export default nextConfig;
```

```typescript
import { cacheLife, cacheTag } from 'next/cache';

export default async function Page() {
  return <Products data={await getProducts()} />;
}

async function getProducts() {
  'use cache: remote';
  cacheTag('products');       // attach a tag for on-demand invalidation
  cacheLife({ expire: 3600 }); // and/or a time budget (1 hour)

  const res = await fetch('https://api.example.com/products');
  return res.json();
}
```

The four primitives, and when each fires:

- **`'use cache'` / `'use cache: remote'`** - marks a function or component cacheable.
  Plain `'use cache'` caches at the appropriate layer; `: remote` targets the
  cross-region Runtime Cache. Anything *not* wrapped becomes a dynamic PPR hole.
- **`cacheLife(...)`** - the time budget (e.g. `{ expire: 3600 }`), the ISR interval
  expressed at the cache boundary.
- **`cacheTag('products')`** - labels the entry so you can invalidate by tag later.
  Tag generously; tags are how you avoid time-based-only invalidation.
- **Invalidation from a Server Action / route handler:**
  - **`revalidateTag('products')`** - marks the tag stale; the next request
    regenerates in the background (stale-while-revalidate). The safe default.
  - **`updateTag('products')`** - the Next.js 16 *immediate, read-your-writes*
    refresh: use it inside a mutating Server Action when the user must see their own
    change reflected synchronously (e.g. after editing their profile). It replaces
    the old pattern of revalidating and hoping the next paint is fresh.

Wire invalidation to the mutation that causes it - call the tag function in the
same Server Action that writes the data. A page that reads `cacheTag('products')`
and a `createProduct` action that calls `revalidateTag('products')` is a complete,
correct loop. Time-based `cacheLife` is the *fallback* for data you cannot tag.

### Step 4 - The Vercel Runtime Cache API for non-framework caching

When you need an explicit key/value cache **outside** the framework's render path -
in a plain Function, in Routing Middleware, or to share a computed value across
Functions and Builds - use the Runtime Cache via `@vercel/functions`. It is a
per-region cache with **global tag-based invalidation**.

```typescript
import { getCache } from '@vercel/functions';

export default {
  async fetch(request: Request) {
    const cache = getCache();

    const hit = await cache.get('blog:list');
    if (hit) return Response.json(hit);

    const data = await (await fetch('https://api.example.com/blog')).json();
    await cache.set('blog:list', data, { ttl: 3600, tags: ['blog'] });
    return Response.json(data);
  },
};
```

Invalidate by tag from anywhere (a webhook handler, a Server Action). Two shapes,
different semantics:

```typescript
import { getCache, invalidateByTag } from '@vercel/functions';

// Hard expire: drop entries for these tags now. Propagates globally.
await getCache().expireTag('blog');

// Soft invalidate: mark stale; entries revalidate in the background on next read.
await invalidateByTag('blog');
```

Use `expireTag` when stale data is unacceptable (pricing, inventory) and
`invalidateByTag` when a brief background-refresh window is fine (it keeps latency
flat under load). Reach for this layer only when the framework cache (step 3) does
not reach your code - most app rendering should stay in `'use cache'`.

### Step 5 - Decide and document

For each route, lock the three choices and write them down so the next engineer
inherits the intent, not a guess. Run `cache_plan.js` (below) to turn the routes
into a table, then paste it into the rendering-plan template.

## Quality bar

A rendering-and-caching plan is done only when all hold:

- **No `runtime = 'edge'`** unless a specific route is justified in writing against
  current docs; everything else is Fluid Compute / Node.js, and middleware is Node.js.
- **No `unstable_cache`** anywhere - all App Router caching is `'use cache'` /
  `cacheLife` / `cacheTag`.
- **Every cached read has a tag**, and **every mutation that changes that data calls
  the matching `revalidateTag`/`updateTag`** (or `expireTag`/`invalidateByTag` for the
  Runtime Cache). No write path leaves a tag stale forever.
- **`updateTag` is used where the user must see their own write immediately**;
  `revalidateTag` everywhere else. The choice is deliberate, not coincidental.
- Each route's render mode is the **most static the data allows** - dynamic only
  where content is genuinely per-request.
- No fabricated latency or cost numbers in the plan; mark anything unmeasured as an
  assumption and verify under load.

## Do NOT

- **Do NOT default to `export const runtime = 'edge'`.** Edge Functions are
  deprecated; Fluid Compute on Node.js is the default and removes the cold-start
  reason people fled to Edge.
- **Do NOT target Node.js 18** or assume a short timeout. The default runtime is
  Node.js 24 LTS and the default function timeout is 300s on all plans.
- **Do NOT reach for `unstable_cache` or a hand-rolled module-level Map.** Use Cache
  Components (`'use cache'`); a per-instance Map is not shared across Fluid
  instances or regions and silently serves inconsistent data.
- **Do NOT cache by time alone when the data has a clear mutation point.** Tag it
  and invalidate on write; `cacheLife` is the fallback for untaggable data, not the
  primary strategy.
- **Do NOT use `revalidateTag` when the acting user must see their own change on the
  next paint** - that is exactly what `updateTag` is for. Conversely, do not reach
  for `updateTag` on a background webhook where stale-while-revalidate is fine.
- **Do NOT pick a runtime to mask a slow function on the request path.** Cache the
  response so the function does not run, rather than running the same slow work
  closer to the user.
- **Do NOT model storage on retired primitives.** Vercel Postgres/KV are retired -
  use Marketplace storage (Neon Postgres, Upstash Redis) plus Vercel Blob and Edge
  Config. The Runtime Cache is a cache, not a database; never treat it as the source
  of truth.

## Calculator

Self-contained Node script - no dependencies. Save as `cache_plan.js`, list your
routes in the `routes` block, and run `node cache_plan.js`. It recommends a render
mode and the cache wiring per route and flags any route still set to a deprecated
runtime.

```javascript
// Vercel rendering & caching planner. Edit routes, then: node cache_plan.js
const routes = [
  // perVisitor: does output differ per user?  mutates: is there a clear write event?
  // freshnessSeconds: how stale may it be? (0 = must be live)  runtime: 'node' | 'edge'
  { path: '/',            perVisitor: false, mutates: false, freshnessSeconds: 86400, runtime: 'node' },
  { path: '/blog/[slug]', perVisitor: false, mutates: true,  freshnessSeconds: 3600,  runtime: 'node' },
  { path: '/product/[id]',perVisitor: true,  mutates: true,  freshnessSeconds: 300,   runtime: 'node' },
  { path: '/dashboard',   perVisitor: true,  mutates: true,  freshnessSeconds: 0,     runtime: 'node' },
  { path: '/edge-redirect',perVisitor: false,mutates: false, freshnessSeconds: 86400, runtime: 'edge' },
];

function plan(r) {
  let mode, cache;
  if (r.freshnessSeconds === 0 && r.perVisitor) {
    mode = 'Dynamic + streaming (<Suspense>)';
    cache = "no 'use cache' on the live region";
  } else if (r.perVisitor) {
    mode = 'PPR (static shell + dynamic hole)';
    cache = "'use cache' the shell; stream the per-user hole";
  } else if (r.freshnessSeconds >= 86400 && !r.mutates) {
    mode = 'Fully static (prerender)';
    cache = 'no revalidation needed';
  } else {
    mode = 'ISR / Cache Components';
    cache = `'use cache'; cacheLife({ expire: ${r.freshnessSeconds} })`;
  }
  const tag = r.path.replace(/[^a-z0-9]+/gi, '-').replace(/^-|-$/g, '').toLowerCase() || 'root';
  const invalidate = r.mutates
    ? `cacheTag('${tag}') + ${r.freshnessSeconds === 0 ? 'updateTag' : 'revalidateTag'}('${tag}') on write`
    : 'time-based only';
  const flag = r.runtime === 'edge'
    ? 'FLAG: edge runtime is deprecated - move to Fluid Compute (node)'
    : 'ok (Fluid Compute / Node.js)';
  return { mode, cache, invalidate, flag };
}

for (const r of routes) {
  const p = plan(r);
  console.log(r.path);
  console.log('  render:    ', p.mode);
  console.log('  cache:     ', p.cache);
  console.log('  invalidate:', p.invalidate);
  console.log('  runtime:   ', p.flag);
  console.log('');
}
```

### Worked example output

With the routes above the script prints:

```
/
  render:     Fully static (prerender)
  cache:      no revalidation needed
  invalidate: time-based only
  runtime:    ok (Fluid Compute / Node.js)

/blog/[slug]
  render:     ISR / Cache Components
  cache:      'use cache'; cacheLife({ expire: 3600 })
  invalidate: cacheTag('blog-slug') + revalidateTag('blog-slug') on write
  runtime:    ok (Fluid Compute / Node.js)

/product/[id]
  render:     PPR (static shell + dynamic hole)
  cache:      'use cache' the shell; stream the per-user hole
  invalidate: cacheTag('product-id') + revalidateTag('product-id') on write
  runtime:    ok (Fluid Compute / Node.js)

/dashboard
  render:     Dynamic + streaming (<Suspense>)
  cache:      no 'use cache' on the live region
  invalidate: cacheTag('dashboard') + updateTag('dashboard') on write
  runtime:    ok (Fluid Compute / Node.js)

/edge-redirect
  render:     Fully static (prerender)
  cache:      no revalidation needed
  invalidate: time-based only
  runtime:    FLAG: edge runtime is deprecated - move to Fluid Compute (node)
```

Read it: the marketing home is fully static, the blog is plain ISR with on-demand
`revalidateTag` on publish, the product page is PPR (static body, streamed
per-user rail), and the dashboard is live-streamed with `updateTag` so a user's own
edit shows immediately. The `/edge-redirect` route is flagged because it still
targets the deprecated Edge runtime - move it to Fluid Compute. Change the inputs
to match your routes; the point is to make every route's three choices explicit.

## Template: rendering-plan

Copy this, run `cache_plan.js`, and paste its rows in. One row per route - this is
the artifact the next engineer reads to understand why each route renders the way
it does.

```
VERCEL RENDERING & CACHING PLAN. [FILL: project]. [FILL: month/year]

GLOBAL
  Runtime default:        Fluid Compute / Node.js 24 LTS  (edge = deprecated)
  Middleware runtime:     Node.js
  Cache Components on:     [FILL: yes/no]  (cacheComponents: true in next.config)
  Storage:                 [FILL: Neon / Upstash / Blob / Edge Config]

PER ROUTE
  ROUTE             RENDER MODE                 CACHE WIRING                 INVALIDATE ON
  [FILL]            [static/ISR/PPR/stream]      [FILL: use cache + cacheLife] [FILL: which write -> tag fn]
  [FILL]            [FILL]                       [FILL]                        [FILL]
  (add rows until every route is listed)

RUNTIME CACHE (only if used outside the framework path)
  Keys/tags:               [FILL]
  Invalidation:            [FILL: expireTag (hard) / invalidateByTag (soft)] from [FILL: which handler]

DEFERRALS
  Env vars:                vercel-env-management
  Deploy/promote:          vercel-deploy-pipeline
  Core Web Vitals/bundle:  next-on-vercel-perf
```

## references/runtime-and-render

The two decisions are independent but both default toward *less work on the request
path*. Runtime is *where code runs when it runs*; render mode is *whether code runs
at all per request*. The runtime default is Fluid Compute on Node.js - full Node,
instance reuse to cut cold starts, same regions and price band, Active-CPU
billing, and a 300s default timeout - and Edge Functions are deprecated. So the
render mode carries the performance weight: a fully static or ISR route serves from
the Edge Network and the function never executes per request, which beats any
runtime choice. Climb the static ladder first (static -> ISR -> PPR -> streaming ->
fully dynamic) and only descend as far as the data forces you. PPR is the lever
that lets one page be mostly static while still showing per-user content: a
prerendered shell from the edge with dynamic holes streamed in, where the holes are
precisely the parts not wrapped in `'use cache'`.

## references/invalidation

Caching is easy; *invalidation* is where teams ship stale data. The rule: every
cached read carries a tag, and every write that changes that data calls the
matching invalidation in the same code path. Tag taxonomy beats time budgets -
`cacheLife` is the fallback for data with no clear mutation point, not the primary
strategy.

Pick the invalidation verb by who needs freshness and how fast:

- **`revalidateTag(tag)`** (Cache Components) - mark stale; next request regenerates
  in the background. Stale-while-revalidate. The default for content other people
  edit (a published post, an updated price seen by all visitors).
- **`updateTag(tag)`** (Next.js 16) - immediate, read-your-writes refresh inside a
  mutating Server Action. Use it when the acting user must see *their own* change on
  the very next render (profile edit, settings save). Without it you get the classic
  "I saved but it still shows the old value" bug.
- **`expireTag(tag)`** (Runtime Cache, `@vercel/functions`) - hard global expiry
  within ~300ms across regions. Use when stale is unacceptable (inventory, pricing).
- **`invalidateByTag(tag)`** (Runtime Cache) - soft: mark stale, revalidate in the
  background on next read. Keeps latency flat under load when a brief stale window
  is acceptable.

If you are migrating from `unstable_cache`: replace the wrapper with a `'use cache'`
function, move its `tags` to `cacheTag(...)`, move its `revalidate` to
`cacheLife({ expire })`, and keep calling `revalidateTag` from the mutation - the
invalidation call site does not change, only the caching declaration does.
