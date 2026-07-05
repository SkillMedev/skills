---
name: Next.js App Router
description: Builds and reviews Next.js App Router code - server/client component boundaries, data fetching, caching and revalidation choices, streaming with Suspense, and Server Action mutations - and delivers routes where every dynamic-vs-cached decision is explicit. Use when someone asks "should this be a client component", "why is my data stale", "where do I put use client", "how do I stream this page", or "how do server actions work". Do NOT use for Vercel-specific runtime and cost tuning of an already-built app - use next-on-vercel-perf instead; for edge middleware and ISR deployment topology - use vercel-edge-and-isr instead; for framework-agnostic Core Web Vitals work - use web-performance instead; for designing an application-wide cache layer beyond the framework - use caching-strategy instead.
---

# Next.js App Router

Default to Server Components; reach for Client Components only for interactivity, state, or browser APIs. The two costly mistakes this skill prevents are the accidental client tree - one `'use client'` placed high that drags the whole page into the browser bundle - and accidental caching, where `fetch`'s default caching silently serves stale data and the team "fixes" it by sprinkling `force-dynamic` everywhere, losing all caching.

## Operating procedure

### Step 1: gather inputs

Collect before writing a route (label guesses as guesses):

1. Per piece of data on the page: is it per-request/per-user, or shared and cacheable? If cacheable, what staleness is acceptable (seconds, minutes, hours)?
2. Which UI elements genuinely need interactivity, state, effects, or browser APIs.
3. Mutations on the page and what data they invalidate.
4. Auth model: does rendering depend on cookies/headers (forces dynamic rendering)?

### Step 2: draw the client boundary at the leaves

- Mark `'use client'` only at the smallest component that needs it. Everything imported by a client component becomes client code, so a high boundary silently ships the subtree to the browser.
- If a client component needs server-rendered content inside it, pass it as `children` - composition keeps the inner tree on the server.
- Pass data down as props; do not lift server data into client state just to share it.

Bad:

```tsx
'use client'                    // whole page is now client code
export default function Page() {
  const [open, setOpen] = useState(false)
  // 40 KB of product listing logic dragged into the bundle for one toggle
}
```

Good:

```tsx
// page.tsx - server component, fetches and renders
export default async function Page() {
  const products = await getProducts()
  return <ProductList products={products} action={<FilterToggle />} />
}
// FilterToggle.tsx - 'use client', only the toggle ships to the browser
```

### Step 3: fetch on the server, choose caching explicitly

- Fetch in server components with `async`/`await`. No `useEffect` data fetching unless the data is genuinely client-driven (e.g. depends on live client state).
- `fetch` is cached by default - every fetch call must make a deliberate choice:
  - Shared, tolerates staleness: `{ next: { revalidate: N } }`. Pick N from acceptable staleness in Step 1 - 60 s for dashboards-ish freshness, 3600 s for marketing/content.
  - Must be fresh or per-user: `{ cache: 'no-store' }`, or `export const dynamic = 'force-dynamic'` when the whole route is per-request.
  - Tag cacheable data (`next: { tags: ['products'] }`) when a mutation will need to invalidate it precisely.
- Reading `cookies()` or `headers()` makes the route dynamic - expect it, don't fight it.
- Deduplicate: a `fetch` with identical args is memoized per request, so fetch where the data is used rather than prop-drilling from the top.

### Step 4: stream instead of blocking

- Add `loading.tsx` for every route with async work - the shell should render immediately while data streams in.
- Wrap independently-slow sections in `<Suspense>` with their own fallbacks so one slow query does not hold the whole page. If any single data dependency takes over ~300 ms, it is a streaming candidate; never let one slow fetch block first paint past 1 s when the rest of the page is ready.
- `error.tsx` for route-level error boundaries; `not-found.tsx` plus `notFound()` for 404s.

### Step 5: mutate with Server Actions and revalidate

- Use Server Actions for mutations. Validate input on the server with a schema - never trust the client; the action is a public endpoint regardless of which form calls it.
- Check authorization inside the action, not only in the page that renders the form.
- After a successful mutation, call `revalidatePath` or `revalidateTag` for exactly the data that changed - tags from Step 3 make this precise instead of nuking whole routes.
- Return typed error state from actions for the form to render; do not throw for expected validation failures.

## Deliverable

Produce the route implementation plus a one-table caching manifest: for each data source on the page - cached or dynamic, revalidate interval or no-store, tags, and which mutation invalidates it. Every choice explicit, none accidental.

## Do NOT

- Do not put `'use client'` on a page or layout to fix one interactive widget - extract the widget instead; the directive is transitive to every import.
- Do not fetch in `useEffect` what a server component could fetch - it adds a client-server waterfall and a loading spinner where streamed HTML would do.
- Do not "fix" stale data with `force-dynamic` route-wide when one fetch needed `no-store` - you lose caching for everything else on the page.
- Do not skip validation or auth checks in Server Actions because "only my form calls it" - actions are callable endpoints.
- Do not store server data in client state and manually sync it; revalidation after mutation is the sync mechanism.

## Quality bar

- Every `'use client'` directive is at a leaf and justified by interactivity, state, or a browser API.
- Every `fetch` has an explicit caching decision; grep for bare fetches with none.
- Every async boundary has a loading state; no route blanks while awaiting data.
- Every mutation revalidates the specific paths/tags it affects, and validates + authorizes on the server.
- The caching manifest exists and matches the code.
