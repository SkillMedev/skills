---
name: Stock Photo APIs
description: Integrates the Unsplash, Pexels, and Pixabay APIs in code - auth, search endpoints, rate limits, attribution, hotlinking versus caching rules, and the compliance obligations that get apps deactivated when skipped. Use when someone says "integrate the Unsplash API", "pull images from Pexels in my app", "build an image picker on a stock photo API", "how do I trigger the Unsplash download endpoint", or is wiring stock imagery into a CMS, blog pipeline, or placeholder service. Do NOT use to choose a source by hand or write a search brief - use stock-photo-finder instead; do NOT use to decide whether a license permits a use or a release is needed - use image-license-rights instead; do NOT use to art-direct results - use visual-asset-curation instead.
---
# Stock Photo APIs

Wire stock imagery into a product through the three main free APIs - Unsplash, Pexels, Pixabay - without tripping the compliance rules that get API keys revoked. The fetch is trivial; the contractual obligations (Unsplash's download-endpoint trigger, Pixabay's mandatory caching, attribution everywhere) are the actual work, and skipping them is the #1 reason apps get deactivated.

## Operating procedure

### Step 1: Gather inputs

- Is the need dynamic, repeated, or in-product? For a one-off hero image, download it by hand - an API key is overkill.
- Which provider(s), or should the integration abstract over all three? (Choosing a source for a specific image is stock-photo-finder's job.)
- Expected request volume, to check against the rate-limit table.
- Where images will render (client hotlink vs server cache) - this decides between Unsplash's hotlink rule and Pixabay's cache rule.
- Whether anything commercial ships - a human owns that license call via image-license-rights.

### Step 2: Pick against the provider quick reference

| Provider | Auth | Search endpoint | Default rate limit | The gotcha most people miss |
|---|---|---|---|---|
| Unsplash | Authorization: Client-ID ACCESS_KEY | GET /search/photos | Demo 50 req/hr; Production 5000 req/hr after app review | MUST trigger the per-photo download endpoint on use, attribute photographer + Unsplash with UTM links, and hotlink the returned urls (do not rehost) |
| Pexels | Authorization: API_KEY | GET /v1/search | 200 req/hr, 20,000 req/mo default | Show Pexels + photographer attribution; do not build a Pexels clone; respect the X-Ratelimit-* response headers |
| Pixabay | ?key=API_KEY query param | GET /api/ | ~100 req/min | Must cache/download images to your own server per their terms rather than permanently hotlinking; cache API responses ~24h |

These are the documented defaults - confirm current limits and terms in each provider's live docs before shipping, since providers revise them.

### Step 3: Implement the compliant flow (Unsplash worked example)

Unsplash's guidelines are strict and enforced. The three non-negotiables: hotlink, trigger-download, attribute.

```ts
const ACCESS_KEY = process.env.UNSPLASH_ACCESS_KEY!; // server-side only
const H = { Authorization: `Client-ID ${ACCESS_KEY}` };

// 1. Search. Hotlink the urls Unsplash returns; do not rehost the file.
async function searchPhotos(query: string) {
  const res = await fetch(
    `https://api.unsplash.com/search/photos?query=${encodeURIComponent(query)}&per_page=12&orientation=landscape`,
    { headers: H },
  );
  if (!res.ok) throw new Error(`Unsplash ${res.status}`);
  const { results } = await res.json();
  return results; // each has urls.regular, links.download_location, user{name, links.html}
}

// 2. REQUIRED: when a photo is actually used/downloaded, ping its
//    download_location. This is how photographers get credited usage.
//    Skipping it is the top reason Unsplash deactivates apps.
async function triggerDownload(downloadLocation: string) {
  await fetch(downloadLocation, { headers: H });
}

// 3. REQUIRED: attribute with UTM params to the photographer and Unsplash.
function attribution(photo: { user: { name: string; links: { html: string } } }) {
  const u = `${photo.user.links.html}?utm_source=your_app&utm_medium=referral`;
  return `Photo by <a href="${u}">${photo.user.name}</a> on ` +
         `<a href="https://unsplash.com/?utm_source=your_app&utm_medium=referral">Unsplash</a>`;
}
```

### Step 4: Run the compliance checklist

- Keys are secret and server-side. Never ship an access/secret key in client JS - proxy through a backend or serverless function.
- Unsplash: hotlink urls, fire download_location on use, attribute with UTM links. Demo apps stay capped at 50 req/hr until production approval.
- Pexels: render the required Pexels + photographer attribution; do not replicate Pexels as a product.
- Pixabay: download/cache images to your server; cache API responses ~24h; do not permanently hotlink.
- Rate limits: read the response headers (X-Ratelimit-Remaining and friends), back off on 429, and cache search results so one query is not re-fetched on every render - pair with rate-limit-handler for the backoff design.
- License still applies. The API grants no rights it does not have: identifiable people/brands and editorial-only assets remain image-license-rights territory. Surface that to whoever publishes the result.

### Step 5: Build for robustness

- Wrap calls with timeouts, retry-with-backoff on 429/5xx, and a graceful empty state for zero results.
- Persist provider, photo ID, photographer name, profile URL, and attribution HTML alongside any stored image - needed to render credit and answer later license questions.
- Normalize the providers behind one internal interface (`searchImages(query): Promise<Image[]>`) so callers never depend on a provider's response shape, and a provider swap is one adapter.

## Deliverable

Produce a working integration containing: the server-side proxy with secret key handling, the provider adapter(s) behind a normalized interface, the Unsplash download-trigger call sited at the moment of use, attribution rendering, response caching per provider rule, and stored metadata (provider, ID, photographer, attribution HTML) for every persisted image.

## Do NOT

- Do not ship API keys in client-side code.
- Do not skip Unsplash's download_location trigger or its UTM attribution - the enforcement is real.
- Do not rehost Unsplash images (hotlink their urls) and do not permanently hotlink Pixabay images (cache them) - the two rules are opposites; applying the wrong one violates the terms.
- Do not hammer search on every page render - cache results and honor rate-limit headers.
- Do not treat API access as a license opinion; anything commercial with identifiable people or brands goes through image-license-rights.
- Do not hand-pick images or write search briefs here - that is stock-photo-finder.

## Quality bar

- Each provider's specific compliance rule is implemented, not just the fetch: download trigger + attribution + hotlinking for Unsplash, attribution for Pexels, server caching for Pixabay.
- No key appears in client-delivered code.
- A 429 produces backoff, not a retry storm; zero results produce a designed empty state.
- Every persisted image carries enough metadata to render credit and answer a license question later.

## Escalation

License permission and model/property-release questions go to image-license-rights (and to counsel for anything contractual). Source selection and search strategy go to stock-photo-finder; visual selection among results to visual-asset-curation.
