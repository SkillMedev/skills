---
name: Knowledge Base Article Writer
description: Turns a resolved support ticket into a clear, searchable knowledge-base or help center article. Use when someone asks "write a KB article from this resolved ticket", "turn these resolved tickets into help center articles", or "write an internal knowledge base article for our support agents" - converting ticket resolutions into self-service documentation that deflects future tickets. Do NOT use for writing user-facing how-to guides from scratch - use help-documentation instead.
---

# Knowledge Base Article Writer

Every ticket that gets answered manually is a ticket that could have been self-served. A good knowledge-base article is written for the customer who is about to open a ticket, not for the agent who already knows the answer.

## When to create an article

Create an article when a ticket has been answered by two or more different customers in the past 60 days, or when a support lead flags a topic as recurring. Do not create articles for one-off edge cases or deprecated features.

## Article structure

Use this structure for every article:
1. Title: a plain-language question or task the customer is trying to accomplish ('How to reset your password', 'Why was I charged twice'). Use the exact words a customer would type into a search box.
2. Summary (2-3 sentences): what this article covers and who it is for.
3. Prerequisites (if applicable): what the customer needs before starting.
4. Steps or explanation: numbered steps for procedural articles, short paragraphs for conceptual ones. Each step has one action. Use plain language, no internal jargon.
5. What to do if it does not work: one to three common failure modes and their fixes.
6. Related articles: two to four links to directly related help content.

## Worked example

The same resolved ticket ("customer saw two identical charges; second was an authorization hold") turned into an article, badly and well:

Bad:

> **Title:** Resolving duplicate charge incidents
> **Opening:** In certain scenarios, the billing subsystem may generate a secondary transaction record when a pre-authorization is initiated prior to capture...

Good:

> **Title:** Why was I charged twice?
> **Opening:** If you see two identical charges from us, the second one is almost always a temporary authorization hold, not a real charge. It disappears from your statement on its own, usually within 3-5 business days. Here's how to tell the difference and what to do if both charges stay.

The bad version is titled in the team's internal vocabulary ("duplicate charge incident"), opens with system internals, and buries the answer. The good version is titled in the customer's search words, answers the most common scenario in the first two sentences, and only then handles the rarer case.

## Writing rules

Write in second person ('you', not 'the user'). Use active voice. Keep sentences under 20 words. Screenshots or short screen recordings reduce confusion for procedural articles - include them where possible. Avoid mentioning specific version numbers or dates unless the content is truly version-specific; these make articles go stale.

## SEO and findability

The article title and first paragraph must contain the keywords the customer would naturally use. Think about synonyms: a customer might search 'cancel subscription', 'end plan', 'stop billing', or 'delete account' for the same workflow - address each variant either in the title or in the first two sentences.

## Review and freshness

Set a review date of 90 days on every new article. Mark articles as 'needs review' when the underlying product or policy changes. Archive articles that have not received a visit in 180 days - they add noise to search results.

## Deliverable

Produce one publish-ready article containing: a search-phrased title, a 2-3 sentence summary, prerequisites where applicable, numbered steps or a short explanation, a "what to do if it does not work" section covering one to three failure modes, two to four related-article links, and a 90-day review date.

## Quality bar

- The title uses words a customer would actually type into search, not internal terminology.
- The answer to the most common scenario appears within the first two sentences of the body.
- Every procedural step contains exactly one action.
- No internal jargon, ticket language, or agent-only terminology survives into the article.
- Synonym variants of the customer's search are covered in the title or the first two sentences.

## What to avoid

- Do not copy-paste the agent's reply verbatim; ticket language is written for one customer, not all customers.
- Do not bury the answer - lead with the most common scenario, not the edge case.
