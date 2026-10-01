---
description: Send a change to a Duda Vibe project's AI agent, in a new or existing conversation, and share the result.
argument-hint: <project> — <change request>
---

# Vibe iterate

Change a Vibe project. Request: **$ARGUMENTS**

## Workflow

Use the `vibe-projects` skill for the full procedure.

1. Resolve `site_name` — use `list_sites` if the user named the project rather than its ID.
2. Call `conversation_find`. If the change builds on an existing conversation, continue it by passing its `conversation_id` (pick it by `title`; ask if more than one fits). Otherwise start a new conversation by omitting `conversation_id`.
3. Call `conversation_send_message` with the full request in one message.
4. Follow the turn with `conversation_wait`, answering any questions the agent asks.
5. Relay the agent's reply and share the preview link (`preview_site_url` from `get_site_details`).
6. Offer to iterate further or publish (see **Publish** in the `vibe-projects` skill).
