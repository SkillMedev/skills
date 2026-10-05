---
name: component-api-design
description: Design clean, composable React/Vue component APIs with accessibility built in.
metadata:
  title: "Component API Design"
---

# Component API Design

Use this skill to design the public API of a UI component before writing
implementation. A good API is learnable, hard to misuse, and composable.

## Principles

1. **Make the common case effortless, the complex case possible.**
2. **Prefer composition over configuration** - slots beat boolean explosions.
3. **The prop name is documentation** - it should read aloud as English.
4. **Accessibility is not a prop** - it is the default behavior.

## Step 1: Name the responsibilities

List every job the component does. If it does more than one thing that could vary
independently, split it. A `Menu` that also manages a button is two components:
`Menu` and `MenuTrigger`.

## Step 2: Choose the composition model

Pick one and stay consistent:

- **Props model** - flat config for simple, leaf components (`Badge`, `Avatar`).
- **Compound components** - shared context for related parts (`Tabs`, `Tab`, `TabPanel`).
- **Render props / slots** - when the consumer must control rendering.

## Step 3: Design the props

For each prop, decide:

- **Controlled vs uncontrolled.** Offer both with the `value` / `defaultValue` pair
  plus `onChange`. Never silently switch modes.
- **Type.** Prefer string unions over booleans: `variant="ghost"` not `ghost={true}`.
- **Default.** Every optional prop needs a sensible default documented in one place.

Avoid these smells:

- More than two boolean props that interact (combinatorial explosion).
- Props named after implementation (`useFlexbox`) instead of intent (`align`).
- A `style` or `className` escape hatch as the only customization path.

## Step 4: Events

- Name handlers `onSomethingHappened`, past or present tense consistently.
- Pass semantic payloads, not raw DOM events: `onSelect(item)` beats `onClick(e)`.
- Make events cancelable only when the consumer can meaningfully prevent default.

## Step 5: Accessibility contract

- Forward `ref` to the focusable root.
- Spread remaining props (`...rest`) onto the semantic element so `aria-*` works.
- Manage focus, roles, and keyboard interaction internally; expose hooks to override.
- Document the rendered semantics (role, tab order) in the API notes.

## Step 6: Write the usage examples first

Before implementing, write three example call sites: the trivial case, the typical
case, and the hardest case you expect. If any reads awkwardly, revise the API now -
it is free to change before code exists and expensive after.

## Output

Deliver a typed interface (TypeScript props or Vue `defineProps`), three usage
examples, and a one-paragraph rationale for the composition model chosen.
