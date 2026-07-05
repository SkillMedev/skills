---
name: Animation System
description: Builds a coherent UI motion system — a duration token scale, named easing curves, choreography rules, and a per-component mapping — so every transition in the product draws from one vocabulary instead of ad-hoc values. Use when someone asks "define our motion tokens", "our animations feel inconsistent", "what duration should this modal use", "set up easing standards", or is starting a design system and needs the motion layer. Do NOT use to spec the motion of one specific component or flow — use motion-spec instead; for animated type treatments, use kinetic-typography.
---

# Animation System

Without a motion system, every engineer picks a duration and curve from memory, and the product accumulates dozens of near-identical transitions that read as sloppy even when no single one is wrong. This skill produces the token set and rules that make motion feel like one hand designed it — and prevents the expensive alternative: auditing and re-timing every transition after launch.

Motion must do a job: direct attention, show cause and effect, express hierarchy, or soften a change. A motion that does none of these gets cut — it is latency wearing a costume.

## Operating procedure

Durations come before easings, and both come before choreography, because choreography rules reference the tokens.

### Step 1: Gather inputs

1. Brand feel on a calm↔energetic axis — it shifts the scale ±50ms and decides whether springs are allowed.
2. Platforms (web, iOS, Android) and the animation stack (CSS, Framer Motion, native).
3. The largest animated surfaces (full-page transitions? sheets?) — they set the slow end of the scale.
4. Existing timing values in the codebase, to map onto tokens rather than orphan.
5. Token naming convention already in use, so motion tokens match it.

### Step 2: Define the duration scale

Small scale, tiered, with each tier tied to element size and travel distance — duration is proportional to distance and size (small/near = fast, large/far = slow):

- `instant`: 0ms — state changes with no transition (checkbox tick).
- `fast`: 100ms — hovers, small toggles, color changes.
- `base`: 200ms — most component transitions; the default when unsure.
- `slow`: 300ms — modals, sheets, page-level transitions.
- `deliberate`: 500ms — large or first-run reveals only.

Red line: never exceed ~500ms for interactive feedback — beyond that the UI reads as sluggish, and users start double-clicking. Interactive acknowledgment (the first visible response to input) must land within 100ms even when the full transition is longer.

### Step 3: Define easing curves

- `ease-out` — `cubic-bezier(0, 0, 0.2, 1)` — elements **entering**: fast start, gentle stop; arrives eagerly.
- `ease-in` — `cubic-bezier(0.4, 0, 1, 1)` — elements **exiting**: they accelerate away.
- `ease-in-out` — `cubic-bezier(0.4, 0, 0.2, 1)` — elements **moving** on screen from A to B.
- `spring` — playful, physical interactions (drag release, bouncy toggles); only if Step 1 said the brand supports it.

Enter/exit asymmetry is a principle, not a preference: entrances decelerate in and get more time; exits accelerate out and take roughly 20–30% less duration than the matching entrance, because users need to see something arrive but only need to notice it leave.

### Step 4: Define choreography rules

- **Stagger** list items by 20–40ms so they cascade instead of flashing as a block.
- **Anchor** transitions to their trigger — a menu grows from the button that opened it, showing cause and effect.
- The **most important** element animates first; supporting elements follow.
- Never animate more than ~5 things independently at once; group the rest into one moving unit.

### Step 5: Constrain animatable properties

Animate GPU-friendly properties only: `transform` and `opacity`. Ban animating `width`, `height`, `top`, `left`, and `margin` — they trigger layout and stutter on mid-range devices. Encode this in the token documentation so it survives code review.

### Step 6: Honor reduced motion — mandatory, not optional

Every motion the system defines must have a reduced-motion path. Vestibular disorders make large parallax, zoom, and slide motions physically nauseating for some users; `prefers-reduced-motion` is an accessibility requirement on par with contrast.

```css
@media (prefers-reduced-motion: reduce) {
  * { animation-duration: 0.01ms !important; transition-duration: 0.01ms !important; }
}
```

Replace large motions with simple cross-fades rather than removing feedback entirely — the user still needs to see that something changed.

### Step 7: Ship the token set and component mapping

Publish the system as named tokens plus a per-component mapping (component → what animates → duration token → easing token). The mapping is what stops drift: an engineer building a new dropdown copies the menu row, not a number from memory.

## Worked token set (copy-paste artifact)

```javascript
// motion.tokens.js — single source of truth for UI motion
export const motion = {
  duration: {
    instant: '0ms',
    fast: '100ms',      // hovers, toggles, color changes
    base: '200ms',      // default component transition
    slow: '300ms',      // modals, sheets, page transitions
    deliberate: '500ms',// large reveals only — hard ceiling
  },
  easing: {
    enter: 'cubic-bezier(0, 0, 0.2, 1)',   // decelerate in
    exit: 'cubic-bezier(0.4, 0, 1, 1)',    // accelerate out
    move: 'cubic-bezier(0.4, 0, 0.2, 1)',  // on-screen A→B
  },
  stagger: { list: '30ms' },
}

// Per-component mapping — extend one row per new component
export const componentMotion = {
  tooltip:  { animates: 'opacity',            in: ['fast', 'enter'], out: ['fast', 'exit'] },
  dropdown: { animates: 'opacity, transform', in: ['base', 'enter'], out: ['fast', 'exit'] },
  modal:    { animates: 'opacity, transform', in: ['slow', 'enter'], out: ['base', 'exit'] },
  toast:    { animates: 'transform, opacity', in: ['base', 'enter'], out: ['base', 'exit'] },
  pageNav:  { animates: 'opacity, transform', in: ['slow', 'enter'], out: ['base', 'exit'] },
}
```

Read the mapping: every exit is one tier faster than or equal to its entrance (the asymmetry rule), nothing exceeds `slow` except deliberate reveals, and only `transform`/`opacity` appear in `animates`.

Bad: modal opens with `all 400ms ease` — animates layout properties, uses a symmetric curve, and exits as slowly as it enters.
Good: modal opens `opacity, transform 300ms cubic-bezier(0,0,0.2,1)`, closes `200ms cubic-bezier(0.4,0,1,1)`.

## Deliverable

Produce a motion system document containing: the duration scale with usage guidance per tier, the easing set with enter/exit/move assignments, choreography rules (stagger, anchoring, priority, the 5-element cap), the animatable-property constraint, the reduced-motion fallback, and the per-component mapping table — implementable by engineering without further design input.

## Do NOT

- Do not exceed ~500ms for anything interactive — sluggishness reads as brokenness.
- Do not use one symmetric easing everywhere — enter and exit have different jobs and different curves.
- Do not give exits as much time as entrances; exits are noticed, not watched.
- Do not animate layout properties (`width`, `height`, `top`, `left`) — they stutter; use `transform`.
- Do not ship any motion without a reduced-motion path — it is an accessibility failure, not a polish gap.
- Do not let components carry inline timing values — every value outside the token set is future drift.
- Do not keep a motion that has no job (attention, causality, hierarchy, or softening); decoration is latency.

## Quality bar

- Every duration and curve in the product maps to a named token; a codebase grep for `cubic-bezier` or `ms` outside the token file returns nothing new.
- Every entrance uses a decelerating curve; every exit accelerates and is no slower than its entrance.
- Reduced-motion is verified by flipping the OS setting, not assumed.
- The component mapping covers every animated component that exists today, and adding a component means adding one row.

For the motion details of one specific component or flow — exact keyframes, sequencing diagrams, handoff redlines — route to motion-spec; the system defined here is its vocabulary. For expressive animated typography, route to kinetic-typography.
