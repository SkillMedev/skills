---
name: GraphQL Schema
description: Designs GraphQL schemas and resolvers that scale - domain-modeled types, Relay pagination, DataLoader batching to kill N+1, mutation payloads with typed user errors, and depth/complexity limits that stop abusive queries. Use when someone asks "how should I structure this GraphQL type", "my resolvers are hammering the database", "cursor or offset pagination", "how do I version a GraphQL API", or is designing or reviewing a schema or federation split. Do NOT use for REST or RPC endpoint design - use api-design instead; do NOT use for the underlying table design - use database-schema instead; for hunting existing N+1s in a codebase, use n-plus-one-hunter.
---

# GraphQL Schema

A GraphQL schema is a public contract that clients build against for years, and a resolver layer is an open invitation for N+1 queries and abusive nesting. This skill designs schemas clients love and servers survive: model the domain rather than the database, batch every relationship resolver, and put hard limits on what a single query may cost - because removing a shipped field or fixing a hot N+1 under load is 10x the effort of designing it right.

## Operating procedure

### Step 1: Gather inputs

- The domain nouns and the operations clients actually need (screens/use-cases, not tables).
- Largest list sizes and expected query shapes; who the clients are (first-party only vs public API - public means stricter limits).
- Data sources behind each type and which relationships cross a database or service boundary.
- Single graph or federated subgraphs.

### Step 2: Model the graph

1. Model around domain nouns, not database tables - the schema is for clients; the database is an implementation detail (table design belongs to database-schema).
2. Use schema-first SDL as the contract; generate types from it.
3. Prefer non-null (`!`) by default; make a field nullable only when null is a real, meaningful value or when the field's resolver can fail independently and you want partial results instead of a nulled-out parent chain.
4. Return rich object types, not scalars, so fields can grow without breaking clients.
5. Mutations return a payload type carrying the mutated entity and a `userErrors` list - do not model expected business failures as top-level GraphQL errors.

### Step 3: Paginate every list that can grow

- Cursor-based connections (Relay spec) for anything unbounded; offset pagination breaks under concurrent writes and invites `offset: 500000` table scans.
- Expose `edges { node cursor }` and `pageInfo { hasNextPage endCursor }`.
- Require `first`/`last` with a hard cap - 100 is the standard ceiling; default to 20-25 when the client omits it. An uncapped list argument is a self-serve denial-of-service endpoint.

### Step 4: Batch every relationship resolver

The classic trap: resolving `items.customer` fires one query per row. Every resolver that crosses a table or service boundary gets a DataLoader, created per-request (a shared loader leaks data across users and requests).

```js
const customerLoader = new DataLoader(async (ids) => {
  const rows = await db.customers.whereIn('id', ids);
  return ids.map(id => rows.find(r => r.id === id)); // preserve input order; missing = undefined
});
// resolver:
Order.customer = (order) => customerLoader.load(order.customerId);
```

Verify with a query log: a page-of-50-orders query should issue on the order of 2-3 SQL queries, not 51. Auditing an existing codebase for these is n-plus-one-hunter's job.

### Step 5: Enforce query cost limits

- Depth limit: 8-10 for public APIs; first-party-only graphs can allow up to ~15. Anything deeper is either a malicious query or a client that should be paginating.
- Complexity/cost limit: assign each field a cost (scalar 1, object 2, list = child cost × requested page size) and reject queries over a budget in the low thousands of points, tuned so the heaviest legitimate client query passes with ~2x headroom.
- Reject at validation time, before execution - a rejected query should cost you parsing, not database load.
- For public APIs, add per-client rate limiting denominated in cost points, not request count, since one GraphQL request can equal 500 REST calls.

### Step 6: Plan evolution and federation

- Version by evolving additively; deprecate with `@deprecated(reason: "use newField")` and remove only after client traffic on the field hits zero. Never break a shipped field (deprecation rollout strategy: api-versioning-strategist).
- Federation: split by subgraph ownership; use `@key` to define entity references; each subgraph owns its fields and the gateway composes. Keep entities resolvable by their key in every subgraph that extends them.

### Step 7: Harden the edges

- Authorization per-field, not just at the query entry point - a nested path reaches any type the graph connects.
- Never expose raw database errors; map to typed, safe messages.
- Errors mid-list: return partial results with the `errors` array rather than failing the whole response.
- Caching: GraphQL-over-POST defeats HTTP caching - use persisted queries (also a security win: only known queries execute) or response caching keyed on query + variables.

## Worked artifact: schema design, bad vs good

Bad - tables leaked into the schema, unbounded list, scalar foreign key, throw-only mutation:

```graphql
type Query {
  orders_tbl(user_id: Int): [OrderRow]
}
type OrderRow {
  id: Int
  status_code: Int      # magic number, client must decode
  customer_id: Int      # scalar FK - client must make a second query
}
type Mutation {
  updateOrder(id: Int, status: Int): Boolean   # failures = thrown errors only
}
```

Good - domain types, enum, object relationships, capped connection, error-carrying payload:

```graphql
type Query {
  order(id: ID!): Order
  orders(first: Int! = 25, after: String): OrderConnection!   # first capped at 100
}
type Order {
  id: ID!
  status: OrderStatus!
  items: [LineItem!]!
  customer: Customer!            # object, resolved via DataLoader
}
enum OrderStatus { PENDING PAID SHIPPED }
type OrderConnection {
  edges: [OrderEdge!]!
  pageInfo: PageInfo!
}
type Mutation {
  updateOrderStatus(input: UpdateOrderStatusInput!): UpdateOrderStatusPayload!
}
type UpdateOrderStatusPayload {
  order: Order
  userErrors: [UserError!]!      # expected failures as data, not exceptions
}
type UserError { field: [String!], code: String!, message: String! }
```

## Deliverable

Produce the SDL schema plus a resolver plan listing: every relationship field with its DataLoader, the pagination caps per connection, the depth and complexity limits with the cost model, the authorization rule per sensitive field, and the deprecation policy for future changes.

## Do NOT

- Do not mirror database tables into the schema - you freeze your storage layout into a public contract.
- Do not ship an unbounded list field; every list that can grow gets a connection with a capped page size.
- Do not resolve relationships row-by-row; no relationship resolver ships without a batch loader.
- Do not share DataLoader instances across requests - cross-user cache leakage.
- Do not model expected business failures as thrown GraphQL errors; use payload `userErrors` so clients can handle them typed.
- Do not remove or retype a shipped field; deprecate additively and wait for zero traffic.
- Do not rely on entry-point auth; enforce per-field.

## Quality bar

- Every type is a domain noun; no `_tbl`, `_id`-scalar-FK, or status-code-integer smells remain.
- Every list is a connection with an enforced page cap; depth and complexity limits are enabled and tested with a hostile deeply-nested query.
- A logged trace of the heaviest client query shows batched loads (single-digit queries per request).
- Every mutation returns a payload with `userErrors`; every error path returns safe messages plus partial results where possible.
- Federation entities resolve by `@key` from every extending subgraph.
