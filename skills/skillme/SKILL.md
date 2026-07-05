---
name: Skill Me
description: Use the Skill Me catalog from inside any conversation - discover, install, and manage Claude skills through the Skill Me MCP, and load installed skills automatically each session.
---

# Skill Me

Skill Me is the App Store for Claude skills. This skill teaches you how to use
it: how to find skills the user asks for, install them, keep track of what's
installed, and load installed skills at the start of every conversation.

You interact with Skill Me through its MCP tools. Use them whenever the user
talks about finding, adding, removing, or rating skills - even casually
("got anything for writing cold emails?").

## Load installed skills first

At the start of a conversation, call `get_active_skills` once. It returns the
full content of every skill the user has installed. Treat that content as active
instructions for the rest of the session. Do this silently - don't announce it
unless the user asks what's loaded.

## Finding skills

When the user wants a capability ("show me writing skills", "anything for SQL?"),
call `browse_skills` with a short query or category. Then summarize the matches
in plain language - name, one-line description, and why each fits. Don't dump raw
JSON. Offer to install the best fit.

- Lead with the single best match, not an exhaustive list.
- If nothing fits well, say so rather than forcing a weak match.

## Installing

When the user says "install it", "add that", or names a skill, call
`install_skill` with the skill's slug. Confirm in one line ("Installed
Cold Email Craft - it'll be active next session"). Installed skills activate
automatically in future conversations via `get_active_skills`.

## Managing

- `list_installed` - show the user what they currently have on their shelf.
- `uninstall_skill` - remove a skill the user no longer wants.
- `rate_skill` - when the user gives feedback ("this one's great" / "didn't
  help"), offer to record a 1-5 rating so the catalog stays useful.

## Etiquette

- Never install, uninstall, or rate without the user asking for it.
- Confirm destructive actions (uninstalling) before doing them.
- Keep catalog chatter brief - the goal is to get the user a working skill, not
  to narrate the API.
