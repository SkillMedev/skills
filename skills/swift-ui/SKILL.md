---
name: SwiftUI Expert
description: Builds clean, performant, accessible SwiftUI views with correct state ownership, scoped invalidation, and smooth list scrolling, and reviews existing SwiftUI code against a concrete frame-time and re-render budget. Use when someone asks "why does my SwiftUI list stutter", "should this be @State or @Observable", "my whole screen re-renders when one row changes", "how do I animate this transition", or wants a SwiftUI view built or refactored. Do NOT use for cross-platform React Native apps - use react-native-pro instead; do NOT use for Flutter widget trees - use flutter-widget-architect instead; do NOT use for Android Compose UIs - use jetpack-compose-builder instead.
---

# SwiftUI Expert

A SwiftUI screen lives or dies on state ownership: put state in the wrong place and every keystroke re-evaluates the whole tree, lists stutter, and animations tear. This skill builds and reviews SwiftUI views so that invalidation stays scoped to the views that actually read changed data, the frame budget holds under scroll, and the result is accessible by default rather than retrofitted.

## Operating procedure

Work in this order because state design determines everything downstream - fixing state after the view hierarchy is built means rewriting the hierarchy.

### Step 1: Gather inputs

Collect before writing any view code. Where the user does not know, use the default and label it a guess.

- Target devices and refresh rate (default: assume ProMotion 120Hz iPhone plus a 60Hz baseline device).
- Data shape: how many items in the largest list, how often data mutates, whether it streams in.
- Ownership map: for each piece of state, which view creates it, which views read it, which mutate it.
- Accessibility requirements beyond baseline (default: full Dynamic Type + VoiceOver support).

### Step 2: Choose state ownership per value

Pick the narrowest wrapper that works:

- `@State` - local, view-owned, value-type state. Also the correct owner for an `@Observable` model instance created by the view.
- `@Binding` - a two-way reference to state owned elsewhere. Pass a binding only when the child mutates; pass a plain value when it only reads.
- `@Observable` (Observation framework) - model objects; views observe only the properties they read, so invalidation is per-property, not per-object.
- `@Environment` - dependency injection down the tree for services and shared models.

Rule: state lives at the lowest common ancestor of every view that reads or writes it - no lower (it resets when the view disappears) and no higher (it widens invalidation).

### Step 3: Compose views to scope invalidation

- Keep `body` small; extract subviews so a change invalidates one row, not the screen. A `body` that exceeds roughly 50 lines or three levels of nesting is a split candidate.
- Pass minimal data into subviews - pass `item.title`, not the whole store - so only affected views re-render.
- Use `@ViewBuilder` helpers instead of giant nested closures.
- Never do heavy work (formatting, sorting, filtering, decoding) in `body`; compute it in the model and hand the view finished values. `body` must stay in the microsecond range because it can run every frame during animation.

### Step 4: Lists and the frame budget

The budget is 8.3ms per frame at 120Hz, 16.7ms at 60Hz - everything on the main thread for that frame, not just your row. Practical rules:

- Use `List` or `LazyVStack` for anything above ~50 rows so rows render on demand; a plain `VStack` in a `ScrollView` builds every row up front.
- Give items stable `id`s from your data (`item.id`), never array indices or `UUID()` computed in `body` - unstable ids destroy row identity, causing flicker, lost scroll position, and lost row state.
- Rows must be constant-cost: no per-row date formatters or image decoding; cache formatters, use `AsyncImage` or a caching image pipeline.
- Verify with Instruments' SwiftUI template: watch view-body evaluation counts while scrolling. A row type whose body evaluates when unrelated state changes is a state-ownership bug - fix Step 2, do not memoize around it.

### Step 5: Animate from state

- Drive animation from state: `withAnimation { model.expanded.toggle() }`.
- Prefer `.animation(_:value:)` scoped to a specific value over implicit global animations, which animate things you did not intend.
- Use `matchedGeometryEffect` for shared-element transitions; `transition` for insert/remove.
- Respect `accessibilityReduceMotion`: replace movement with opacity fades when it is set.

### Step 6: Accessibility pass

- Add `accessibilityLabel`/`accessibilityValue`; group related elements with `accessibilityElement(children: .combine)` so a row reads as one sentence, not five fragments.
- Respect Dynamic Type - system text styles, no fixed frames that clip text; verify at the largest accessibility size.
- Check contrast and add `#Preview` variants for dark mode, large Dynamic Type, and empty/loading/error states with sample data.

## Worked artifact: state ownership, bad vs good

Bad - the whole screen invalidates on every keystroke because the search text lives in a model the entire tree observes, and rows receive the whole store:

```swift
@Observable final class ScreenStore {
  var searchText = ""
  var items: [Item] = []
}

struct ContactList: View {
  @State private var store = ScreenStore()
  var body: some View {
    VStack {
      TextField("Search", text: $store.searchText)
      ForEach(store.items) { item in
        ContactRow(store: store, item: item) // row observes everything
      }
    }
  }
}
```

Good - search text is view-local, rows get only the values they read, and the list is lazy with stable ids:

```swift
struct ContactList: View {
  @State private var searchText = ""
  @Environment(ContactModel.self) private var model
  var body: some View {
    List(model.filtered(searchText)) { item in
      ContactRow(name: item.name, isOnline: item.isOnline)
    }
    .searchable(text: $searchText)
  }
}

struct ContactRow: View {
  let name: String
  let isOnline: Bool
  var body: some View {
    Label(name, systemImage: isOnline ? "circle.fill" : "circle")
  }
}
```

Typing in the good version invalidates the `List` content closure and the rows whose filtered membership changed - nothing else.

## Deliverable

Produce working SwiftUI view code plus a one-paragraph state map stating, for each piece of state: its owner, its wrapper, and which views read vs write it. For reviews, produce a findings list ordered by frame-budget impact, each with the fix.

## Do NOT

- Never mutate state during `body` evaluation - it loops or triggers undefined-behavior warnings.
- Do not do async work in `onAppear` with a raw `Task` - use `.task {}`, which cancels automatically on disappear.
- Do not put view types (`Color`, `Image`, `AnyView`) in models; models must stay testable without UI.
- Do not lift all state to one screen-level store "for simplicity" - that is exactly the pattern that makes every keystroke redraw the screen.
- Do not paper over invalidation bugs with `equatable()` or caching before fixing ownership; the bug will resurface in the next view that observes the same object.
- Do not use fixed frames on text - Dynamic Type will clip it.

## Quality bar

A finished view passes when:

- Every piece of state has exactly one owner and the narrowest wrapper that works.
- Instruments shows row bodies evaluating only when their own data changes.
- Scrolling the largest expected list holds the frame budget on the baseline 60Hz device.
- Long-lived state is lifted above any view that can disappear, so it survives navigation.
- Keyboard avoidance and safe areas are handled (`safeAreaInset`, `scrollDismissesKeyboard`).
- VoiceOver reads each interactive element with a meaningful label; the layout survives the largest Dynamic Type size; `reduceMotion` is respected.
- Previews exist for loading, empty, error, dark mode, and large-type states.

For app-wide profiling beyond a single screen, pair with mobile-perf-profiler.
