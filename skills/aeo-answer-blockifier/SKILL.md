---
name: AEO Answer Blockifier
description: Rewrites a section or page into concise, self-contained answer blocks that AI answer engines and search snippets can lift and cite verbatim. Use when restructuring a blog post, FAQ, docs page, or landing copy to win a featured snippet or get quoted by AI Overviews, ChatGPT, or Perplexity, or when a draft buries its answer in setup. Do NOT use for keyword research, full-article drafting, or technical/crawlability SEO — use a dedicated SEO or copywriting skill instead.
---
# AEO Answer Blockifier

Restructure existing content into question-led blocks an engine can quote out of context, so the page earns citations and snippets instead of just ranking.

## Workflow

1. **Extract the questions.** List every distinct question the section actually answers, in the user's natural wording (including conversational and voice phrasing). One block per question. If a passage answers no clear question, either reframe it as one or drop it from blockification.
2. **Set the heading to the question.** Make each H2/H3 the exact question, matching how a user would type or speak it. This maps the block to the query and mirrors People Also Ask.
3. **Lead with the answer.** State the direct answer in the first one or two sentences under the heading — conclusion first, before any setup. Supporting detail, nuance, and caveats follow. An answer buried in paragraph three forfeits the citation; extraction overwhelmingly favors the first 1-2 sentences after the heading.
4. **Make the block self-contained.** Name the subject explicitly in the answer sentence. Remove "as mentioned above," "this," and any pronoun or reference pointing offscreen, so the block reads correctly when lifted alone.
5. **Pick the format the question demands.** "How to" / "steps" → numbered list. "Best" / "types of" → bulleted list. "What is" → one tight definition paragraph. Comparison → a table. Forcing prose onto a list-shaped query loses the extraction.
6. **Size the block to the format.** Definition/paragraph answer: roughly 40–60 words (featured snippets typically truncate near 300 characters, so the complete answer must land before that cutoff). List: 3–8 scannable items, one idea each, under ~10 words per item where possible. Table: labeled columns, no prose padding in cells.
7. **Add verifiable trust signals.** Where the answer makes a factual claim, attach a concrete number, named source, or first-hand specific an engine can check (this is what E-E-A-T rewards). Replace generic unsourced assertions with verifiable ones.
8. **Verify against the quality bar** before returning.

## Deliverable

Produce the rewritten content as an ordered set of answer blocks, each containing: the question-phrased heading (H2/H3), the answer-first body in the format the question demands (sized per step 6), and any trust signals inline. Alongside the rewrite, include a one-line change log per block noting what was restructured (e.g. "answer moved from paragraph 3 to sentence 1," "prose converted to numbered list") so the author can review the edits without a diff.

## Quality bar

A block ships only if all hold:
- The heading is the user's question; the first sentence answers it directly.
- The block reads correctly quoted in isolation — no offscreen references, subject named.
- The format matches the question type, sized as in step 6.
- Every factual claim is specific and verifiable, none fabricated.
- No throat-clearing intro precedes the answer.

## Do NOT

- Do NOT bury the answer behind setup, background, or a "in this post we'll cover" intro.
- Do NOT leave pronouns or "as above" references that break when the block is quoted alone.
- Do NOT force every heading into a question when the wording becomes unnatural.
- Do NOT fabricate stats, dates, or sources to look citable — readers and engines punish inaccuracy.
- Do NOT strip genuine caveats or accuracy hedges; precise factual qualification builds trust. Cut only filler, not correctness.
- Do NOT promise snippet or citation wins as guaranteed; they fluctuate. Optimize structure, treat placement as upside.
