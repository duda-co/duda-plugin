---
description: Build a new Duda Vibe project (website or app) from a description.
argument-hint: <project description>
---

# Vibe build

Build a new Vibe project described as: **$ARGUMENTS**

## Workflow

Use the `vibe-projects` skill for the full procedure.

1. If `create_or_generate_site` is available, confirm with the user that they want a Vibe project (it requires an explicit creation method), then call it with `creation_method: 'vibe'`. Keep the returned `site_name`.
2. If it isn't, ask the user for an existing Vibe project to build in.
3. Turn the description into one complete brief — business, audience, pages or screens, copy, colours — asking the user for anything important that's missing.
4. Call `conversation_send_message` without a `conversation_id` to open a new conversation. Keep the returned `conversation_id` for follow-ups.
5. Follow the turn with `conversation_wait`, answering any questions the agent asks.
6. Relay the agent's reply and share the preview link (`preview_site_url` from `get_site_details`).

For further changes, use `/duda:vibe-iterate`.
