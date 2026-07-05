---
name: Mobile Perf Profiler
description: Diagnoses and fixes mobile jank, dropped frames, slow startup, and growing memory by capturing a trace or heap snapshot on a real device, isolating the single worst cost, fixing it, and re-measuring against the 16ms frame and cold-start budgets. Use when someone says "the app stutters when I scroll", "startup takes forever", "memory keeps climbing after navigating around", or the UI feels laggy on a real device. Do NOT use when the bottleneck is a website or web app (LCP, bundle size, slow endpoint or query) - use web-performance instead; for Compose recomposition fixes pair with jetpack-compose-builder; for Flutter widget architecture use flutter-widget-architect.
---
# Mobile Perf Profiler

Diagnose mobile performance from measured evidence, fix the largest cost, and re-measure - never guess. The costly failure is folklore optimization: weeks spent caching and micro-tuning paths the profiler would show are cheap, while the one 40ms bind on the scroll path ships untouched.

## Operating procedure

### Step 1: Gather inputs

1. The exact symptom, named precisely: jank while scrolling a specific list, jank on first frame of a screen, slow cold start, or memory that climbs. "The app feels slow" is not a symptom yet - reproduce until it is one.
2. A representative device: a real mid-tier phone from the install base, not the developer's flagship and never a simulator/emulator.
3. A release or profile build. Debug builds misreport timing by integer factors.
4. The display refresh rate of target devices - 60Hz means a 16.6ms frame budget, 120Hz means 8.3ms.
5. Field data access: Play Console Android vitals, Xcode Organizer / MetricKit. If available, this decides which symptom is worth the trace before any local work.

### Step 2: Work the evidence ladder, cheapest first

Escalate only when the cheaper rung has not localized the cause:

1. Field data (minutes, zero setup): Android vitals and Xcode Organizer already know the slow-frame rate, frozen-frame rate, cold-start percentiles, and which devices are worst. Confirm the reported symptom exists at scale and pick the worst screen/device class.
2. On-device overlays (seconds to enable): Android GPU rendering profile bars, Flutter performance overlay. Watch the bars while reproducing - this tells which screen and roughly which frames blow the budget.
3. Trace capture (minutes per run): iOS Instruments - Time Profiler (CPU), Animation Hitches (rendering); Android Perfetto/system tracing; Flutter DevTools timeline reading the UI-thread vs raster-thread split. This names the expensive function or phase.
4. Heap snapshot diff (minutes per run): iOS Allocations and Leaks; Android Studio Memory Profiler and LeakCanary. Only for memory symptoms.
5. Repeatable benchmarks (hours to wire): Android Macrobenchmark, XCTest performance metrics. Reserve for regression-gating a fix in CI, not for initial diagnosis.

### Step 3: Locate the single worst offender in the trace

Find the frames that miss the budget and the single most expensive thing they do. For Flutter, read which thread is blown first: raster-thread jank is overdraw or shader compilation; UI-thread jank is Dart build/layout. For memory, diff a heap snapshot before and after the repro loop. Do not change code until the evidence points at one cause.

### Step 4: Apply the fix that matches the measured cause

- Over-rendering: stop rebuilding/recomposing subtrees that did not change (in Compose, hand recomposition fixes to jetpack-compose-builder); move allocation, parsing, and I/O off the build/measure/onDraw path; cache derived values; flatten the view hierarchy; reduce overdraw from stacked opaque layers.
- Long lists: virtualize with cell reuse (RecyclerView, LazyColumn, UICollectionView, ListView.builder) so only visible rows exist; give stable keys/ids and fixed or estimated item sizes; paginate the data source.
- Slow startup: defer non-critical init off the critical path (lazy dependency graphs, background warmup after first frame); trim Application/AppDelegate work; warm or simplify shader compilation and cold layout inflation for first-frame jank.
- Leaks: diff before/after heap snapshots for objects retained that should be gone - undisposed controllers/streams/subscriptions, listeners never removed, static or singleton references to a destroyed Activity/ViewController, unbounded image caches. Decode images at display size, not source size.

### Step 5: Re-measure on the same device and build

Confirm the symptom is gone against the budget on the same device, build type, and repro steps - not against a microbenchmark. If the fix matters, pin it with a Macrobenchmark/XCTest metric in CI so it cannot silently regress.

## Budgets and red lines

- Frame budget: 16.6ms per frame at 60Hz, 8.3ms at 120Hz. Jank is a single missed deadline the user sees, not an average.
- Excessive slow frames (Android vitals red line): more than 50 percent of frames over 16ms. Excessive frozen frames: more than 0.1 percent of frames over 700ms.
- Cold start: Android vitals flags cold starts at or over 5s, warm at or over 2s, hot at or over 1.5s. Target cold start under 2s on a mid-tier device; a cold start over 5s is a launch blocker, not a nice-to-have.
- Memory: flat under repeated navigation (push a screen, pop it, 10+ times). Any monotonic growth across that loop is a leak, regardless of absolute size.

## Worked artifact: trace-reading decision example

Symptom: fling on the order-history list drops frames on a mid-tier Android device.

```
Evidence ladder:
1. Vitals: order screen worst; slow-frame rate 31% on this device class.
2. GPU bars: green (input/draw) fine; long blue/purple segments during fling
   -> frame time spent in UI-thread work per row, not GPU.
3. Perfetto trace, worst frame = 41ms:
   RecyclerView onBindViewHolder          38.2ms
     SimpleDateFormat.<init> + format     22.1ms   <- allocation + parse per bind
     Glide decode (full-size bitmap)      11.4ms   <- decoding at source size
     remaining bind work                   4.7ms
Verdict: fix date formatting first (54% of the frame), image decode second.
Fix: preformat dates in the data layer; request display-size decode.
Re-measure, same device/build: worst frame 9.8ms; vitals slow-frame rate 4%.
```

The order of fixes follows the measured cost split, not intuition - image loading "feels" like the culprit, but the trace says dates.

## Deliverable

Produce a performance case file: the named symptom and repro steps, device and build used, the evidence at each ladder rung climbed, the trace or snapshot excerpt naming the worst cost with its milliseconds, the single fix applied, and the before/after measurement against the budget - plus the CI benchmark pinning the win where one was wired.

## Quality bar

- Every claim traces to a capture: no fix without the trace or snapshot line that motivated it, no "done" without the re-measurement.
- Measurements come from a release/profile build on a real representative device.
- Success is every frame holding the 16.6ms (or 8.3ms) budget on that device, cold start under the vitals thresholds, and memory flat across a 10x navigation loop.
- Fixes were applied in measured-cost order, largest first, one at a time so the re-measurement attributes the win.

## Do NOT

- Do NOT prescribe a fix without a captured trace or heap snapshot - no folklore optimizations.
- Do NOT profile a debug build, a simulator/emulator, or a flagship device and treat the numbers as real.
- Do NOT skip the cheap rungs: field vitals and overlays localize the symptom before a single trace is captured.
- Do NOT micro-optimize a path the profiler shows is cheap; readability beats a nanosecond no user perceives.
- Do NOT chase averages or synthetic benchmarks instead of the frames that actually miss the budget.
- Do NOT load the full dataset to render a screenful of rows.
- Do NOT fix web-side latency here - a slow endpoint, query, or page render is web-performance territory; the mobile trace will show the wait as idle network time, which is the handoff signal.
