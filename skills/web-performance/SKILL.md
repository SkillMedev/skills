---
name: Web Performance
description: Diagnoses and fixes Core Web Vitals - LCP, INP, and CLS - through an ordered audit procedure with concrete code-level changes for images, fonts, JavaScript, and third-party scripts. Use when someone asks "why is my page slow", "how do I fix my LCP", "we're failing Core Web Vitals", "improve my Lighthouse score", or wants a frontend performance audit before a launch or SEO push. Do NOT use for testing server capacity under concurrent traffic - use load-testing instead; for native mobile app performance, use mobile-perf-profiler; for Next.js-on-Vercel-specific tuning, use next-on-vercel-perf.
---

# Web Performance

Pages get optimized for the developer's laptop and fail for the median user on a mid-tier phone over 4G - that is the costly mistake this skill prevents. The outcome is passing Core Web Vitals at the 75th percentile of real users: LCP under 2.5s, INP under 200ms, CLS under 0.1. Everything below is ordered so measurement decides where effort goes, because intuition about performance is wrong more often than it is right.

## Operating procedure

Run the audit in this order. Fixing before measuring wastes effort on metrics that already pass; measuring in the lab before checking field data optimizes for a synthetic user who does not exist.

### Step 1: Gather inputs

- The URL(s) or page templates that matter commercially (home, landing, product, checkout). Default: the highest-traffic template.
- Field data access: CrUX (public per-origin) or in-house RUM. If neither exists, note that lab data is a proxy and label conclusions accordingly.
- Framework and rendering mode (SSR, SSG, SPA, islands), CDN in use, and the third-party tag list.
- The audience's device and network profile. Default when unknown: mid-tier Android over 4G - never a desktop on fiber.

### Step 2: Read field data first

Use field data (CrUX, RUM) for what users actually experience; lab data (Lighthouse, WebPageTest) only to debug. Read LCP, INP, and CLS at the 75th percentile. Passing bands: LCP under 2.5s, INP under 200ms, CLS under 0.1. Needs-improvement bands: LCP 2.5-4s, INP 200-500ms, CLS 0.1-0.25; anything beyond those is failing. The failing metric - not the loudest complaint - selects which playbook below to run.

### Step 3: Reproduce in the lab under throttling

Profile on a throttled mid-tier phone profile and 4G - not the development laptop. Identify the LCP element, the longest main-thread tasks, and the layout-shift sources before changing anything.

### Step 4: Run the playbook for the failing metric

Fix the worst-failing metric first, one change at a time.

LCP (target under 2.5s):
- Identify the LCP element (usually the hero image or headline).
- Preload it: link rel="preload" as="image" with fetchpriority="high".
- Serve AVIF/WebP, correctly sized via srcset and sizes.
- Eliminate render-blocking CSS/JS; inline critical CSS, defer the rest.
- Use a CDN and long cache headers; reduce TTFB with server caching - TTFB above roughly 800ms makes 2.5s LCP nearly unreachable.

CLS (target under 0.1):
- Set explicit width/height (or aspect-ratio) on images, videos, and iframes.
- Reserve space for ads, embeds, and late-loading banners.
- Load web fonts with font-display: swap and preload the primary font.
- Never insert content above existing content after load.

INP (target under 200ms):
- Break long tasks (over 50ms) with scheduler.yield() or chunking.
- Defer non-critical JS; remove unused third-party scripts.
- Debounce expensive handlers; move heavy work to a Web Worker.
- Use CSS for animation instead of JS where possible.

### Step 5: Apply the standing rules

- Ship less JavaScript - it is the dominant cost. Code-split per route.
- Lazy-load below-the-fold images with loading="lazy" (never the LCP image).
- Audit third parties; each tag adds main-thread cost and risk. Anything not paying for itself in revenue or insight gets removed, not deferred.
- Re-measure after every change; do not trust intuition or ship batched changes that cannot be attributed.

### Step 6: Verify in the field

Lab confirmation is necessary but not sufficient. Confirm the p75 field metric moved over a full collection window before declaring the fix done.

## Worked artifact: the hero image, bad vs good

Bad - invisible to the preload scanner, unsized, blocking script:

```html
<div class="hero" style="background-image:url(hero.jpg)"></div>
<script src="analytics.js"></script>
```

Good - discoverable, prioritized, sized, deferred:

```html
<link rel="preload" as="image" href="hero.webp" fetchpriority="high">
<img src="hero.webp" width="1200" height="630" fetchpriority="high" alt="Product dashboard">
<script src="analytics.js" defer></script>
```

The bad version hides the LCP image from the browser's preload scanner (background-image is only discovered after CSS parses), gives the layout engine no dimensions (CLS), and blocks parsing on analytics. The good version typically moves LCP by hundreds of milliseconds on 4G from the preload and priority hints alone.

## Edge cases

- SPA route changes: instrument soft navigations; Core Web Vitals accounts for them.
- Hydration cost: prefer streaming SSR and islands to reduce main-thread blocking.
- Cache busting: hash filenames and set long max-age with immutable on static assets.

## Deliverable

Produce a performance audit containing: current p75 field values for LCP, INP, and CLS against the 2.5s/200ms/0.1 budgets; the identified LCP element and top main-thread offenders; a ranked fix list where each item names the metric it moves, the specific code change, and the expected direction of impact; and a re-measurement plan (lab check per change, field confirmation at p75 over a full window).

## Do NOT

- Do not optimize from a Lighthouse score alone - lab data on a fast machine routinely passes while p75 field data fails.
- Do not test on the development laptop; the median user is on a mid-tier phone over 4G.
- Do not lazy-load the LCP image - it adds a full request-discovery delay to the one image that matters most.
- Do not chase a perfect score on a metric already passing while another fails; the failing metric sets the agenda.
- Do not ship five changes at once - attribution dies and regressions hide.
- Do not add a third-party tag manager to "manage" performance; every tag is main-thread cost.

## Quality bar

- Every claim of improvement cites a before/after measurement, lab and field.
- The audit names the specific LCP element and the specific long tasks, not "images are slow".
- Every recommended change maps to exactly one metric and one budget.
- The device/network profile used for lab testing is stated in the report.
- p75 field data confirms the fix before it is declared done.

## Escalation

If the bottleneck is server capacity under concurrency rather than client rendering, route to load-testing. Backend query time dominating TTFB belongs with sql-query-optimizer or caching-strategy. Native app jank belongs with mobile-perf-profiler.
