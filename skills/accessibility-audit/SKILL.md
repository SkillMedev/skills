---
name: Accessibility Audit
description: Runs a full WCAG 2.2 accessibility audit of an interface - keyboard, screen reader, contrast and zoom, forms, and media, in a fixed pass order - and delivers a severity-triaged findings table with concrete fixes. Use when someone asks "is this page accessible", "run an a11y audit on this UI", "will this pass WCAG AA", "why can't a screen reader use this form", or is preparing for an accessibility review, procurement questionnaire, or VPAT. Do NOT use for color-and-contrast-only questions such as palette checks or colorblind-safe data visualization - use color-accessibility instead.
---

# Accessibility Audit

An interface that fails keyboard or screen reader users fails them completely - not "slightly worse UX," but locked out. Retrofitting accessibility after launch costs several times what building it in does, and automated scanners catch only about 30% of real defects, so a team that "ran axe and passed" usually ships the other 70%. This audit finds the full set, in an order that stops early failures from masking later ones, and triages every finding by severity so the team fixes blockers before polish.

## Inputs to collect

Gather these before auditing. If any is missing, ask; if the user shrugs, apply the default and label it a guess.

1. **Scope** - the pages, flows, or components under audit. Default: the top task flow (e.g., sign-up through first core action), not the whole site.
2. **Target conformance level** - default WCAG 2.2 Level AA. Level A alone is rarely acceptable for procurement; AAA is aspirational, not a default.
3. **Artifact type** - live URL, code, or screenshots. Code beats screenshots: focus order and ARIA state are invisible in a picture.
4. **Assistive tech matrix** - default VoiceOver + Safari (macOS/iOS) and NVDA + Chrome (Windows).
5. **Known constraints** - legacy widgets, third-party embeds, legal deadlines.

## Operating procedure

Run the passes in this order. Keyboard comes first because a control that cannot be reached cannot be screen-reader tested; contrast comes after semantics because fixing markup often changes rendered styles.

### Pass 1: Keyboard

- Tab through the entire page using only the keyboard. Everything interactive must be reachable and operable (Enter/Space for buttons, arrows within composite widgets).
- Visible focus indicator on every focusable element - never `outline: none` without a replacement.
- Logical tab order matching visual order; no positive `tabindex`.
- Modals trap focus while open and restore focus to the trigger on close.
- WCAG 2.2: focus must not be entirely hidden behind sticky headers (Focus Not Obscured).
- A "skip to content" link is the first focusable element.

### Pass 2: Automated scan

Run axe (or equivalent) to sweep the mechanically detectable ~30%: missing alt, missing labels, duplicate IDs, landmark misuse. Record findings but do not stop here.

### Pass 3: Screen reader

- Navigate by headings, landmarks, and forms mode. Native elements first: `<button>`, `<a href>`, `<nav>`, `<main>`, `<label>` ship with correct roles and keyboard behavior. Reach for ARIA only when no native element fits - the first rule of ARIA is don't use ARIA.
- Every control has an accessible name via `<label>`, `aria-label`, or `aria-labelledby`.
- ARIA state (`aria-expanded`, `aria-selected`, `aria-checked`) stays in sync with visual reality:

```html
<button aria-expanded="false" aria-controls="menu">Options</button>
<ul id="menu" hidden>...</ul>
```

- Async updates announce via `aria-live="polite"` regions.
- Every image has meaningful `alt`, or `alt=""` if decorative.
- Custom widgets (comboboxes, tabs, menus) follow the WAI-ARIA Authoring Practices keyboard model exactly - no invented interaction schemes.

### Pass 4: Contrast, zoom, and motion

- Text contrast ≥ 4.5:1; large text (≥ 24px, or ≥ 18.5px bold) ≥ 3:1; UI components and meaningful graphics ≥ 3:1.
- Meaning is never conveyed by color alone - pair with text or icons.
- Page usable at 200% zoom; content reflows at 400% (320 CSS px width) without horizontal scrolling.
- Zoom is never disabled - `user-scalable=no` is a violation.
- Animations respect `prefers-reduced-motion`.
- WCAG 2.2: interactive targets at least 24×24 CSS px (with spacing exceptions); dragging operations have a single-pointer alternative; authentication never requires memory or transcription puzzles.

### Pass 5: Forms

- Every field has a programmatically associated label.
- Error messages are linked to their field via `aria-describedby`, announced on submit, and never conveyed by color alone.
- Required state is programmatic, not just an asterisk.

### Pass 6: Media

- Prerecorded video: captions (Level A) and audio description (Level AA).
- Audio-only content: transcript. No autoplaying audio longer than 3 seconds without a stop control.

### Pass 7: Triage and report

Assign each finding a severity:

- **Critical** - blocks task completion for assistive-tech users, or a Level A failure on a core flow. Fix before ship.
- **Serious** - major barrier or Level AA failure; workaround exists but is painful. Fix this cycle.
- **Moderate** - friction, degraded experience. Next cycle.
- **Minor** - best-practice polish. Backlog.

## Findings table template

Copy and fill one row per finding:

```
| # | Location | WCAG SC | Severity | Finding | Fix | Owner |
|---|----------|---------|----------|---------|-----|-------|
| 1 | [FILL: page/component] | [FILL: e.g. 2.1.1] | [FILL: Critical/Serious/Moderate/Minor] | [FILL: what fails, for whom] | [FILL: concrete code-level fix] | [FILL] |
```

**Bad finding:** "Buttons need ARIA labels." (No location, no criterion, no severity, wrong fix - visible-text buttons don't need aria-label.)

**Good finding:** "Cart page, icon-only delete button (2.1.1 / 4.1.2, Critical): rendered as `<div onclick>`, unreachable by keyboard and announced as plain text by NVDA. Fix: replace with `<button aria-label=\"Remove item\">` - native button restores focusability, role, and Enter/Space handling."

## Deliverable

Produce an audit report containing: scope and conformance target, the assistive-tech matrix used, the severity-triaged findings table, a count summary by severity, and a top-five fix list ordered by user impact (not by ease).

## Do NOT

- Do not certify "compliant" from an automated scan alone - scanners miss ~70% of defects, including every focus-order and announcement problem.
- Do not prescribe ARIA patches on non-semantic markup when a native element exists; `role="button"` on a div still lacks keyboard handling.
- Do not average severities - one Critical outweighs twenty Minors, and the report must say so.
- Do not test only the happy path; error states, empty states, and loading states are where accessibility usually breaks.
- Do not treat 24×24 target size, focus-not-obscured, dragging alternatives, or accessible authentication as optional - they are WCAG 2.2 success criteria, not suggestions.

## Quality bar

Before delivering, verify: every finding cites a location, a WCAG success criterion, a severity, and a concrete fix a developer can apply without research; every pass (keyboard, scan, screen reader, contrast/zoom, forms, media) was executed or explicitly marked out of scope; the Critical list is short enough to be actionable this sprint; guessed inputs are labeled as guesses.

## Escalation

This audit is engineering guidance, not a legal conformance certification - formal claims (VPAT/ACR, ADA or EAA exposure) need a qualified accessibility firm and legal counsel. For color palette design, contrast-safe brand systems, or colorblind-safe charts alone, route to color-accessibility. For copy-level clarity of labels and errors, pair with ux-writing-audit.
