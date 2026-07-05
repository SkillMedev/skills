---
name: Rust Patterns
description: Writes and reviews idiomatic Rust — ownership and borrowing decision rules for API signatures, lifetime avoidance strategies, thiserror vs anyhow error design, iterator-first style, and when Rc/RefCell/Arc/Mutex are actually justified — with good/bad code pairs. Use when someone asks "why won't this borrow check", "should this take &str or String", "thiserror or anyhow", "how do I share state between threads in Rust", or their code is a wall of clone() calls. Do NOT use for cross-language migration planning — use language-version-migrator instead — and do NOT use for generic API endpoint design — use api-design instead.
---

# Rust Patterns

The borrow checker is a design-feedback tool: when it fights you, the ownership story of the code is unclear, and the fix is almost always restructuring, not sprinkling `clone()` until it compiles. The costly mistake this skill prevents is exactly that clone-and-`unwrap()` dialect — code that compiles, ships, then panics in production or silently copies megabytes per request.

## Operating procedure

### Step 1: Gather inputs

1. Binary or library? Libraries need typed errors and conservative signatures; binaries can be looser.
2. Threading/async story: single-threaded, threaded, or async (which runtime — assume Tokio unless told otherwise).
3. Performance sensitivity: hot path or glue code. Label guesses as guesses; don't optimize glue.
4. Existing conventions (error crate in use, MSRV constraints stated by the user).

### Step 2: Decide ownership per signature — the four-question rule

For every function parameter, in order:

1. Only reads? Take `&T` — and prefer the borrowed view type: `&str` over `&String`, `&[T]` over `&Vec<T>`, `&Path` over `&PathBuf`. This makes the API maximally callable.
2. Mutates in place? Take `&mut T`.
3. Stores or consumes the value (moves it into a struct, sends it to a channel, returns it)? Take `T` by value — taking `&T` and cloning inside hides the cost from the caller; taking `T` lets callers who are done with the value move it for free.
4. Sometimes borrows, sometimes owns? `impl Into<String>` / `Cow<'_, str>` for ergonomic APIs — but only at public boundaries; internally, pick one.

Return owned values from constructors. Default to borrowing everywhere else. If a signature needs a lifetime annotation you can't explain in one sentence, restructure instead: most lifetimes should stay elided.

### Step 3: Choose the sharing tier — escalate only on proof

Work down this ladder and stop at the first rung that works; each step adds runtime cost and API noise.

1. Plain ownership + borrows (the default, ~95% of code).
2. `Rc<T>` — single-threaded shared ownership (e.g. a graph node referenced by several parents).
3. `Rc<RefCell<T>>` — add interior mutability; runtime borrow panics replace compile errors, so keep borrow scopes tiny.
4. `Arc<T>` / `Arc<Mutex<T>>` (or `RwLock`) — cross-thread. In async code, never hold a `std::sync::Mutex` guard across an `.await`; use the runtime's mutex or restructure so the guard drops first.

Prefer owning data in structs over storing references with lifetimes — a struct with a lifetime parameter infects every type that contains it. Self-referential structs are a design smell: use indices into a `Vec`, or an arena, not `Pin` gymnastics.

### Step 4: Design errors by audience

- Library: `thiserror` — a typed enum, `#[error(...)]` messages, `#[from]` for source errors. Callers must be able to match on variants.
- Application: `anyhow` — `Result<T, anyhow::Error>` with `.context("reading config")` at each layer, so failures carry a breadcrumb trail.
- `?` for all recoverable errors; `panic!`/`expect` only for invariant violations that indicate a bug, and `expect("why this cannot fail")` with a reason, never bare `unwrap()` on production paths.

```rust
use thiserror::Error;

#[derive(Error, Debug)]
enum AppError {
    #[error("not found: {0}")]
    NotFound(String),
    #[error(transparent)]
    Io(#[from] std::io::Error),
}

fn load(path: &str) -> Result<String, AppError> {
    Ok(std::fs::read_to_string(path)?)
}
```

### Step 5: Apply the idiom checklist

- Iterators and combinators (`map`, `filter`, `collect`) over index loops — they eliminate bounds-check noise and off-by-ones.
- `Option`/`Result` combinators (`map`, `and_then`, `ok_or`, `unwrap_or_else`) over nested `match`; but a `match` is still clearer than a four-combinator chain.
- Newtypes for domain values (`struct UserId(u64)`) so the compiler catches transposed arguments; implement `From` for conversions; derive `Debug, Clone, PartialEq` where sensible.
- Make illegal states unrepresentable: an enum with `Connected(Session)` / `Disconnected` beats a struct with `is_connected: bool` and a nullable session.
- Generics for hot-path polymorphism (monomorphized, inlined); `dyn Trait` for heterogeneous collections and to keep compile times and binary size down at cold boundaries.

### Step 6: Performance and hygiene passes

- Grep for `clone()`: each one is either justified (write a word of why), replaced by a borrow, or replaced by a move.
- `Vec::with_capacity` when size is known; avoid collecting intermediates you immediately iterate again.
- Profile before optimizing anything beyond these defaults.
- `unsafe` stays tiny, wrapped in a safe API, with a `// SAFETY:` comment stating the invariant.
- `cargo clippy -- -D warnings` and `cargo fmt --check` run in CI, not just locally.

## Worked artifact: signature and sharing, bad/good

Bad — fights the checker, hides costs, panics:

```rust
fn process(names: Vec<String>, cfg: &Config) -> Vec<String> {
    let mut out = Vec::new();
    for i in 0..names.len() {
        let n = names[i].clone();                  // clone to dodge a move error
        out.push(format!("{}-{}", cfg.prefix.clone(), n));
    }
    out
}
let shared = Arc::new(Mutex::new(state));          // single-threaded code!
let v = map.get("k").unwrap().clone();             // panic + copy
```

Good:

```rust
fn process<'a>(names: impl Iterator<Item = &'a str>, prefix: &str) -> Vec<String> {
    names.map(|n| format!("{prefix}-{n}")).collect()
}
let shared = Rc::new(RefCell::new(state));          // right tier for one thread
let v = map.get("k").ok_or(AppError::NotFound("k".into()))?;
```

The good version borrows what it reads, allocates only the output it must own, uses the cheapest sharing tier, and turns the panic into a typed error.

## Deliverable

Produce code (or a review) where every signature is justified by the four-question rule, sharing primitives are at the lowest sufficient tier, the error strategy matches the audience (thiserror for libraries, anyhow for apps), and every remaining `clone()`, `expect`, and `unsafe` block carries its one-line justification.

## Do NOT

- Do not `clone()` to silence a borrow error — restructure ownership; the checker is pointing at a real design ambiguity.
- Do not take `&String`/`&Vec<T>`/`&PathBuf` in signatures; the borrowed view types cost nothing and accept more callers.
- Do not reach for `Arc<Mutex<T>>` in single-threaded code, or `Rc<RefCell<T>>` as a default — they are escalations, not conveniences.
- Do not hold a sync mutex guard across `.await` — it deadlocks or blocks the executor.
- Do not `unwrap()` on production paths; `?` or `expect("reason")`.
- Do not store references in structs when owning is feasible; lifetime parameters propagate to every containing type.
- Do not block the async executor with sync I/O; use the runtime's `spawn_blocking` for it.

## Quality bar

- `cargo clippy -- -D warnings` clean; `cargo fmt` clean.
- No bare `unwrap()` outside tests; every `expect` states why it can't fail.
- Every `clone()` in the diff is annotated or eliminated.
- Public API takes borrowed view types and returns owned values.
- `unsafe` blocks each have a `// SAFETY:` invariant comment.

## Escalation

For migrating a codebase between language versions or from another language, use language-version-migrator. Async architecture questions beyond Rust idiom (queues, services) route to microservices or kafka-pipelines; API surface design to api-design.
