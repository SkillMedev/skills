---
name: Next on Vercel Performance
description: Diagnose and fix Core Web Vitals and page performance of a Next.js app on Vercel — measurement-first. Use when an LCP/INP/CLS score is bad, a page "feels slow", Lighthouse/PageSpeed/Speed Insights is red, the JS bundle is too big, images or fonts are janky, or you want Vercel-specific caching/edge wins. Triggers: "improve Core Web Vitals", "fix LCP", "reduce INP", "stop layout shift", "my Next.js page is slow on Vercel", "shrink my bundle", "next/image optimization", "font is causing CLS", "cache this page". Do NOT use to set up the deploy pipeline or rollbacks — use vercel-deploy-pipeline; for ISR/cache-component semantics and edge runtime choice — use vercel-edge-and-isr; for env/secrets — use vercel-env-management; for model/provider routing — use vercel-ai-gateway; for WAF/bot defense — use vercel-firewall-and-botid.
---

# Next on Vercel Performance

You make a Next.js app on Vercel measurably faster — by **measuring first**, fixing
the one metric that is actually red, and verifying the number moved. Performance
work without a number in front of you is guesswork; this skill refuses to guess.

Trigger eagerly on any signal that a page is slow or a vital is failing: "fix my
LCP", "reduce INP", "stop the layout shift", "Lighthouse is red", "Speed Insights
is bad", "my bundle is huge", "the page feels janky on mobile", "make this page
faster on Vercel". Each Core Web Vital has a *different* root cause and a
*different* fix — never apply an INP fix to an LCP problem. This skill is one stop
in the ship-a-Next.js-app-on-Vercel workflow: it assumes the app already deploys
(vercel-deploy-pipeline) and that ISR / runtime choices live in
vercel-edge-and-isr; here we tune what those produce.

Platform facts this skill assumes (current as of 2026): Node.js 24 is the default
runtime and Fluid Compute is the default compute model (full Node.js, instance
reuse cuts cold starts) — Edge Functions are no longer recommended, so do NOT
reach for the edge runtime as a performance lever by default. Reserve runtime
decisions for vercel-edge-and-isr.

## Step 1 — Get a real measurement before touching code

Do not open a component until you have a number. There are two measurement worlds
and you need both:

- **Field data (real users, what Google ranks on):** turn on **Vercel Speed
  Insights** (`@vercel/speed-insights`) to collect LCP / INP / CLS / TTFB / FCP from
  production traffic. In CI or a script, pull the field numbers with the CLI:

```bash
# requires Speed Insights enabled + Observability Plus on the project
vercel metrics
```

- **Lab data (reproducible, one page):** run Lighthouse against a **production**
  deployment (never `next dev` — dev is unoptimized and lies about every number):

```bash
npm i -g lighthouse
lighthouse https://your-app.vercel.app/the-slow-page \
  --only-categories=performance --form-factor=mobile --output=json \
  --output-path=./lh.json --quiet
```

Write down the failing metric and its value (e.g. "mobile LCP 4.8s, target ≤2.5s").
That number is your acceptance test. Field and lab disagree often — **trust field
data for what to fix, use lab data to iterate quickly.**

The 2026 thresholds (mobile, "good"):

| Metric | Good | Needs work | Poor | What it measures |
|---|---|---|---|---|
| **LCP** | ≤ 2.5s | ≤ 4.0s | > 4.0s | Time to render the largest element (loading) |
| **INP** | ≤ 200ms | ≤ 500ms | > 500ms | Slowest interaction→paint (responsiveness) |
| **CLS** | ≤ 0.1 | ≤ 0.25 | > 0.25 | Unexpected layout movement (visual stability) |

## Step 2 — Route the failing metric to its root cause

Each vital has one dominant cause family. Diagnose, do not shotgun.

- **LCP slow →** the largest element (usually a hero image or a heading blocked by
  a font/by data). Causes: unoptimized hero image, slow TTFB (uncached dynamic
  render), render-blocking JS/CSS, font swap delaying text. Go to Step 3 (images),
  Step 5 (fonts), Step 6 (caching/TTFB).
- **INP high →** main-thread blocked during interaction by too much JavaScript:
  oversized client bundles, heavy hydration, expensive event handlers. Go to
  Step 4 (bundle) — this is almost always a "ship less JS" problem.
- **CLS high →** elements without reserved space: images/embeds/ads without
  dimensions, fonts swapping metrics, content injected above the fold. Go to
  Step 3 (image `width`/`height`) and Step 5 (`next/font` + `size-adjust`).

Run `vitals_triage.js` (below) with your numbers to get the ordered fix list.

## Step 3 — Images: the #1 LCP and CLS lever

Most LCP and CLS wins are one image. Use `next/image`, which on Vercel runs the
native Image Optimization API (on-demand resize, AVIF/WebP, lazy-load, cached at
the edge):

```tsx
import Image from 'next/image'

// Above-the-fold hero (the LCP element): priority loads it eagerly, no lazy delay.
<Image
  src="/hero.jpg"
  alt="Product hero"
  width={1280}
  height={720}     // width+height reserve space → zero layout shift (CLS)
  priority         // ONLY on the LCP image — preloads, skips lazy-loading
  sizes="(max-width: 768px) 100vw, 1280px"  // stops over-large downloads on mobile
  placeholder="blur"
/>
```

Rules that actually move the number:
- **`priority` on the LCP image only.** It preloads. Putting it on everything
  re-creates the problem by preloading offscreen images.
- **Always pass `width` and `height`** (or `fill` + a sized parent). This is the
  single biggest CLS fix — the browser reserves the box before the bytes arrive.
- **Set `sizes`** so mobile fetches a small variant, not the 1280px desktop file.
- Prefer **AVIF then WebP**. Configure formats in `vercel.ts` (or `vercel.json`):

```typescript
// vercel.ts — recommended config (import from '@vercel/config'); vercel.json still works
import type { VercelConfig } from '@vercel/config/v1'
export const config: VercelConfig = {
  images: {
    formats: ['image/avif', 'image/webp'],
    minimumCacheTTL: 31536000, // cache optimized variants 1 year at the edge
    qualities: [50, 75],
  },
}
```

For remote images add `remotePatterns`. Store user uploads in **Vercel Blob**
(public or private) rather than bundling them. Note: Vercel Postgres/KV are
retired — use Marketplace storage (Neon Postgres, Upstash Redis) for data; this
matters for LCP only insofar as a slow DB query delays TTFB (Step 6).

## Step 4 — Bundle size: the #1 INP lever

INP is a JavaScript problem. Less client JS = a freer main thread = faster
interactions. Measure before cutting:

```bash
# See exactly which routes ship how much First Load JS
ANALYZE=true npm run build         # with @next/bundle-analyzer wired in next.config
# or just read the build output table — the "First Load JS" column per route
npm run build
```

The senior moves, in order of payoff:
1. **Default to Server Components.** Anything that does not need state, effects, or
   browser APIs should NOT have `'use client'`. The biggest bundles are almost
   always an over-eager `'use client'` at the top of a tree dragging children to
   the client. Push the directive **down** to the smallest interactive leaf.
2. **`next/dynamic` for heavy, below-the-fold, or interaction-gated client code**
   (charts, editors, modals, maps):

```tsx
import dynamic from 'next/dynamic'
const Chart = dynamic(() => import('./Chart'), { ssr: false, loading: () => <Skeleton /> })
```

3. **Kill heavy dependencies.** A moment library, a full icon set imported as a
   namespace, or a 100KB date util is often the whole regression. Import single
   members, swap for lighter libs, or move the work server-side.
4. **Server-render data; do not ship a client fetch + spinner** for content that
   could be in the initial HTML — that also helps LCP.

Re-run the build and confirm First Load JS for the route dropped. That delta is
your evidence.

## Step 5 — Fonts: a quiet LCP + CLS tax

Web fonts cause CLS (swap reflow) and delay LCP (text waits on the font). Use
`next/font` — it self-hosts the font at build time (no third-party request), and
auto-generates a fallback with `size-adjust` so the swap does not shift layout:

```tsx
// app/layout.tsx
import { Inter } from 'next/font/local' // or next/font/google
const inter = Inter({ subsets: ['latin'], display: 'swap' })
export default function RootLayout({ children }: { children: React.ReactNode }) {
  return <html lang="en" className={inter.className}>{/* ... */}</html>
}
```

- Subset to the scripts you actually use; never ship the full multilingual file.
- Self-hosting via `next/font` removes the render-blocking call to fonts.googleapis
  and the FOUT shift — both an LCP and a CLS win in one change.

## Step 6 — Vercel caching: cut TTFB so LCP starts sooner

A slow LCP often starts with a slow TTFB — the function re-renders on every
request. Serve from cache and the byte-zero arrives in milliseconds. Three layers,
cheapest first:

1. **Static / ISR first.** If the page can be prerendered, it should be. ISR works
   on Next.js, SvelteKit, Nuxt, and Astro. On **Next.js 16 Cache Components**, mark
   cacheable work with **`'use cache'`** and tune lifetime/invalidation with
   `cacheLife`, `cacheTag`, and `updateTag` (these replace `unstable_cache`). The
   semantics live in **vercel-edge-and-isr** — defer the depth there; here you only
   confirm the page is not needlessly dynamic.

2. **CDN cache for dynamic responses** via the three-tier header — `Cache-Control`
   for the browser, `CDN-Cache-Control` for the Vercel edge, `Vercel-CDN-Cache-Control`
   to override both at Vercel only:

```ts
// app/api/products/route.ts — fresh-feeling but edge-cached
export async function GET() {
  const data = await getProducts()
  return Response.json(data, {
    headers: {
      // browser: don't cache; edge: serve cached for 60s, revalidate in background
      'Cache-Control': 'public, max-age=0, must-revalidate',
      'Vercel-CDN-Cache-Control': 's-maxage=60, stale-while-revalidate=300',
    },
  })
}
```

   `stale-while-revalidate` is the key to fast TTFB: users get an instant cached
   byte while Vercel refreshes in the background.

3. **Keep functions warm and lean.** Fluid Compute already reuses instances to cut
   cold starts; help it by keeping the slow dependency (DB, AI call) off the
   critical render path or behind the cache above. If a page calls an LLM, route it
   through **vercel-ai-gateway** (GA, Aug 2025) so a slow provider can fail over
   instead of stalling TTFB — use plain `provider/model` strings via the AI SDK, not
   provider-specific packages.

## Quality bar

A performance change is done only when all hold:

- You captured a **before** number (field via Speed Insights / `vercel metrics`, or
  lab via Lighthouse on a **production** deploy) and an **after** number, and the
  after is better. No number, not done.
- You fixed the metric that was actually red, routed through its real root cause —
  not a generic "make it faster" pass.
- The LCP image (and only it) has `priority`; every `next/image` has `width`/`height`
  or a sized `fill` parent; `sizes` is set for responsive images.
- Fonts go through `next/font` (self-hosted, subsetted, `display: 'swap'`).
- For any INP work, the route's **First Load JS dropped** in the build output and
  `'use client'` sits at the smallest leaf that needs it.
- Cacheable pages are static/ISR; genuinely dynamic ones set an explicit
  `Vercel-CDN-Cache-Control` with `stale-while-revalidate`.

## Do NOT

- Do NOT measure or benchmark against `next dev`. Dev disables minification,
  caching, and optimization — always test a production build / deployment.
- Do NOT optimize without a profile. "It feels slow" is not a target; an LCP number
  is. If you have no measurement, go back to Step 1.
- Do NOT put `priority` on more than the LCP image, and never use a raw `<img>` for
  a vital image when `next/image` is available.
- Do NOT ship images without `width`/`height` — that is the most common CLS cause.
- Do NOT reach for the **edge runtime** as a speed fix. Edge Functions are
  deprecated; default to Fluid Compute (Node.js 24) and let vercel-edge-and-isr own
  any runtime decision.
- Do NOT cache-bust by disabling caching to "be safe" — a dynamic render on every
  request is usually the TTFB cause you are chasing.
- Do NOT bolt on provider-specific AI SDKs for a slow model call — route through
  vercel-ai-gateway for fallbacks and observability.
- Do NOT confuse the vitals: an INP fix (less JS) will not move LCP, and an LCP fix
  (image/cache) will not move INP. Diagnose first.

## Calculator

Self-contained Node script. Save as `vitals_triage.js` and run with
`node vitals_triage.js`. Edit the inputs with your measured numbers. No
dependencies. It classifies each vital against the 2026 thresholds and prints an
ordered fix list pointing at the right step.

```javascript
// Core Web Vitals triage. Edit the measured numbers, then: node vitals_triage.js
const measured = {
  lcpSeconds: 4.8,   // Largest Contentful Paint, seconds (mobile field/lab)
  inpMs: 320,        // Interaction to Next Paint, milliseconds
  cls: 0.21,         // Cumulative Layout Shift, unitless
}

// 2026 "good" / "poor" thresholds (mobile).
const T = {
  lcp: { good: 2.5, poor: 4.0, unit: 's' },
  inp: { good: 200, poor: 500, unit: 'ms' },
  cls: { good: 0.1, poor: 0.25, unit: '' },
}

const fixes = {
  lcp: 'LCP: optimize the hero image (Step 3: next/image + priority + sizes), self-host fonts (Step 5), cut TTFB with cache/ISR (Step 6).',
  inp: 'INP: ship less client JS (Step 4: Server Components by default, push "use client" to leaves, next/dynamic heavy code, drop heavy deps).',
  cls: 'CLS: reserve space — width/height on every image (Step 3), next/font with size-adjust fallback (Step 5), no above-fold injection.',
}

function classify(value, t) {
  if (value <= t.good) return 'GOOD'
  if (value <= t.poor) return 'NEEDS WORK'
  return 'POOR'
}

// Severity: how far past "good", normalized, so the worst vital is fixed first.
function severity(value, t) {
  if (value <= t.good) return 0
  return (value - t.good) / (t.poor - t.good)
}

const rows = [
  ['lcp', measured.lcpSeconds, T.lcp],
  ['inp', measured.inpMs, T.inp],
  ['cls', measured.cls, T.cls],
].map(([key, value, t]) => ({
  key,
  value,
  unit: t.unit,
  status: classify(value, t),
  severity: severity(value, t),
}))

console.log('Core Web Vitals triage (mobile thresholds, 2026):')
for (const r of rows) {
  console.log(
    '  ' + r.key.toUpperCase().padEnd(4),
    String(r.value + r.unit).padEnd(8),
    r.status,
  )
}

const failing = rows
  .filter((r) => r.status !== 'GOOD')
  .sort((a, b) => b.severity - a.severity)

console.log('')
if (failing.length === 0) {
  console.log('All vitals are GOOD. Verify with field data before declaring done.')
} else {
  console.log('Fix in this order (worst first):')
  failing.forEach((r, i) => console.log('  ' + (i + 1) + '. ' + fixes[r.key]))
}
```

### Worked example output

With the numbers above the script prints:

```
Core Web Vitals triage (mobile thresholds, 2026):
  LCP  4.8s     POOR
  INP  320ms    NEEDS WORK
  CLS  0.21     NEEDS WORK
Fix in this order (worst first):
  1. LCP: optimize the hero image (Step 3: next/image + priority + sizes), self-host fonts (Step 5), cut TTFB with cache/ISR (Step 6).
  2. CLS: reserve space — width/height on every image (Step 3), next/font with size-adjust fallback (Step 5), no above-fold injection.
  3. INP: ship less client JS (Step 4: Server Components by default, push "use client" to leaves, next/dynamic heavy code, drop heavy deps).
```

Read it: LCP is POOR and the furthest past "good", so the hero image and TTFB come
first; CLS and INP are "needs work" and follow by severity. The script turns three
raw numbers into an ordered, root-caused plan — and after each fix you re-measure
(Step 1) and re-run it to confirm the status flipped to GOOD.

## Template: perf-audit-record

Copy this, fill the BEFORE column from Step 1, do the work, fill AFTER, and keep it
in the PR description so the win is auditable.

```
NEXT-ON-VERCEL PERF AUDIT. [FILL: route / page]. [FILL: date]

MEASUREMENT SOURCE
  Field (Speed Insights / vercel metrics):  [FILL: yes/no]
  Lab (Lighthouse, production deploy):      [FILL: yes/no]

VITAL        BEFORE        TARGET     AFTER       FIX APPLIED (step)
  LCP        [FILL]s       <= 2.5s    [FILL]s     [FILL: e.g. priority hero + sizes (3)]
  INP        [FILL]ms      <= 200ms   [FILL]ms    [FILL: e.g. dynamic-import chart (4)]
  CLS        [FILL]        <= 0.1     [FILL]      [FILL: e.g. width/height + next/font (5)]
  TTFB       [FILL]ms      [target]   [FILL]ms    [FILL: e.g. SWR CDN cache (6)]

BUNDLE (if INP touched)
  First Load JS for route, before:  [FILL] KB
  First Load JS for route, after:   [FILL] KB

VERDICT: [FILL: all targets met / remaining red metric + next step]
```
