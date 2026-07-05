---
name: React Native Pro
description: Builds and ships production React Native apps - architecture, navigation, list and startup performance against explicit budgets, native modules, and EAS release flow. Use when someone asks "why is my FlatList janky", "how do I speed up cold start", "should this be a TurboModule", "how do I set up EAS builds and OTA updates", or wants a React Native feature built to production quality. Do NOT use for Flutter apps - use flutter-widget-architect instead; do NOT use for offline-first sync and conflict resolution - use mobile-offline-sync instead; do NOT use for native-only iOS SwiftUI work - use swift-ui instead; for deep profiling sessions use mobile-perf-profiler.
---

# React Native Pro

React Native apps fail in production for predictable reasons: a JS thread saturated by re-renders, a cold start bloated by eager imports, and lists that map thousands of items into a ScrollView. This skill builds features against explicit performance budgets from day one, because retrofitting performance into a shipped RN app costs 10x what designing for it costs.

## Operating procedure

### Step 1: Gather inputs

Collect before building; default and label guesses where unknown.

- Baseline device (default: a low-end Android with 3-4GB RAM - the JS thread there is the real bottleneck, not the iPhone in your pocket).
- Largest list size and item complexity; media weight per screen.
- Expo managed/prebuild or bare workflow (default: Expo with prebuild + EAS).
- Release cadence and whether OTA updates are in play.

### Step 2: Project setup

- Prefer Expo with the managed/prebuild workflow and EAS for builds and OTA updates.
- Use the New Architecture (Fabric + TurboModules) for new apps; Hermes on.
- TypeScript everywhere; strict mode on.

### Step 3: Set the budgets

Hold these throughout; measure on the baseline Android device, release build, not the simulator.

- Cold start to interactive: under 2s on mid-tier Android, under 1.5s on iOS. Over 4s and users uninstall.
- JS thread: 60fps during scroll and gestures; sustained drops below 45fps are a blocker.
- JS bundle: audit with react-native-bundle-visualizer; treat any single dependency over ~300KB as a line item to justify. Lazy-load anything not needed for the first screen.
- App size: with Hermes and resource shrinking, a typical app lands 20-40MB per platform; investigate anything over ~60MB (usual culprits: unstripped assets, duplicate fonts, unused locales).
- Images: never ship images larger than their rendered size at 3x; use expo-image with caching. Remember decoded images cost width × height × 4 bytes of RAM regardless of file size - a single full 12MP photo decodes to ~48MB, so a list of unresized photos evicts everything else on a 3GB device.

### Step 4: Navigation

- Use React Navigation (native stack) or Expo Router (file-based).
- Type routes and params so navigation is checked at compile time.
- Keep the initial route light for fast cold start; lazy-load heavy screens. Every module imported at the entry point is parsed before first paint - move analytics, charts, and rarely-used flows behind dynamic imports.

### Step 5: Lists and re-renders

- Use FlashList (or FlatList) for anything above ~20 items; never map long arrays inside a ScrollView - that renders every item up front.
- Provide `keyExtractor` with stable ids (never index), `getItemLayout` when row height is fixed, and memoized row components (`React.memo`).
- Keep row props primitive; a new object/array/lambda per render defeats memo. Hoist callbacks with `useCallback`.
- FlatList tuning: `initialNumToRender` ≈ one screenful (8-12), `windowSize` 5-10, `maxToRenderPerBatch` ~10. With FlashList, set an accurate `estimatedItemSize` and watch blank-cell area while flinging - visible blank cells on the baseline device mean rows are too expensive.
- Run animations and gestures on the UI thread with Reanimated worklets; JS-driven `Animated` for gestures drops frames whenever the JS thread is busy.

### Step 6: Native modules

- Reach for a native module only when JS can't do it; check the ecosystem first.
- With the New Architecture, write TurboModules with a codegen spec for type-safe bridging.
- Keep the boundary quiet: batch calls, pass primitives, never marshal large objects per frame.

### Step 7: Release via EAS

- Configure `eas.json` profiles: development, preview, production.
- OTA updates for JS-only changes; store build for anything touching native code. Never OTA a JS bundle that calls native APIs the installed binary lacks - that is a guaranteed crash.
- Secrets via EAS secrets, not committed env files.
- Wrap the app in an error boundary and report crashes to a service (Sentry); a silent white screen is unrecoverable feedback-wise.

## Worked artifact: list row, bad vs good

Bad - inline lambda and object props break memoization; every parent render re-renders every row:

```tsx
<FlashList
  data={items}
  renderItem={({ item }) => (
    <Row item={item} style={{ padding: 12 }} onPress={() => open(item.id)} />
  )}
/>
```

Good - stable props, memoized row, primitive data:

```tsx
const Row = React.memo(function Row({ id, title, onPress }: RowProps) {
  return <Pressable onPress={() => onPress(id)}><Text>{title}</Text></Pressable>;
});

const handlePress = useCallback((id: string) => open(id), [open]);

<FlashList
  data={items}
  estimatedItemSize={56}
  keyExtractor={(item) => item.id}
  renderItem={({ item }) => (
    <Row id={item.id} title={item.title} onPress={handlePress} />
  )}
/>
```

## Deliverable

Produce working, typed feature code plus a short budget report: measured cold start, JS fps during the heaviest scroll, bundle-visualizer top offenders, and app size - each against its budget, with the fix for any miss.

## Do NOT

- Do not profile on the iOS simulator or a flagship phone and call it done - the failure mode lives on low-end Android release builds.
- Do not map long arrays in a ScrollView; virtualize.
- Do not handle platform differences with copy-pasted branches; use `Platform.select` or platform-specific files.
- Do not blanket-`useMemo`/`useCallback` everything; memoize where a measured re-render is expensive, or you add complexity without wins.
- Do not ship an OTA update containing native-API changes.
- Do not let deep links ship untested - configure and test universal links and app links on both platforms; they break silently.
- Do not ignore OS limits on background tasks and notifications; use Expo Notifications and respect platform scheduling constraints.

## Quality bar

- Cold start, scroll fps, bundle, and app size all measured on the baseline Android device and within budget.
- All lists virtualized with stable keys and memoized rows; no visible blank cells while flinging.
- Gesture-driven animation runs in worklets, verified smooth while the JS thread is artificially loaded.
- Routes fully typed; `tsc --noEmit` clean under strict mode.
- Error boundary + crash reporting wired; deep links verified on both platforms; Hermes enabled and unused locales/assets stripped.

Route store-listing and release-notes work to app-store-release-prep, and push campaign wiring to push-notification-wirer.
