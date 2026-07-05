---
name: positioning-statement
description: Use when defining or sharpening what a product IS before writing copy or planning a launch. Triggers on "how do I position this", "what category are we in", "who is this really for", "positioning statement", "we sound like everyone else", "nobody gets what we do", "frame the product", "April Dunford positioning". Runs competitive alternatives → unique attributes → the value they enable → best-fit segment → market frame, producing one tight defensible paragraph. Do NOT use for the actual page/headline wording — use [[messaging-hierarchy]] instead; for price points — use [[pricing-strategy]] or [[saas-pricing]].
---

# Positioning Statement

Positioning is the context you set so a buyer instantly understands what you are,
why you are different, and why they should care. Get it wrong and every downstream
asset — the headline, the deck, the launch — is fluent nonsense written about the
wrong product for the wrong person. This is the first GTM step. Everything else
([[messaging-hierarchy]], [[launch-plan-sequencer]], [[launch-day-runbook]],
[[plg-motion-designer]], [[sales-enablement-kit]]) is calibrated to its output, so do
it first and do it honestly.

The trap is positioning from ambition — the category you wish you led, the buyer
you wish you had. Position from evidence instead: the alternatives real customers
weigh, the attributes you actually have, and the segment that already loves you.
This follows April Dunford's method (*Obviously Awesome*): build positioning bottom-up
from competitive alternatives, not top-down from a category you picked.

## When to use this skill

Reach for it before writing any customer-facing copy or planning a launch, and
re-run it whenever the product, the best-fit customer, or the competitive set has
moved. If a founder says "we sound generic," "nobody gets what we do," or "what
category are we even in," that is a positioning problem, not a copy problem — start here.

## The workflow — five components, in this order

Run them in sequence; each later component is derived from the earlier ones. Do NOT
jump to the market frame first — the category you can credibly claim is an *output*
of the value you deliver, not an input you assert.

1. **Competitive alternatives.** List what a customer would *actually* do if you
   did not exist — and be honest: the real alternative is usually a spreadsheet,
   a manual process, a hire, or "do nothing," not the funded competitor you fear.
   These alternatives define the frame the buyer is already in. (For deep rival
   teardowns, defer to the catalog's `competitive-intelligence` skill — here you
   only need the alternatives a buyer weighs at the point of decision.)
2. **Unique attributes.** List the features, capabilities, assets, or facts you
   have that the alternatives do not. Attributes are objective and provable —
   "reconciles in real time," "ships with 40 connectors," "SOC 2 from day one" —
   not adjectives like "powerful" or "intuitive." If an alternative can claim it
   too, it is not unique; cut it.
3. **Value (the so-what).** Translate each unique attribute into the value it
   *enables* for the customer. Attribute → value: "reconciles in real time" →
   "closes the books in a day instead of a week." Buyers do not buy attributes;
   they buy the outcome the attribute makes possible. Cluster the values into 2–3
   themes — these become the spine of [[messaging-hierarchy]].
4. **Best-fit customer segment.** Identify who cares about that value *the most* —
   the segment for whom your unique value is urgent, not nice-to-have. Describe them
   by characteristics you can target (role, company stage, trigger event), not a
   vague persona. Narrow beats broad: the tightest segment that loves you gives the
   sharpest positioning. A weak best-fit segment is the #1 silent positioning killer.
5. **Market frame / category.** Choose the market context that makes your value
   obvious to that segment — the frame of reference that tells the buyer what to
   compare you against and what to expect. You can lead a known category, fight for
   a slice of one, or (rarely, expensively) create a new one. Pick the frame that
   makes your unique value the *obvious* choice; the wrong frame makes your
   strengths look irrelevant.

Deliberately out of scope here: price points and packaging tiers. Positioning sets
the frame; what to charge inside it is [[pricing-strategy]] / [[saas-pricing]]. Naming,
taglines, and page copy are downstream in [[messaging-hierarchy]].

## The one-paragraph statement

Assemble the five components into a single tight internal paragraph. This is an
*internal alignment artifact*, not customer copy — it is the brief every later
skill reads. Fill this template:

```
For [best-fit segment, described by targetable traits]
who [the trigger / situation that makes the value urgent],
[Product] is a [market frame / category]
that [the single most important value, the so-what].
Unlike [the dominant competitive alternative they'd otherwise choose],
[Product] [the unique attribute(s) that make that value possible].
```

Worked example (a fictional close-automation tool):

```
For Series-A-to-B SaaS controllers
who are closing the books manually in spreadsheets and missing board deadlines,
LedgerLoop is a continuous-close accounting platform
that lets finance close the month in a day instead of a week.
Unlike general ledgers and bolt-on close checklists,
LedgerLoop reconciles every transaction in real time as it lands,
so the books are always one day from done.
```

Note what the example does NOT do: no price, no "powerful/intuitive," no
"for everyone," no invented category nobody searches for. Every clause traces to one
of the five components.

## Runnable artifact — positioning scorer

A self-contained Node script that scores a candidate positioning statement against
the five components and the most common failure modes, so the founder gets a sharp,
repeatable check instead of a vibe. Save as `score-positioning.mjs` and run
`node score-positioning.mjs`.

```js
// score-positioning.mjs — score a positioning statement, Dunford-style.
// No deps. Edit the `p` object, then: node score-positioning.mjs

const p = {
  segment: "Series-A-to-B SaaS controllers",
  trigger: "closing the books manually in spreadsheets, missing board deadlines",
  category: "continuous-close accounting platform",
  value: "close the month in a day instead of a week",
  alternative: "general ledgers and bolt-on close checklists",
  attributes: ["reconciles every transaction in real time as it lands"],
};

// Words that signal you wrote an adjective instead of a provable attribute/value.
const FLUFF = ["powerful", "intuitive", "seamless", "robust", "innovative",
  "world-class", "best-in-class", "cutting-edge", "easy-to-use", "next-gen"];
const BROAD = ["everyone", "anyone", "all businesses", "any company", "teams of all sizes"];

const checks = [
  ["Has a best-fit segment", () => p.segment.trim().length > 0],
  ["Segment is targetable, not 'everyone'",
    () => !BROAD.some((b) => p.segment.toLowerCase().includes(b))],
  ["Has a trigger that makes value urgent", () => p.trigger.trim().length > 0],
  ["Names the real competitive alternative",
    () => p.alternative.trim().length > 0],
  ["Has >=1 unique, provable attribute", () => p.attributes.length >= 1],
  ["Value reads as an outcome (has 'instead of' or a metric/verb)",
    () => /(instead of|in (a|one) |without |so |faster|less|days?|hours?|week)/i.test(p.value)],
  ["No adjective fluff in value/attributes",
    () => ![p.value, ...p.attributes].some((s) =>
      FLUFF.some((f) => s.toLowerCase().includes(f)))],
  ["Category is a frame, not a slogan",
    () => p.category.trim().split(/\s+/).length <= 5 && p.category.trim().length > 0],
];

let pass = 0;
console.log("\nPositioning scorecard\n---------------------");
for (const [label, fn] of checks) {
  const ok = fn();
  if (ok) pass++;
  console.log(`${ok ? "PASS" : "FAIL"}  ${label}`);
}
const pct = Math.round((pass / checks.length) * 100);
console.log(`\nScore: ${pass}/${checks.length} (${pct}%)`);
console.log(pct === 100
  ? "Tight. Hand it to messaging-hierarchy."
  : "Fix the FAILs before writing any copy.\n");
```

Expected output for the example above:

```
Positioning scorecard
---------------------
PASS  Has a best-fit segment
PASS  Segment is targetable, not 'everyone'
PASS  Has a trigger that makes value urgent
PASS  Names the real competitive alternative
PASS  Has >=1 unique, provable attribute
PASS  Value reads as an outcome (has 'instead of' or a metric/verb)
PASS  No adjective fluff in value/attributes
PASS  Category is a frame, not a slogan

Score: 8/8 (100%)
Tight. Hand it to messaging-hierarchy.
```

## Quality bar

The positioning is done when:

- Every clause in the paragraph traces back to one of the five components — no
  free-floating claims.
- The competitive alternative is the one a real buyer would *actually* pick, often
  "do nothing" or a spreadsheet — not the competitor that scares the founder.
- Each unique attribute is provable and exclusive: if an alternative could honestly
  claim it too, it is cut.
- Every attribute has a stated so-what value; no attribute is left as a feature.
- The best-fit segment is narrow enough to target and is described by a trigger,
  not a demographic mood.
- The market frame makes the unique value look *obvious*, and a stranger could
  guess what you compete with from the category alone.
- The whole statement survives being read aloud to a skeptic in under 20 seconds
  without a follow-up "wait, but what *is* it?"
- It contains zero price points and zero adjectives doing the work facts should do.

## Do NOT

- Do NOT write headlines, taglines, or page copy here. This produces the internal
  brief; the customer-facing words are [[messaging-hierarchy]]'s job.
- Do NOT set price, tiers, or packaging — defer to [[pricing-strategy]] /
  [[saas-pricing]]. Positioning is the frame, not the number.
- Do NOT invent a brand-new category to "own" unless you have the budget and proof
  to educate a market into it. Creating a category is the most expensive play in
  positioning; most products should lead or sub-segment an existing one.
- Do NOT position against the funded competitor when the real alternative is a
  spreadsheet or doing nothing. You will answer questions no buyer is asking.
- Do NOT pick the broadest possible segment to "keep options open." Broad
  positioning is invisible positioning; the tightest best-fit segment wins.
- Do NOT let adjectives ("powerful," "seamless," "intuitive") stand in for provable
  attributes. If you cannot demo it or cite it, it is not an attribute.
- Do NOT proceed to [[launch-plan-sequencer]], [[launch-day-runbook]], or
  [[sales-enablement-kit]] until the one-paragraph statement scores clean — a launch
  built on muddy positioning only amplifies the muddiness.

## Deliverable

A filled five-component worksheet (alternatives, unique attributes, value/so-what,
best-fit segment, market frame), the assembled one-paragraph positioning statement,
a passing positioning scorecard, and a one-line note on the single riskiest assumption
in the positioning to validate with customers. Hand the paragraph to
[[messaging-hierarchy]] to turn into copy, and to [[launch-plan-sequencer]] to plan the launch.
