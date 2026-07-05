---
name: Prototype Spec
description: Turns a static design into a numbered, testable, dev-ready specification covering every state, interaction, breakpoint, motion detail, and edge case the mockup cannot show. Use when someone asks "write the spec for this screen", "engineering keeps asking what happens when", "document the interactions before handoff", "what states does this component need", or a build came back wrong because behavior was never written down. Do NOT use for the motion token details themselves - use motion-spec instead; for verifying a finished build against the spec, use design-qa-checklist.
---

# Prototype Spec

A static mockup shows one moment of one state on one screen size; engineering has to invent everything else, and every invention is a coin flip that lands in QA or in production. This skill converts a design into numbered, testable behavior statements so the build matches intent the first time, instead of surfacing as "that's not what I meant" a sprint later.

A spec must answer three questions the mockup cannot: what happens when I touch it, what does it look like in every state, and what happens when something goes wrong.

## Operating procedure

Order matters: the element inventory (Step 2) feeds every later step, and states must exist before interactions can reference them.

### Step 1: Gather inputs

1. The design files and which screens are in scope.
2. Fidelity target - pick from the ladder below; do not spec at higher fidelity than the decision requires.
3. Supported breakpoints and input modes (touch, pointer, keyboard).
4. The design token and motion system names, so the spec references tokens, never raw values.
5. Known backend realities: what can be slow, what can fail, what can be empty. If unknown, mark each as a guess and flag for engineering confirmation.

**Fidelity ladder** - match spec depth to project phase:

- **Lo-fi** (concept validation, early usability tests): flows and layout intent only; no redlines, no motion. Speccing pixel details here wastes work that the next iteration deletes.
- **Mid-fi** (scoping, estimation): element inventory, key states, happy-path interactions.
- **Hi-fi / dev-ready** (build handoff): everything below - full states, redlines, motion, breakpoints, edge cases. This is the level the rest of this procedure produces.

### Step 2: Inventory the interactive elements

List every control per screen: buttons, inputs, toggles, draggables, scroll regions, gestures. Anything a user can act on gets a row. This inventory is the spec's table of contents - an element missing here is behavior nobody specs.

### Step 3: Specify states

For each element, define every visual state: default, hover, focus, active/pressed, disabled; loading, success, error where applicable; selected/unselected, expanded/collapsed. Describe what changes between states (color, elevation, label, icon) by token name, not raw hex or px. A button with fewer than five defined states is almost always underspecified.

### Step 4: Specify interactions as trigger → response

Write each as a four-part statement:

- **Trigger** - the exact input: tap, hover 300ms, drag past 40px, Enter key, scroll past threshold.
- **Response** - what changes: state change, navigation, data mutation.
- **Transition** - duration, easing, and what animates, by motion-system token (defer curve/duration definitions to motion-spec).
- **Feedback** - immediate acknowledgment within 100ms: ripple, spinner, optimistic update. If the response can take longer than 400ms, a loading affordance is mandatory.

### Step 5: Specify screen transitions

For navigations and overlays: enter and exit animation (direction, duration token, easing token); whether the previous screen persists, dims, or unmounts; shared-element transitions if any element morphs across screens.

### Step 6: Specify breakpoints and redlines

- Per supported breakpoint: what reflows, what collapses, what disappears, and minimum touch target (44x44px on touch).
- Redlines by token: spacing, sizing, and type references engineering can map to code constants. A raw pixel value in a spec is a future inconsistency.

### Step 7: Specify edge and error behavior

Document every unhappy path: empty, loading, and error states for each data-driven view; slow-network behavior (skeletons, progressive reveal, timeout threshold and what happens at it); validation timing - on blur, on submit, or live, chosen deliberately per field; concurrency - double-tap, rapid navigation, offline. Spell out conditional logic in plain language: "If the cart is empty, the Checkout button is disabled and shows tooltip X." Cover every branch the design implied but did not draw.

### Step 8: Specify accessibility behavior

Focus order and focus trapping for modals; keyboard equivalents for every pointer interaction; live-region announcements for dynamic content; visible focus states already covered in Step 3.

### Step 9: Format as numbered, testable statements

Group by screen, then by element. Each statement must be verifiable by a QA engineer without a follow-up question - "feels snappy" is not a spec; "transition completes in duration-fast with easing-exit" is.

## Spec skeleton (copy and fill)

```
SPEC - [FILL: screen name]            Fidelity: hi-fi / dev-ready
Tokens referenced: [FILL: token set + motion system names]

ELEMENT INVENTORY
  E1 [FILL: element]   E2 [FILL: element]   ...

E1 - [FILL: element name]
  STATES
    default: [FILL: tokens]        hover: [FILL]
    focus: [FILL: visible ring]    active: [FILL]
    disabled: [FILL + why it can be disabled]
    loading / error: [FILL or "n/a because ..."]
  INTERACTIONS
    1. Trigger: [FILL] → Response: [FILL]
       Transition: [FILL: duration + easing tokens] Feedback: [FILL, <100ms]
  BREAKPOINTS
    ≥1024: [FILL]   768-1023: [FILL]   <768: [FILL, touch targets ≥44px]
  EDGE CASES
    Empty: [FILL]  Slow (>400ms): [FILL]  Error: [FILL]
    Double-tap / rapid nav: [FILL]  Offline: [FILL]
  ACCESSIBILITY
    Keyboard: [FILL]  Focus order: [FILL]  Announcements: [FILL]

CONDITIONAL LOGIC
  If [FILL condition], then [FILL behavior].

OPEN QUESTIONS FOR ENGINEERING
  [FILL: every guessed backend behavior, labeled "guess"]
```

## Deliverable

Produce a spec document containing: the element inventory, a state matrix per element, numbered trigger → response interaction statements with motion tokens, screen-transition specs, breakpoint behavior with token-based redlines, edge-case and conditional-logic statements, accessibility behavior, and an open-questions list - ready to hand directly to engineering.

## Common handoff failure modes

- **The happy-path-only spec** - error, empty, and loading states get invented by whoever writes the code, and they will not match the design language.
- **Raw values instead of tokens** - engineers hard-code the pixel value; the next design-system update misses it.
- **Untestable prose** - "should feel responsive" produces a QA argument, not a check.
- **State gaps** - hover and focus omitted because the mockup was drawn for touch; the desktop build ships with dead-feeling controls.
- **Silent assumptions about the backend** - a spec that assumes instant responses breaks the moment latency is real; every timing assumption must be written and flagged.
- **Speccing motion values inline** - durations and curves drift per component; reference the motion system and keep the definitions in motion-spec.

## Quality bar

- Every element in the inventory has all Step 3 states or an explicit "n/a because…".
- Every interaction statement is individually verifiable by QA with no follow-up question.
- Zero raw color/spacing/duration values - tokens only.
- Every data-driven view has empty, loading, error, and slow-network behavior.
- Every guessed value is labeled a guess and listed under open questions.
- After the build, design-qa-checklist can be run directly against the numbered statements.
