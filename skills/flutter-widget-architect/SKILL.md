---
name: Flutter Widget Architect
description: Architects Flutter widget trees and Riverpod or Bloc state so rebuilds are scoped, build() stays pure, and const widgets skip recomposition. Use when someone asks "why is my Flutter screen janky", "should I use Riverpod or Bloc", "my whole page rebuilds when one field changes", or is building or refactoring Flutter screens, wiring StatelessWidget/StatefulWidget, Consumer, or BlocBuilder. Do NOT use for Android Jetpack Compose UI - use jetpack-compose-builder instead; do NOT use for React Native - use react-native-pro instead.
---

# Flutter Widget Architect

Structure Flutter widget trees and state so rebuilds start and stop exactly where intended and build() is a pure, cheap function of state. The costly failure this prevents is the rebuild storm: one text field changes, the entire screen re-runs build(), the frame budget blows past 16ms (60fps) or 8ms (120fps), and the user sees jank that profiling later traces to architecture, not to any single slow line.

## Operating procedure

Order matters: decompose before optimizing subscriptions, because `.select` on a monolithic build() buys nothing.

### Step 1: Gather inputs

Before restructuring a screen, establish:

1. The screen or widget in question and where jank or excessive rebuilds appear (default: run DevTools rebuild tracking or `debugPrintRebuildDirtyWidgets` first and capture which widgets rebuild).
2. Existing state management - Riverpod, Bloc, provider, setState, or a mix (a mix inside one feature is itself the finding).
3. Which state is local-ephemeral (a toggle, one text field) vs shared/async/tested.
4. List sizes - anything that can exceed roughly 30 items must be lazy.

Label any performance claim not backed by DevTools as a guess.

### Step 2: Decompose into widget classes, not methods

Split a deep build() into small `StatelessWidget`/`StatefulWidget` subclasses. Never extract subtrees into `_buildX()` helper methods - a helper always re-runs with its enclosing build(), while a separate `const` widget is skipped entirely when its inputs are unchanged. Mark `const` everywhere the analyzer allows; every const constructor is a subtree the framework never revisits.

Bad - the header re-runs every time the counter changes, because it is just a method on the same build:

```dart
class ProfileScreen extends ConsumerWidget {
  Widget build(BuildContext context, WidgetRef ref) {
    final count = ref.watch(counterProvider);
    return Column(children: [
      _buildHeader(),            // re-executes on every count change
      Text('$count'),
    ]);
  }
  Widget _buildHeader() => Row(children: [Icon(Icons.person), Text('Profile')]);
}
```

Good - the header is a const class; the framework short-circuits it, and only the Text depending on the counter rebuilds:

```dart
class ProfileScreen extends ConsumerWidget {
  Widget build(BuildContext context, WidgetRef ref) {
    final count = ref.watch(counterProvider);
    return Column(children: [
      const _ProfileHeader(),    // const: skipped when inputs unchanged
      Text('$count'),
    ]);
  }
}

class _ProfileHeader extends StatelessWidget {
  const _ProfileHeader();
  Widget build(BuildContext context) =>
      const Row(children: [Icon(Icons.person), Text('Profile')]);
}
```

### Step 3: Keep build() pure

build() must read state and return widgets - no network calls, timers, setState, or controller allocation. Create `AnimationController`, `TextEditingController`, `StreamSubscription`, and similar in `initState` or a provider, and dispose every one in `dispose`. A controller allocated in build() leaks one instance per rebuild.

### Step 4: Pick one state tool and scope it

- Riverpod for dependency-injected, testable state; use `autoDispose` for screen-scoped data so leaving the screen releases it.
- Bloc for explicit event-to-state transitions over an auditable stream.
- `StatefulWidget` + `setState` for purely local ephemeral UI state (a toggle, one text field) - do not reach for a global store.
- Never mix Riverpod and Bloc inside one feature; two sources of truth for one screen is a bug factory.

### Step 5: Watch the narrowest unit

Subscribe to one field with `ref.watch(provider.select((s) => s.field))` or a Bloc `buildWhen`, never the whole model - watching the model rebuilds on every unrelated field change. Use `ref.read` for one-shot actions inside callbacks, never to react to changes.

### Step 6: Stop rebuilds at the boundary

Wrap only the changing subtree in `Consumer`/`BlocBuilder`/`ValueListenableBuilder`, and leave the static shell (AppBar, background, scaffold chrome) outside it. Pass `child` through builders to reuse subtrees the rebuild does not touch.

### Step 7: Build lists lazily with stable keys

Use `ListView.builder`/`SliverList` so off-screen items are never built; a mapped `Column` or `ListView(children: [...])` builds every item up front. Give items a `ValueKey` on the model id so element and state survive reorder.

### Step 8: Bound your layout

Wrap flexible children in `Expanded`/`Flexible` inside `Column`/`Row` to prevent unbounded-height constraints, overflow errors, and layout thrash.

## Deliverable

Produce a restructured screen containing: widget-class decomposition with const constructors, controllers created in `initState`/providers and disposed, one state tool scoped per Step 4, `.select`/`buildWhen` on every subscription, builders wrapping only changing subtrees, lazy keyed lists, and a before/after DevTools rebuild capture proving the static shell no longer rebuilds.

## Do NOT

- Do NOT extract subtrees into `_buildX()` helper methods that return `Widget` - they cannot be skipped, const classes can.
- Do NOT do work in build() (I/O, timers, allocation, setState) - build() may run every frame.
- Do NOT `ref.watch` a whole model when you depend on one field; use `.select`/`buildWhen`.
- Do NOT call `setState` on a large `State` to update one value; isolate it with `ValueListenableBuilder` or a scoped provider.
- Do NOT leave a `ListView`/`Column` building every item or with unbounded height.
- Do NOT introduce Riverpod and Bloc into the same feature.

## Quality bar

- The static shell does not rebuild when a single value changes - verified with `debugPrintRebuildDirtyWidgets` or DevTools rebuild tracking, not by inspection.
- Every controller, subscription, and `AnimationController` created is disposed; `flutter analyze` reports no dispose or `prefer_const_constructors` warnings.
- Long or unbounded lists use a lazy builder, never a mapped `Column`/`ListView(children: [...])`.
- State scope matches sharing: local state stays in `StatefulWidget`; shared/async/tested state lives in a provider or Bloc.
- Frames stay inside the 16ms budget (8ms at 120Hz) on a profile build for the interaction that was janky.
