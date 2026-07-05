---
name: TypeScript Strict Mode
description: Write and review TypeScript as if strict mode plus the hardening flags (noUncheckedIndexedAccess, exactOptionalPropertyTypes) are enforced, and migrate loose codebases to full strictness flag by flag without breaking the build. Use when someone asks "how do I get rid of any", "turn on strict mode in an existing project", "is this type assertion safe", "why does tsc say possibly undefined", or when writing new TypeScript that must pass a strict compiler. Do NOT use for migrating between language or runtime versions - use language-version-migrator instead.
---

# TypeScript Strict Mode

Every `any` is a runtime bug that the compiler was ready to catch and was told not to. Strict TypeScript converts a class of production failures - undefined access, wrong-shape API responses, unhandled union cases - into compile errors that cost seconds instead of incidents. This skill enforces strictness in new code and migrates existing loose codebases without a big-bang rewrite.

## Operating procedure

1. **Determine the mode.** Greenfield code: apply the full config and the banned/required patterns below from the first commit. Existing loose codebase: jump to the migration procedure - do not flip every flag at once, because a wall of 2,000 errors gets the whole effort reverted.
2. **Set the config.** The target tsconfig is the artifact below. `"strict": true` is the floor, not the ceiling - `noUncheckedIndexedAccess` and `exactOptionalPropertyTypes` catch real bugs that `strict` alone misses.
3. **Write to the banned/required pattern lists.** Every review comment and every fix should cite one of them.
4. **Prefer narrowing over asserting.** When the compiler complains, the question is "how do I prove this to the compiler," not "how do I silence it." Use the narrowing toolkit.
5. **Contain unavoidable casts with the escape-hatch policy.** A cast is a claim the compiler cannot check; isolate it, validate it, and never let it propagate.

## Inputs to collect

Before migrating or reviewing, gather: (1) the current tsconfig, (2) whether the codebase is greenfield or existing - and if existing, the error count per flag (`tsc --noEmit` after toggling each flag tells you), (3) any third-party libraries with missing or bad types, since those are where casts concentrate. If the error counts are unknown, measure them first; label any estimate a guess.

## Target tsconfig

```jsonc
{
  "compilerOptions": {
    "strict": true,                        // enables all strict-family checks
    "noUncheckedIndexedAccess": true,      // arr[i] and obj[key] are T | undefined
    "exactOptionalPropertyTypes": true,    // { a?: string } rejects { a: undefined }
    "noImplicitOverride": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "forceConsistentCasingInFileNames": true
  }
}
```

## Banned patterns

- `any` - use `unknown` and narrow, or define the type.
- `as SomeType` on values from outside the type system - use type guards or schema validation instead.
- `value!.property` non-null assertion - handle null/undefined explicitly.
- `@ts-ignore` - fix the error. If suppression is truly unavoidable, `@ts-expect-error` with a reason comment, which errors when the underlying problem is fixed.
- Implicit `any` in function parameters.

## Required patterns

- Explicit return types on exported/public functions.
- `unknown` over `any` for truly unknown data, then narrow.
- `satisfies` over `as` when checking a value against a type without widening it.
- Discriminated unions over bags of optional properties.
- `as const` assertions for literal types.
- Exhaustive switches with a `never` check in the default case.

## Good/bad pairs

Bad - `any` erases checking downstream:

```typescript
function parse(json: string): any { return JSON.parse(json) }
const user = parse(body); user.emial // typo compiles, crashes later
```

Good - `unknown` forces proof before use:

```typescript
function parse(json: string): unknown { return JSON.parse(json) }
const data = parse(body)
if (isUser(data)) { data.email } // typo is a compile error
```

Bad - assertion claims without evidence:

```typescript
const user = JSON.parse(body) as User // wrong shape crashes at runtime
```

Good - narrowing proves the shape:

```typescript
function isUser(x: unknown): x is User {
  return typeof x === 'object' && x !== null && 'id' in x && 'email' in x
}
```

Good - exhaustive switch that breaks the build when a variant is added:

```typescript
function handle(action: Action) {
  switch (action.type) {
    case 'A': return handleA(action)
    case 'B': return handleB(action)
    default: { const _exhaustive: never = action; throw new Error('unreachable') }
  }
}
```

## Migration procedure for a loose codebase

Order matters: each flag builds on the previous one's guarantees, and the noisiest flags come last so early wins build momentum.

1. Ban new violations first: turn on the target flags in CI for *new and changed files only* (lint rule or a diff-based check), so the hole stops getting deeper while you drain it.
2. Enable `noImplicitAny`. Fix by adding real types, not `: any`. This is the highest-value flag per error fixed.
3. Enable `strictNullChecks` (with `strictFunctionTypes` and `alwaysStrict`). Usually the largest error count. If a flag produces more errors than the team can fix in about two weeks, scope it per-directory with project references or a second tsconfig, and ratchet the boundary weekly - never let an "errors allowed" list grow.
4. Enable the remainder of the strict family (`strictBindCallApply`, `strictPropertyInitialization`, `useUnknownInCatchVariables`), then replace the individual flags with `"strict": true`.
5. Enable `noUncheckedIndexedAccess`, then `exactOptionalPropertyTypes`. These are last because they are the noisiest and the fixes (index checks, `undefined`-vs-absent distinctions) are mechanical once the codebase is null-safe.
6. Delete every `@ts-expect-error` you can after each step; each one still standing needs a reason comment.

## Escape-hatch policy

A cast is acceptable only at a boundary the type system cannot see across: deserialized JSON, a third-party library with wrong types, a test fixture. Contain it:

- One cast per boundary, inside a single adapter function that validates before asserting (type guard or schema check). Callers receive a real type and never see the cast.
- Comment every cast with the reason it is safe.
- `as unknown as T` double assertions only in test code, never in production paths.
- `@ts-expect-error` over `@ts-ignore`, always with a description.

## Deliverable

Produce strict-compliant code plus, for migrations, a flag-by-flag plan: current error count per flag, the enablement order above, the per-directory scoping if any count exceeds the two-week budget, and the CI ratchet that blocks new violations immediately.

## Do NOT

- Do not fix a strict error by widening a type to `any` or adding `!` - that converts a found bug back into a hidden one.
- Do not flip all flags at once on a large codebase; the resulting error wall gets the migration reverted.
- Do not allow a suppression list that can grow; ratchets only tighten.
- Do not use `as` to satisfy the compiler on data from outside the process - validate, then narrow.
- Do not skip `noUncheckedIndexedAccess` because it is noisy; unchecked index access is the single most common strict-mode blind spot.

## Quality bar

- `tsc --noEmit` passes with the target config (or the migration plan states exactly which flags are pending and their error counts).
- Zero `any`, zero `@ts-ignore`, zero bare non-null assertions in the changed code.
- Every remaining cast sits inside a validating adapter with a reason comment.
- Every switch over a union has a `never` exhaustiveness check.
- Public functions declare their return types.
