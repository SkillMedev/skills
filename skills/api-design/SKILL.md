---
name: REST API Design
description: Designs REST API surfaces - resource naming, HTTP method and status-code semantics, error shapes, pagination, and filtering - and delivers an endpoint spec a consumer can build against without asking questions. Use when someone asks "how should I name this endpoint", "what status code should this return", "should this be PUT or PATCH", "how do I paginate this list", or is reviewing an API before it ships to external consumers. Do NOT use for planning breaking-change rollouts and deprecation windows - use api-versioning-strategist instead; for GraphQL type and resolver design - use graphql-schema instead; for generating client SDKs from an existing spec - use api-client-generator instead; for designing inbound webhook endpoints - use webhook-receiver-hardener instead.
---

# REST API Design

APIs are products: the consumer's mental model, not your database schema, decides the shape. The costly mistake this skill prevents is shipping an inconsistent surface - mixed naming, wrong status codes, offset pagination on a growing table - that every consumer then hardcodes around, making it effectively unfixable once integrated.

## Operating procedure

Work in this order. Resources come before methods because method semantics depend on what the resource is; error and pagination contracts come before the spec is written because retrofitting them breaks consumers.

### Step 1: gather inputs

Collect before designing. Label any assumption as a guess.

1. The consumers: internal services, first-party clients, or external developers. External consumers raise the consistency bar - default to external if unknown.
2. The core nouns of the domain and which ones own which (drives nesting).
3. Expected collection sizes and growth. A list that can exceed ~10,000 rows rules out offset pagination.
4. Read/write ratio and latency expectations per endpoint.
5. Auth model (affects 401 vs 403 correctness).

### Step 2: name resources

- Plural nouns: /users, /orders, /products.
- Nest one level for ownership: /users/{id}/orders. Do not nest deeper than two levels - flatten with filters instead (/orders?user_id=... beats /users/{id}/orders/{oid}/items/{iid}).
- No verbs in paths; the HTTP method is the verb. For genuine actions that fit no CRUD verb (e.g. sending an invoice), model a subresource POST: POST /invoices/{id}/send-attempts.
- Consistent case: kebab-case for multi-word path segments, snake_case for JSON fields - pick once, apply everywhere.

### Step 3: assign methods and status codes

- GET: read, idempotent, no request body. 200 with body.
- POST: create. Return 201 plus a Location header pointing at the new resource. Return the created representation in the body so the client skips a follow-up GET.
- PUT: full replace, idempotent. Missing fields mean "clear", not "keep".
- PATCH: partial update (JSON Merge Patch or JSON Patch - state which in the spec).
- DELETE: remove, idempotent, returns 204. Deleting an already-deleted resource returns 204 or 404 - pick one and document it.
- Errors: 400 for malformed input, 401 for missing/invalid credentials, 403 for valid credentials without permission, 404 for absent resources (also use 404 instead of 403 when revealing existence is a leak), 409 for state conflicts, 422 for well-formed but semantically invalid input, 429 for rate limits (with Retry-After), 500 for server faults - never expose stack traces or internal identifiers.

### Step 4: fix the error contract

One shape for every error, machine-readable code first:

```json
{
  "error": "validation_error",
  "message": "Email is required",
  "field": "email",
  "request_id": "req_abc123"
}
```

The request_id must be logged server-side so support can correlate. Human message is for developers reading logs, never for end-user display.

### Step 5: fix pagination and filtering

- Cursor-based pagination for any collection that grows: return an opaque cursor and has_more flag. Offset pagination is acceptable only for small, bounded, admin-facing lists - it degrades linearly and skips/duplicates rows under concurrent writes.
- Default page size 25, maximum 100. Reject page sizes above the max with 400, do not silently clamp.
- Filtering and sorting via query params: ?status=active&sort=created_at:desc. Document which fields are filterable; reject unknown filter params with 400 rather than ignoring them (silent ignoring hides client bugs).

### Step 6: apply performance and payload budgets

- p95 latency budget: 300 ms for interactive endpoints, 1 s for reports/exports. If an operation cannot meet 1 s, make it asynchronous: 202 Accepted plus a status resource to poll.
- Response payload budget: keep typical responses under 100 KB; anything routinely above 1 MB needs pagination, field selection (?fields=), or a dedicated export flow.
- Version breaking changes in the URL path (/v1/). Additive changes (new optional fields) do not require a version bump. Hand the deprecation timeline itself to api-versioning-strategist.

## Worked contrast

Bad:

```
POST /getUserOrders?userId=42&page=3
→ 200 { "data": [...], "error": null }
```

Verb in the path, GET semantics on a POST, offset paging, 200 wrapping an error field so clients must parse the body to know if the call failed.

Good:

```
GET /users/42/orders?status=shipped&limit=25&cursor=eyJpZCI6OTh9
→ 200 { "orders": [...], "next_cursor": "eyJpZCI6MTIzfQ", "has_more": true }
→ 404 { "error": "not_found", "message": "User 42 does not exist", "request_id": "req_9f2" }
```

Noun path, ownership nesting, cursor paging, status codes carry the outcome.

## Deliverable

Produce an endpoint spec containing: the resource table (path, methods, status codes per method), the shared error shape, the pagination contract (cursor format, defaults, max), the filtering whitelist, and the latency/payload budget per endpoint.

## Do NOT

- Do not mirror the database schema in the API - consumers get coupled to internals you will want to change.
- Do not return 200 for failures with an error body; clients and monitoring both key off status codes.
- Do not use offset pagination on unbounded collections - it duplicates and skips rows under writes and degrades linearly.
- Do not nest paths deeper than two levels; deep nesting encodes one access path and forbids all others.
- Do not silently ignore unknown query parameters - a typoed filter that returns unfiltered data is a data-leak class bug.

## Quality bar

- A consumer could implement a client from the spec without asking a single question.
- Every endpoint states its status codes, including error cases.
- All collection endpoints share one pagination contract; all errors share one shape.
- Identical concepts have identical names across every path and payload.
- Each endpoint has an explicit latency budget and the >1 s ones are async.
