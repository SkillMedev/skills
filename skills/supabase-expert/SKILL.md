---
name: Supabase Expert
description: Builds secure Supabase apps - Row Level Security policies as the authorization layer, schema design against auth.users, Edge Functions for service-role work, realtime subscriptions, and versioned migrations. Use when someone asks "set up RLS for my tables", "is my Supabase app secure", "anyone can read my table with the anon key", "when do I need an Edge Function", or "why is my realtime subscription not receiving rows". Do NOT use for general Postgres schema design without Supabase - use database-schema instead; for tuning slow queries, use sql-query-optimizer; for Stripe billing inside a Supabase app, use stripe-integration.
---

# Supabase Expert

Supabase exposes Postgres directly to browsers through the anon key, which means Row Level Security is not a hardening step - it IS the authorization layer. A table without RLS is a table every visitor can read and write with credentials shipped in your JavaScript bundle. This skill builds Supabase apps where every table fails closed, privileged work is isolated server-side, and schema plus policies are versioned artifacts.

## Operating procedure

RLS comes immediately after tables exist because every hour a table lives without policies is an hour it is publicly readable.

### Step 1: Gather inputs

1. The data model and, for every table, its access rule in one sentence: "owners read/write their own rows", "team members read team rows", "public read, owner write". If a table's rule cannot be stated in a sentence, the model is not ready for policies.
2. Auth model - individual users only, or teams/orgs (which need a membership table that policies join through)?
3. Client surfaces - browser, mobile, server? Anything server-side that legitimately needs to bypass RLS?
4. Realtime and Storage requirements, which need their own policy passes.

### Step 2: Design the schema against auth

- Ownership column on every user-owned table: `user_id uuid not null references auth.users(id) default auth.uid()`. The default closes the classic bug where the client forgets to send user_id (or sends someone else's).
- Never modify the `auth` schema itself; a `public.profiles` table (keyed by the auth user id, populated by an on-signup trigger) holds app-level user data.
- Standard schema discipline applies - constraints, correct types, indexed foreign keys. For deep schema questions, pair with database-schema.

### Step 3: Enable RLS and write per-operation policies

```sql
alter table notes enable row level security;

create policy "owner can read" on notes
  for select using (auth.uid() = user_id);

create policy "owner can insert" on notes
  for insert with check (auth.uid() = user_id);

create policy "owner can update" on notes
  for update using (auth.uid() = user_id)
  with check (auth.uid() = user_id);

create policy "owner can delete" on notes
  for delete using (auth.uid() = user_id);
```

- One policy per operation (select/insert/update/delete). A single broad `for all` policy hides asymmetries - reading and deleting rarely deserve identical rules.
- `using` filters which existing rows the operation can see; `with check` validates rows being written. UPDATE needs both - `using` alone lets a user rewrite their row to belong to someone else.
- Default-deny is the safety net: RLS enabled with no policy blocks everything. Add access deliberately, never by disabling RLS "to debug".
- Team access joins through membership: `using (team_id in (select team_id from memberships where user_id = auth.uid()))`. Wrap `auth.uid()` calls as `(select auth.uid())` in policies so Postgres evaluates it once per query instead of once per row.
- Index every column policies filter on (`user_id`, `team_id`) - policies execute per row, and an unindexed predicate turns every select into a sequential scan.

### Step 4: Isolate privileged work in Edge Functions

The service role key bypasses all RLS. It exists only server-side - in Edge Functions or a trusted backend - and never in any client bundle, mobile app, or public repo.

Use an Edge Function when the operation needs privileges the caller must not hold (admin writes, cross-user aggregation, third-party API calls with secret keys). The function must validate the caller's JWT itself, because it is running with the master key:

```ts
Deno.serve(async (req) => {
  const jwt = req.headers.get('Authorization')?.replace('Bearer ', '');
  const { data: { user }, error } = await anonClient.auth.getUser(jwt);
  if (error || !user) return new Response('unauthorized', { status: 401 });
  // privileged work with the service-role client, scoped to what `user` may do
  return new Response(JSON.stringify({ ok: true }));
});
```

If an operation works under the caller's own RLS, do it from the client - Edge Functions are for privilege boundaries, not a default API layer.

### Step 5: Wire realtime and storage

- Enable replication on tables you subscribe to. Realtime respects RLS for Postgres changes - clients only receive rows their policies let them read, so a "subscription receives nothing" bug is usually a policy bug.
- Subscribe with filters (`event`, `filter=user_id=eq....`) to limit payload volume.
- Storage buckets have their own RLS-style policies on `storage.objects`; a private bucket with no policies is inaccessible, a public one is public to everyone. Set policies per bucket explicitly.

### Step 6: Version everything and test as different users

- All schema and policy changes go through supabase CLI migrations - the dashboard SQL editor is for exploration, not production change management.
- Test the policy matrix as three actors before shipping: anonymous (no session), user A, and user B attempting to read/write A's rows. A missing policy fails closed (annoying, safe); a too-broad policy leaks silently (invisible until breach).

## Worked contrast

Bad - the table every Supabase security incident starts with:

```sql
create table notes (
  id uuid default gen_random_uuid() primary key,
  user_id uuid,               -- nullable, client-supplied
  body text
);
-- RLS never enabled: anon key reads and writes every row
```

Good:

```sql
create table notes (
  id uuid default gen_random_uuid() primary key,
  user_id uuid not null references auth.users(id) default auth.uid(),
  body text not null
);
alter table notes enable row level security;
create index on notes (user_id);          -- policy predicate is indexed
-- plus the four per-operation policies from Step 3
```

## Deliverable

Produce a Supabase security and schema package containing: the migration files for tables with ownership columns and indexes on policy predicates; per-operation RLS policies for every table plus storage bucket policies; the Edge Function code for each privilege boundary with JWT validation; and the three-actor test matrix (anon / user A / user B) with observed results per table.

## Do NOT

- Do not leave RLS disabled on any table holding user data - the anon key in the browser reads all of it.
- Do not ship the service role key anywhere client-side; it bypasses every policy.
- Do not write UPDATE policies with only `using` - without `with check`, rows can be rewritten out of the owner's scope.
- Do not rely on client code to set `user_id`; default it from `auth.uid()` and constrain it NOT NULL.
- Do not leave policy predicate columns unindexed - per-row policy checks on unindexed columns collapse query performance.
- Do not forget that every table in a join needs its own policies, and that storage buckets need policies separate from tables.

## Quality bar

- Every user-data table has RLS enabled, four per-operation policies, and an index on each policy predicate.
- The three-actor test passes: anon sees nothing private, user B cannot read or write user A's rows.
- Service role key appears in zero client bundles (grep the build output, not just the source).
- Every Edge Function validates the caller's JWT before privileged work.
- Schema and policies exist as CLI migrations reproducible on a fresh project.

## Escalation

Route pure schema modeling to database-schema, slow-query work to sql-query-optimizer, and billing flows to stripe-integration. For a full pre-launch security pass beyond RLS (secrets, dependencies, headers), pair with secure-code-review.
