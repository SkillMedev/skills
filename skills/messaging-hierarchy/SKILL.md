---
name: Messaging Hierarchy
description: Use when turning a positioning statement into actual copy — building the value proposition, message pillars, proof points, and per-channel messaging that keep the website, ads, and sales deck all saying the same thing. Triggers on "write our value proposition", "message pillars", "messaging framework", "messaging house", "key messages", "proof points", "our website and sales say different things", "per-channel messaging", "on-message copy". Takes the positioning-statement as input and feeds landing-page-copy. Do NOT use when you have not yet fixed positioning — use positioning-statement first. Do NOT use when you need the actual hero/landing page words — use landing-page-copy. Do NOT use to sequence launch phases — use launch-plan-sequencer; for the hour-by-hour go-live — use launch-day-runbook; for the sales deck/talk track — use sales-enablement-kit; for the in-product activation flow — use plg-motion-designer.
---

# Messaging Hierarchy

Positioning is the strategy; messaging is the words. Most teams skip the layer in
between and let every surface invent its own — so the website promises one thing,
the ads a second, and the sales deck a third, and the buyer never hears the same
story twice. This skill builds the **messaging hierarchy** (the "messaging
house"): one value proposition on top, three message pillars holding it up, proof
points under each pillar, and per-channel copy derived from — never inventing
beyond — that structure. Build it once, and every channel says the same thing in
its own register.

This skill takes the output of [[positioning-statement]] as its input and produces
the brief that [[landing-page-copy]] turns into hero and page copy. It is the
spine of the [[launch-plan-sequencer]] and the source the [[sales-enablement-kit]],
[[plg-motion-designer]], and [[launch-day-runbook]] pull their language from — so
the same promise survives from the strategy doc to the go-live announcement.

## When to use this skill

Reach for it the moment positioning is fixed and you need words, and whenever
someone notices the website, ads, and sales aren't saying the same thing. If
positioning itself is still wobbly (you can't name the audience, the alternative,
or the one differentiator), stop and run [[positioning-statement]] first — a
messaging house on a cracked foundation just scales the confusion.

## The hierarchy, top to bottom

Everything below the value prop must *ladder up* to it. If a pillar doesn't
support the value prop, or a proof point doesn't support its pillar, cut it.

1. **Value proposition** — one sentence. The single promise to the single
   audience, in their words, that no chosen competitor can say as credibly.
2. **Message pillars** — three (occasionally two or four) themes that *prove the
   promise is true*. Each is a reason-to-believe, not a feature. "Faster" is a
   pillar; "webhooks" is not.
3. **Proof points** — under each pillar, the concrete, checkable evidence:
   metrics, capabilities, customer outcomes, integrations, certifications. This
   is where features finally appear — in service of a pillar.
4. **Per-channel copy** — the same hierarchy expressed in each surface's voice
   and length budget: a 6-word hero, a 90-character ad, a one-line sales opener.

## The ordered workflow

Work top-down. Do not write a single ad until the value prop and pillars are set.

1. **Ingest positioning.** Pull the four load-bearing inputs from
   [[positioning-statement]]: target customer, the alternative they'd otherwise
   use (the "instead of"), the one differentiator, and the category frame. If any
   is missing, get it before proceeding.
2. **Draft the value proposition.** One sentence: *for [audience] who [need],
   [product] is the [category] that [single differentiated benefit] — unlike
   [alternative].* Write it in the customer's language, not yours. Kill adjectives
   that any competitor could also claim ("powerful," "seamless," "intuitive").
3. **Derive 3 message pillars.** Ask: "What three things must a skeptic believe
   for the value prop to be true?" Those are your pillars. Name each as a benefit
   the buyer feels, not a feature you ship. Three is the target — two is thin,
   five is a list nobody remembers.
4. **Load proof points under each pillar.** 2–4 per pillar. Each must be
   *checkable* — a number, a named capability, a customer result, a logo, a
   compliance fact. If you can't substantiate it, it's a claim, not a proof point;
   cut it or go get the evidence.
5. **Run the ladder-up test.** For every proof point ask "which pillar?" and for
   every pillar ask "does this prove the value prop?" Orphans get cut or
   re-homed. The house must be fully connected.
6. **Project onto channels.** Express the *same* hierarchy per surface, longest to
   shortest: website (value prop = hero, pillars = section headers, proof points =
   body) → sales ([[sales-enablement-kit]]) → ads → product/PLG
   ([[plg-motion-designer]]) → social. Shorter channels carry fewer pillars, never
   different ones.
7. **Write the one-page brief and hand off.** Emit the messaging house as a single
   page (see template) and hand it to [[landing-page-copy]] for the hero/landing
   words and to [[sales-enablement-kit]] for the deck and talk track.

## Quality bar

A messaging hierarchy is done when:

- The **value proposition is one sentence** and survives being read aloud. No
  semicolons stapling two promises together.
- Every pillar is a **reason-to-believe the buyer cares about**, not a feature
  name, and there are **three** (two or four only with cause).
- **Every proof point is checkable** — a number, capability, outcome, or fact a
  buyer could verify. Nothing aspirational hiding as evidence.
- The house is **fully connected**: no orphan pillar that doesn't support the
  value prop, no orphan proof point that doesn't support a pillar.
- A teammate could **regenerate the hero, an ad, and a sales opener** from the
  one-pager alone and they'd all say the same thing in different lengths.
- It uses the **customer's words**, validated against real call transcripts /
  reviews / support tickets where possible — not internal jargon.
- It **reuses the existing GTM skills** rather than re-deriving them: pricing
  language defers to [[pricing-strategy]] / [[saas-pricing]], competitor framing
  to [[competitive-intelligence]], overall plan to [[go-to-market-planner]].

## Do NOT

- **Do NOT start here without positioning.** No fixed audience / alternative /
  differentiator means no value prop. Run [[positioning-statement]] first.
- **Do NOT write the landing page in this skill.** This produces the *framework
  and brief*; the actual hero/page words are [[landing-page-copy]]'s job. Stop at
  the one-pager.
- **Do NOT let pillars be feature lists.** "SSO, audit logs, SOC 2" is three
  proof points under one pillar ("Enterprise-ready"), not three pillars.
- **Do NOT ship un-evidenced superlatives.** "The fastest," "the only," "#1"
  without a checkable proof point is a liability, not a message.
- **Do NOT let channels diverge.** A different *length* per channel is correct; a
  different *message* per channel is the exact failure this skill exists to
  prevent.
- **Do NOT re-invent pricing, competitive, or plan content.** Cross-link to
  [[pricing-strategy]], [[saas-pricing]], [[competitive-intelligence]], and
  [[go-to-market-planner]] instead of duplicating them.

## Runnable artifact — the consistency linter

This self-contained Python script holds the messaging house as data and checks the
structure the eye misses: orphaned pillars/proof points, the wrong number of
pillars, channels that go off-message, and proof points that read as un-checkable
superlatives. Fill in `HOUSE`, run it, and fix what it flags before you write a
word of channel copy.

```python
#!/usr/bin/env python3
"""messaging_lint.py — lint a messaging hierarchy for structural integrity.
Usage: python3 messaging_lint.py   (edit HOUSE below first)
No dependencies. Pure stdlib.
"""
import re
import sys

# ---- FILL THIS IN (from your positioning-statement output) ------------------
HOUSE = {
    "value_prop": (
        "For revenue teams drowning in CRM busywork, Pulse is the sales workspace "
        "that auto-logs every call and email \u2014 unlike Salesforce, no manual data entry."
    ),
    "pillars": [
        {
            "name": "Zero manual entry",
            "proof": [
                "Auto-captures calls, emails, and meetings into the CRM",
                "Reps save ~5 hours/week vs. manual logging (Q2 customer survey, n=120)",
            ],
        },
        {
            "name": "Live, trustworthy pipeline",
            "proof": [
                "Pipeline updates in real time, not at week's end",
                "Forecast accuracy within 8% across 40 design partners",
            ],
        },
        {
            "name": "Enterprise-ready",
            "proof": [
                "SOC 2 Type II certified",
                "SSO, SCIM, and role-based access control",
            ],
        },
    ],
    # Each channel lists which pillars it carries. Must be a SUBSET of pillars,
    # never a pillar that doesn't exist in the house.
    "channels": {
        "website": ["Zero manual entry", "Live, trustworthy pipeline", "Enterprise-ready"],
        "ads": ["Zero manual entry"],
        "sales_deck": ["Zero manual entry", "Live, trustworthy pipeline", "Enterprise-ready"],
        "social": ["Zero manual entry", "Live, trustworthy pipeline"],
    },
}
# -----------------------------------------------------------------------------

SUPERLATIVES = re.compile(
    r"\b(fastest|best|only|#1|number one|leading|world.?class|revolutionary|"
    r"seamless|powerful|cutting.?edge|unmatched|ultimate)\b",
    re.IGNORECASE,
)
CHECKABLE = re.compile(r"\d|SOC|SSO|SCIM|ISO|HIPAA|GDPR|%|hours?|x\b")


def lint(house):
    errors, warnings = [], []
    vp = house.get("value_prop", "").strip()
    pillars = house.get("pillars", [])
    names = [p["name"] for p in pillars]

    # 1. Value proposition: one sentence, present, no double-promise.
    if not vp:
        errors.append("value_prop is empty.")
    else:
        sentences = [s for s in re.split(r"(?<=[.!?])\s+", vp) if s.strip()]
        if len(sentences) > 1:
            errors.append(f"value_prop is {len(sentences)} sentences; tighten to one.")
        if ";" in vp:
            warnings.append("value_prop uses ';' \u2014 likely two promises stapled together.")

    # 2. Pillar count: 3 ideal, 2\u20134 allowed, else flag.
    if not 2 <= len(pillars) <= 4:
        errors.append(f"{len(pillars)} pillars; target 3 (2\u20134 only with cause).")
    elif len(pillars) != 3:
        warnings.append(f"{len(pillars)} pillars \u2014 3 is the sweet spot. Justify {len(pillars)}.")
    if len(set(names)) != len(names):
        errors.append("duplicate pillar names \u2014 each pillar must be distinct.")

    # 3. Proof points: present, checkable, not bare superlatives.
    for p in pillars:
        proof = p.get("proof", [])
        if not 2 <= len(proof) <= 4:
            warnings.append(f"pillar '{p['name']}': {len(proof)} proof points (want 2\u20134).")
        for pt in proof:
            if SUPERLATIVES.search(pt) and not CHECKABLE.search(pt):
                errors.append(f"un-checkable superlative under '{p['name']}': \"{pt}\"")

    # 4. Channels: every carried pillar must exist in the house (no off-message).
    for ch, carried in house.get("channels", {}).items():
        for pillar in carried:
            if pillar not in names:
                errors.append(f"channel '{ch}' carries unknown pillar '{pillar}' (off-message).")
        if not carried:
            warnings.append(f"channel '{ch}' carries no pillars \u2014 it will say nothing.")

    return errors, warnings


def main():
    errors, warnings = lint(HOUSE)
    for w in warnings:
        print(f"WARN  {w}")
    for e in errors:
        print(f"ERROR {e}")
    if errors:
        print(f"\n{len(errors)} error(s), {len(warnings)} warning(s). Fix errors before writing copy.")
        sys.exit(1)
    print(f"\nMessaging house is structurally sound ({len(warnings)} warning(s)). "
          "Hand the one-pager to landing-page-copy.")


if __name__ == "__main__":
    main()
```

Worked example output for the `HOUSE` above:

```
Messaging house is structurally sound (0 warning(s)). Hand the one-pager to landing-page-copy.
```

If you instead set the ads channel to a pillar that doesn't exist (e.g. `"ads": ["Cheapest"]`)
and add a proof point like `"The only real-time CRM"`, the linter prints:

```
ERROR un-checkable superlative under 'Live, trustworthy pipeline': "The only real-time CRM"
ERROR channel 'ads' carries unknown pillar 'Cheapest' (off-message).

2 error(s), 0 warning(s). Fix errors before writing copy.
```

## Fill-in template — the one-page messaging house

Hand this completed page to [[landing-page-copy]] and [[sales-enablement-kit]] as
the single source of truth.

```
MESSAGING HOUSE — <Product>
Inputs (from positioning-statement):
  Audience:        <who, specifically>
  Instead of:      <the alternative they'd otherwise use>
  Differentiator:  <the one thing only you can claim>
  Category:        <the frame>

VALUE PROPOSITION (one sentence):
  For <audience> who <need>, <product> is the <category> that
  <single differentiated benefit> — unlike <alternative>.

PILLAR 1: <benefit the buyer feels>
  - proof: <checkable evidence>
  - proof: <checkable evidence>
PILLAR 2: <benefit the buyer feels>
  - proof: <checkable evidence>
  - proof: <checkable evidence>
PILLAR 3: <benefit the buyer feels>
  - proof: <checkable evidence>
  - proof: <checkable evidence>

PER-CHANNEL PROJECTION (same message, different length):
  Website hero (<= 8 words):   <value prop, compressed>
  Website sections:            <pillar 1> | <pillar 2> | <pillar 3>
  Ad headline (<= 90 chars):   <strongest single pillar>
  Sales opener (1 line):       <value prop in the rep's voice>
  PLG / in-product nudge:      <pillar most felt during activation>
  Social one-liner:            <value prop, conversational>

Deferrals:
  Pricing language ......... see pricing-strategy / saas-pricing
  Competitor framing ....... see competitive-intelligence
  Overall GTM plan ......... see go-to-market-planner
```

## Deliverable

A one-page messaging house: the inputs from [[positioning-statement]], a
one-sentence value proposition, three pillars each with 2–4 checkable proof
points, and a per-channel projection that keeps website, ads, sales, PLG, and
social on the same message in different lengths — linted for structural integrity
and ready to hand to [[landing-page-copy]] and [[sales-enablement-kit]].
