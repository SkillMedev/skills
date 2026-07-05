---
name: Regex Master
description: Authors, debugs, and hardens regular expressions - test-cases-first workflow, flavor-aware construction (JS, PCRE, Python re, RE2), catastrophic-backtracking detection and rewrites, and annotated verbose patterns with a verification table. Use when someone asks "write a regex for X", "why doesn't this regex match", "is this regex safe for user input", "explain what this pattern does", or a validation regex is hanging the server on certain inputs. Do NOT use for parsing HTML, JSON, or CSV - those need real parsers, not patterns - and do NOT use for full input-validation architecture or injection defenses - use secure-code-review instead.
---

# Regex Master

A regex is a tiny program in a language with no error messages, so the only way to know what it does is to run it against inputs you chose in advance. The costly mistakes this skill prevents are the two silent ones: a pattern that matches things it shouldn't (an unanchored validator "passing" garbage) and a pattern that never returns (catastrophic backtracking turning a validation check into a CPU-pinning denial of service on one crafted input).

## Operating procedure

### Step 1: Gather inputs

1. Flavor and engine - JS, PCRE, Python `re`, Go/RE2, Java. Features differ (lookbehind, possessive quantifiers, `\p{...}`); a pattern is only correct *for an engine*.
2. Purpose: validate a whole string, extract fields, or search-and-replace. This decides anchoring and grouping.
3. Whether the *input* is untrusted (user-supplied strings run through your pattern) or the *pattern* is untrusted (user-supplied patterns - a different threat entirely).
4. 5+ example strings from the requester: at least 2 that must match, 2 near-misses that must not, and 1 adversarial/degenerate case (empty string, very long string, unicode). If they can't supply near-misses, write them yourself and label them guesses to confirm.

### Step 2: Write the test table before the pattern

The tests are the spec. A pattern delivered without a verification table is a guess.

### Step 3: Build incrementally, anchored early

- Anchor validators immediately: `^...$` (or `\A...\z` in Python to avoid the trailing-newline surprise with `$`). An unanchored `\d{4}` happily matches inside `abc12345`.
- Compose from the core vocabulary: character classes `[a-z0-9_]`, negation `[^...]`, shorthand `\d \w \s`; quantifiers `* + ? {m,n}` (lazy `*?` up to a delimiter); groups - capturing `(...)`, non-capturing `(?:...)` by default, named `(?<year>\d{4})` when extracting; lookaround `(?=...) (?!...) (?<=...) (?<!...)` for context conditions.
- Escape literal metacharacters: `. ^ $ * + ? ( ) [ ] { } | \`.
- Prefer a negated class over lazy dot-star: `"[^"]*"` beats `".*?"` - it can't cross a closing quote and it can't backtrack pathologically.

### Step 4: Run the ReDoS audit

Catastrophic backtracking arises when the engine has exponentially many ways to split the same text among quantifiers. Scan for these shapes:

- Nested quantifiers over overlapping atoms: `(a+)+`, `(\w+)*`, `(\d+|\w+)+`.
- Adjacent overlapping quantifiers: `\w+\s?\w+` style chains like `(\w+\s?)+`.
- Alternation with a common prefix under a quantifier: `(a|ab)+`.

The trigger is always a *near-match* - `(\w+\s?)+$` against 30 word characters and a final `!` explores ~2^30 paths. Fixes, in preference order:

1. Restructure so atoms can't overlap: `(\w+\s?)+` becomes `\w+(\s\w+)*`.
2. Possessive quantifiers `\w++` or atomic groups `(?>...)` where the engine supports them (PCRE, Java - not JS, not Python `re`).
3. Switch to a linear-time engine: RE2 (Go, `google-re2`) cannot backtrack catastrophically. Mandatory when the pattern itself is user-supplied.

Test with a degenerate input: 50+ repeats of the vulnerable atom followed by a character that forces failure. If runtime visibly jumps between 30 and 50 repeats, the pattern is exponential - fix it before shipping.

### Step 5: Deliver annotated with the verification table

Anything non-trivial ships in verbose/extended mode or with a line-by-line breakdown:

```python
import re
pattern = re.compile(r"""
    \A(?P<area>\d{3})    # area code
    [-.\s]?              # optional separator
    (?P<prefix>\d{3})
    [-.\s]?
    (?P<line>\d{4})\Z
""", re.VERBOSE)
```

## Worked artifact: verification table + a bad/good pair

Delivered pattern (pragmatic email check, JS): `^[^@\s]+@[^@\s]+\.[^@\s]+$`

| Input | Expect | Got |
|---|---|---|
| `ana@example.com` | match | match |
| `a.b+tag@sub.example.co` | match | match |
| `ana@example` | no match | no match |
| `@example.com` | no match | no match |
| `ana@ex ample.com` | no match | no match |
| `""` (empty) | no match | no match |
| 10k-char string of `a` + `@` | no match, fast | no match, <1ms |

Bad - vulnerable "trimmed words" validator:

```
^(\w+\s?)+$        # hangs on "aaaaaaaaaaaaaaaaaaaaaaaaaaaaaa!"
```

Good - same language, linear time:

```
^\w+(\s\w+)*$      # words separated by single spaces; no overlap, no blowup
```

Recipes worth keeping (validate flavor before use): ISO date `^\d{4}-\d{2}-\d{2}$`; trim `^\s+|\s+$` replaced with empty; extract `${var}` placeholders `\$\{(\w+)\}`.

## Deliverable

Produce the pattern, the flavor it targets, the annotated breakdown, the verification table with every row confirmed, and an explicit ReDoS note ("linear because…" or "hardened by…").

## Do NOT

- Do not deliver a validator without `^...$`/`\A...\z` anchors - unanchored validation is the most common regex bug in production.
- Do not parse HTML, JSON, or CSV with regex when a real parser exists; nesting and escaping rules defeat patterns in edge cases you won't test.
- Do not run user-supplied *patterns* through a backtracking engine - that hands strangers a CPU-exhaustion primitive; use RE2.
- Do not stack quantified overlapping groups (`(a+)+` shapes) even if your test inputs pass - the blowup only appears on near-misses.
- Do not rely on `\d`/`\w` meaning ASCII: in Python 3 and with the JS `u` flag they can match beyond it; use `[0-9]`/`\p{L}` deliberately.
- Do not forget flags: `^ $` are per-line only with `m`; `.` crosses newlines only with `s`/dotall; unicode classes in JS need `u`.

## Quality bar

- Flavor is named; every construct used exists in that flavor.
- The verification table has ≥5 rows including near-misses and one degenerate long input, all passing.
- The ReDoS scan is documented - either no risky shapes, or the applied fix.
- The pattern has an annotation a colleague could maintain from.
- For extraction patterns, groups are named or non-capturing - no bare positional soup.

## Escalation

If the "regex problem" is really structured-format parsing, route to a parser library discussion instead. If the pattern guards a security boundary (auth, injection filters), pair with secure-code-review - regex allowlists are one layer, never the defense.
