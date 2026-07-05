---
name: Jetpack Compose Builder
description: Builds Android Jetpack Compose UI with hoisted state, unidirectional data flow, and recomposition-safe patterns backed by measured stability rules. Use when someone asks "build this screen in Compose", "where should this state live", "why does my LazyColumn stutter", or is writing Composable functions, ViewModel and StateFlow wiring, or fixing recomposition jank in Kotlin. Do NOT use when the screen targets Flutter - use flutter-widget-architect instead; for iOS declarative UI use swift-ui; for React Native screens use react-native-pro; and if jank persists after state fixes, profile with mobile-perf-profiler.
---
# Jetpack Compose Builder

Build the state and data flow first, the pixels second - most Compose bugs are state-ownership and recomposition bugs, not layout bugs. The costly failure is a screen that works in a demo, then recomposes entire subtrees on every keystroke or scroll frame because state lives in the wrong place and parameters are unstable.

## Operating procedure

### Step 1: Gather inputs

Before writing a composable, establish:

1. The screen's state shape: what the user sees, what can change, and what triggers each change.
2. Which state is UI-local (text field focus, expanded flags) versus screen-level (loaded data, selection) versus app-level (auth, theme).
3. The data source: ViewModel exposing StateFlow, or a snapshot-state holder for pure-UI screens.
4. List characteristics if any: item count, item identity (stable server ids?), reorder/insert behavior.
5. Refresh-rate target of the flagship devices in the install base (60Hz gives a 16.6ms frame budget, 120Hz gives 8.3ms).

### Step 2: Map state ownership before drawing anything

Decide which state is UI-local (remember) versus screen-level (ViewModel exposing StateFlow, collected via collectAsStateWithLifecycle()). Hold mutable state in the lowest common ancestor that needs it - no lower (parent cannot drive it) and no higher (recomposes too wide a scope).

### Step 3: Make composables stateless by default

Pass immutable state down as parameters; emit changes up via lambdas (onValueChange, onClick). A composable that owns its own remember { mutableStateOf } cannot be controlled, previewed, or tested by its parent - hoist it. Every leaf composable must render in a @Preview without a ViewModel.

### Step 4: Remember with correct keys

Use remember to cache allocations and computations across recompositions, rememberSaveable to survive config change and process death. Key remember, LaunchedEffect, and derivedStateOf on the inputs they depend on; LaunchedEffect(Unit) where an id should restart the effect is a bug. Wrap derived values whose input changes faster than the output (scroll offset to "show FAB" boolean) in derivedStateOf so downstream recomposition happens only when the result changes.

### Step 5: Confine side effects to effect handlers

LaunchedEffect for scoped suspend work, rememberCoroutineScope for event-driven launches, DisposableEffect for cleanup, SideEffect to publish Compose state to non-Compose code. Never launch coroutines or write state in the composable body - state writes during composition throw or loop.

### Step 6: Build lists with Lazy layouts and stable keys

Use LazyColumn/LazyRow with a stable item key (server id, not index) so reorders animate and item state survives. Hoist scroll position with rememberLazyListState when it must be observed or controlled. Prefer slot APIs (content lambdas) over boolean flags. Never nest a lazy list inside a scrollable of the same axis.

### Step 7: Contain recomposition with the cost rules

Apply the recomposition cost rules below, cheapest first: push state reads down, defer reads past composition, then fix stability.

### Step 8: Measure before optimizing

Capture recomposition counts in Layout Inspector and skippability in the Compose compiler metrics report (enable the compiler's reports output and read the -classes.txt for "unstable" parameters). Fix the composable that actually recomposes too often. Add stability annotations only against that evidence. If frames still miss budget after recomposition is contained, move to mobile-perf-profiler for a full trace.

## Recomposition cost rules

Compose skips a composable only when the call site's arguments are all stable and unchanged. Work through these in order:

1. Read state low. A State read subscribes the reading scope. Push reads into the smallest composable that needs the value; passing State down and reading it in the leaf beats reading it at the screen root.
2. Defer reads past composition. Composition costs more than layout, which costs more than draw. Lambda-based modifiers (Modifier.offset { } instead of Modifier.offset(), Modifier.drawBehind) move a read out of composition entirely - a scroll-driven offset should never recompose anything.
3. Fix parameter stability. Unstable and skip-defeating: classes with var properties, List/Map/Set interface types (compiler cannot prove immutability - use kotlinx.collections.immutable or wrap in an @Immutable holder), and types from modules without the Compose compiler. Annotate @Immutable/@Stable only when the contract truly holds.
4. Stabilize lambdas. A lambda capturing an unstable receiver is recreated each pass and defeats skipping in hot paths; use method references or remember the lambda keyed on its captures. Do this only for measured-hot composables - everywhere is noise.
5. Budget list items. A LazyColumn row's composition should be well under 1ms on a mid-tier device; a row that allocates, parses, or formats dates inline will blow the 16.6ms frame during fling. Precompute in the ViewModel, pass display-ready strings.

Red line: if Layout Inspector shows a composable recomposing on interactions that do not concern it (typing in an unrelated field recomposes the whole list), that is the bug to fix - not a candidate for micro-tuning.

## Worked artifact: bad vs good list row

Bad - unstable params, inline work, index keys:

```kotlin
@Composable
fun MessageList(vm: ChatViewModel) {                 // child reaches into ViewModel
    val messages by vm.messages.collectAsState()
    LazyColumn {
        items(messages.size) { i ->                  // index key: reorders reset state
            val m = messages[i]
            Row {
                Text(SimpleDateFormat("HH:mm").format(m.sentAt))  // allocation + parse per recomposition
                Text(m.body)
                Button(onClick = { vm.retry(m) }) { Text("Retry") } // lambda captures vm per pass
            }
        }
    }
}
```

Good - stateless, stable keys, display-ready data, hoisted events:

```kotlin
@Immutable
data class MessageUi(val id: String, val timeLabel: String, val body: String)

@Composable
fun MessageList(
    messages: ImmutableList<MessageUi>,
    onRetry: (String) -> Unit,
    modifier: Modifier = Modifier,
) {
    LazyColumn(modifier) {
        items(messages, key = { it.id }) { m ->
            MessageRow(message = m, onRetry = onRetry)   // previews without a ViewModel
        }
    }
}
```

Time formatting moved to the ViewModel, identity keys keep row state across reorders, and every parameter is stable so unchanged rows skip.

## Deliverable

Produce the screen as a state-hoisted composable tree: an immutable UI-state class, a stateless screen composable taking state plus event lambdas, preview functions for the key states (loading, content, error), and - for any performance claim - the Layout Inspector recomposition counts or compiler-metrics lines that justify it.

## Quality bar

- Every leaf composable is stateless, parameterized, and renders in a @Preview without a ViewModel.
- State flows down as immutable values; events flow up as lambdas. No two-way ownership.
- Effects are keyed on their real inputs; no LaunchedEffect(Unit) that should restart on an id.
- Lists carry stable identity keys; no scrolling list nested in a same-axis scrollable.
- Hot-path parameters are stable per the compiler metrics report; frames hold 16.6ms (60Hz) or 8.3ms (120Hz) during scroll on a mid-tier device.
- Any optimization is backed by a measured recomposition count, not a guess.

## Do NOT

- Do NOT keep mutable UI state inside a child composable the parent must drive - hoist it.
- Do NOT launch coroutines or mutate state directly in the composable body.
- Do NOT key LaunchedEffect/remember on Unit when it should re-run on an id or input change.
- Do NOT nest a LazyColumn/LazyRow inside a verticalScroll/horizontalScroll of the same axis.
- Do NOT pass unstable params (interface collections, var-property classes, per-pass lambdas) into measured-hot composables - they defeat skipping.
- Do NOT scatter @Stable/@Immutable annotations or premature optimizations before measuring; a wrong stability promise causes stale-UI bugs.
- Do NOT rewrite working XML/View screens to chase purity; AndroidView is fine for heavy interop or established View code.
