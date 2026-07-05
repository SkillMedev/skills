---
name: Test & Placeholder Data APIs
description: Use when building a demo, prototype, or tutorial that needs realistic remote data - "fake products/users/todos for this app", "an API I can hit while wiring up fetch", "placeholder images", "seed this UI with something that looks real", or teaching HTTP/CRUD against a live endpoint. DummyJSON is the default (rich e-commerce-shaped data, full fake CRUD, no key); JSONPlaceholder, RandomUser, and Lorem Picsum cover the rest. Do NOT use for generating local test fixtures or factories inside a test suite - use test-data-builder instead; do NOT use when the task needs real-world data (weather, prices, countries) - route through public-data-api-picker.
---

# Test & Placeholder Data APIs

Wire a demo against live fake data in one call. The mistake this prevents: hand-writing a 40-line mock array (or standing up a local json-server) when a keyless hosted API already serves richer, more realistic data - and the demo then never exercises real fetch/loading/error paths.

## Ranked APIs

1. **DummyJSON** (dummyjson.com) - DEFAULT. 190+ products, users, carts, posts, recipes with realistic fields; search, pagination, field selection; fake write endpoints for full CRUD demos. No key.
2. **JSONPlaceholder** (jsonplaceholder.typicode.com) - the classic minimal set (posts/comments/todos/users) when the tutorial audience expects it or you need the flattest possible shape. No key.
3. **RandomUser** (randomuser.me) - generated people with names, emails, avatars, addresses. Use for user-directory and profile-card UIs. No key.
4. **Lorem Picsum** (picsum.photos) - placeholder photos at any size. No key.

## Procedure

1. **Match the API to the demo's domain**: e-commerce/dashboard → DummyJSON products or carts; social/feed → DummyJSON posts or JSONPlaceholder; people/profiles → RandomUser; image slots → Picsum.
2. **Make pagination and search real** - DummyJSON supports it server-side, so demo the actual pattern instead of slicing a local array (templates below).
3. **Seed deterministically when it matters.** RandomUser output changes per call; pin it with `&seed=demo` so screenshots and tests reproduce. Picsum: use `/id/{n}/` URLs, not `/random`.
4. **Label fake writes.** DummyJSON's POST/PUT/DELETE return plausible responses but persist nothing - say so in the demo README/comments so nobody files a bug that "saves don't stick".

Ask the user only if missing: domain of data (default: products), row count (default: 10), whether writes are needed (default: read-only).

## URL templates + real response shapes

**DummyJSON - read, search, paginate, select:**

```
https://dummyjson.com/products/1
https://dummyjson.com/products?limit=10&skip=20&select=title,price,rating
https://dummyjson.com/products/search?q=phone
https://dummyjson.com/users        https://dummyjson.com/carts        https://dummyjson.com/posts
```

```json
{"id":1,"title":"Essence Mascara Lash Princess","category":"beauty","price":9.99,
 "rating":2.56,"stock":99,"brand":"Essence","thumbnail":"...","images":["..."],
 "dimensions":{"width":15.14,"height":13.08},"reviews":[{"rating":2,"comment":"..."}]}
```

List endpoints wrap results: `{"products":[...],"total":194,"skip":20,"limit":10}` - read `total` for pager math. Fake CRUD: `POST /products/add`, `PUT /products/1`, `DELETE /products/1` (echo back, no persistence).

**JSONPlaceholder - flat classic resources:**

```
https://jsonplaceholder.typicode.com/posts/1
https://jsonplaceholder.typicode.com/posts?userId=1
https://jsonplaceholder.typicode.com/posts/1/comments
```

```json
{"userId":1,"id":1,"title":"sunt aut facere...","body":"quia et suscipit..."}
```

100 posts, 200 todos, 10 users; text is Latin filler. Writes are fake here too.

**RandomUser - generated people:**

```
https://randomuser.me/api/?results=25&nat=us,gb&seed=demo
```

```json
{"results":[{"name":{"first":"Regina","last":"Fisher"},"email":"regina.fisher@example.com",
  "location":{"city":"York","state":"Massachusetts","country":"United States"},
  "picture":{"large":"https://randomuser.me/api/portraits/women/88.jpg"},
  "login":{"uuid":"c305bcf6-..."}}],"info":{"seed":"demo","results":25}}
```

Top level is always `results[]` even for one user. Trim payload with `&inc=name,email,picture`.

**Lorem Picsum - images:**

```
https://picsum.photos/id/237/400/300          ← deterministic (dog photo, 400×300)
https://picsum.photos/seed/{any-string}/400/300   ← deterministic by seed
https://picsum.photos/v2/list?page=1&limit=30     ← JSON metadata: id, author, download_url
```

Image URLs 302-redirect to the file - fine in `<img src>`, but follow redirects when fetching programmatically. `?grayscale&blur=2` for effects.

## Deliverable

Working fetch code (or curl) against the chosen endpoint with real pagination/search wired, deterministic seeds where repeatability matters, and an explicit note on any fake-write endpoints.

## Do NOT use - reach for X instead

- **Local fixtures, factories, or builders inside a test suite** - test-data-builder. This skill is for *hosted* fake data behind real HTTP.
- **Mocking a network call in unit tests** - mock-stub-designer; don't point unit tests at live placeholder APIs (network flake becomes test flake).
- **Real stock photography with search, licensing, and attribution (Unsplash, Pexels, Pixabay)** - stock-photo-api; Picsum here is throwaway layout filler only.
- **Real data needs** - weather-climate, geo-places, finance-fx, or the public-data-api-picker router. Demo data in a real feature is a bug.
- **Load testing** - these are free community services; don't benchmark against them.

## Quality bar

- The demo exercises at least one real HTTP concern (loading state, pagination, or error path) that a hardcoded array could not.
- Repeatable output wherever the UI is screenshotted or asserted on (seeds pinned).
- Any fake-persistence endpoint is labeled as such where the next developer will see it.

Sourced and liveness-verified from the public-apis project (MIT).
