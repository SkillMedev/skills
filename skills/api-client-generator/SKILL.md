---
name: API Client Generator
description: Generates a typed API client from an OpenAPI/Swagger spec, with a hand-controlled transport wrapper for timeouts, auth, and typed errors. Use when integrating a REST API that ships an openapi.yaml/swagger.json, when generating or regenerating a client from a spec, or when a hand-written client keeps drifting from the upstream contract. Do NOT use when the task is the backoff/retry policy itself — use rate-limit-handler instead; do NOT use for cursor pagination or keeping a local copy in sync — use pagination-and-sync-engineer instead.
---
# API Client Generator

Generate the API surface from the spec so types stay honest, then wrap the generated code in a transport layer you own for resilience.

## Workflow
1. Confirm a spec exists. If the API ships an OpenAPI/Swagger document, generate from it. If there is no spec, or the integration is a single endpoint, hand-write a small typed wrapper with the same transport in step 4 — skip codegen.
2. Generate the client. Run a codegen tool (openapi-typescript-codegen, openapi-generator, oapi-codegen, or equivalent) to produce request methods plus request/response types. Pin the spec by version or hash so an upstream change surfaces as a reviewable diff, not a silent runtime surprise.
3. Treat generated code as a build artifact. Commit it, regenerate it from the pinned spec, and never hand-edit it — edits get clobbered on the next regen.
4. Wrap, don't modify. Inject a custom HTTP transport (the client's configurable fetch/http layer) instead of editing generated files. Put timeouts, auth, default headers, and logging in that one wrapper so every generated call inherits them and the generated layer stays regenerable.
5. Set an explicit per-request timeout in the transport — generated clients usually default to none.
6. Make errors typed. Map non-2xx responses to a discriminated error type — auth (401/403), not-found (404), validation (422), rate-limit (429), server (5xx) — and parse the API's structured error body into a typed shape so callers branch on a tag, not on parsed strings. Never let a non-2xx return a half-empty success object.
7. Centralize config. Hold base URL, auth token refresh, and default headers in one configured instance; don't scatter raw fetch calls across the codebase.
8. Optionally validate critical responses against the schema at runtime so spec drift fails loudly. Generated types describe the contract, not reality.
9. Defer cross-cutting policy. Add the retry/backoff hook point in the transport, but get the backoff policy (exponential backoff, full jitter, Retry-After, retriable status codes, idempotency) from rate-limit-handler. Get cursor pagination and incremental sync from pagination-and-sync-engineer. This skill owns the generated surface and the transport seam, not those policies.

## Worked example: the transport seam

Bad — resilience patched into a generated file (clobbered on next regen, invisible in review):

```ts
// generated/services/UserService.ts  ← hand-edited generated code
export class UserService {
  static async getUser(id: string) {
    const res = await fetch(`${BASE}/users/${id}`, {
      signal: AbortSignal.timeout(5000), // added by hand — lost on regen
    });
    return res.json(); // 404 returns {} shaped like a User
  }
}
```

Good — one owned transport injected into the untouched generated client:

```ts
// transport.ts — the only file that knows about timeouts, auth, errors
export const transport: Fetch = async (url, init) => {
  const res = await fetch(url, {
    ...init,
    headers: { ...init?.headers, Authorization: `Bearer ${await getToken()}` },
    signal: AbortSignal.timeout(10_000),
  });
  if (!res.ok) throw await toApiError(res); // discriminated union: auth | not_found | validation | rate_limit | server
  return res;
};

// client.ts — generated code stays pristine
export const api = createClient({ baseUrl: config.apiBase, fetch: transport });
```

Every generated method now inherits the timeout, auth, and typed errors; regenerating from an updated spec touches nothing hand-written.

## Deliverable

Produce a committed client package containing: the generated client code (untouched, reproducible from a pinned spec version or hash), one transport wrapper implementing timeout, auth, default headers, logging, and the typed error mapping, a single configured client instance exported for the codebase, and a regen script or documented command that regenerates from the pinned spec. A consumer should be able to call any endpoint through the instance and branch on a typed error without ever importing fetch directly.

## Quality bar
- Generated files are committed, untouched by hand, and reproducible from a pinned spec.
- Every call goes through one transport with an explicit timeout and typed errors; no raw fetch escapes it.
- Errors are a discriminated union keyed on the failure class, not strings.
- Regenerating from an updated spec produces a clean reviewable diff with zero manual reconciliation.

## Do NOT
- Do NOT hand-edit generated code, or add resilience by patching generated files — the next regen silently erases it.
- Do NOT swallow non-2xx into a success-shaped object; a 404 that returns `{}` typed as the resource is a downstream null-pointer factory.
- Do NOT scatter base URL, auth, or headers across call sites — token refresh then has to be fixed in N places.
- Do NOT float the spec (regenerating from "latest" on every build); pin it so upstream changes arrive as reviewable diffs, not surprise breakage.
- Do NOT reach for full codegen on a one-endpoint, spec-less API — hand-write a typed wrapper instead.
- Do NOT re-specify backoff/jitter/Retry-After or pagination/sync logic here — defer to rate-limit-handler and pagination-and-sync-engineer.
