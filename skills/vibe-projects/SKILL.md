---
name: vibe-projects
description: Use when the user wants to build, change, or understand a Duda Vibe project — a website or app built and edited by Duda's AI agent from prompts. Covers creating an empty Vibe project, starting a new conversation with the project's agent or continuing an existing one, answering the agent's questions, reading the project's code to explain how something works, viewing form submissions, and publishing.
---

# Vibe projects

## When to use

- "Build me a website / app for [business] with Vibe"
- "On [project], add a bookings page" / "change the hero to…"
- "Continue where we left off on [project]"
- "How does the contact form on [project] work?" / "What pages does it have?"
- "Show me the form submissions for [project]"

## Tools

`create_or_generate_site` · `conversation_find` · `conversation_send_message` · `conversation_wait` · `conversation_get_messages` · `conversation_cancel` · `vibe_list_files` · `vibe_read_files` · `get_site_details` · `list_form_submissions` · `publish_site` · `unpublish_site`

## Credits and plan

- Each message sent with `conversation_send_message` spends AI credits from the user's Duda account.
- Creating, publishing, and unpublishing projects need the Custom plan.

## Is it a Vibe project?

`get_site_details` returns `"editor": "VIBE"` for a Vibe project.

## New or existing conversation

A project can hold many conversations with its agent. `conversation_find` lists them, most recently active first, each with the agent's own `title`.

- **Continue** (pass `conversation_id`) when the request builds on that conversation — the agent keeps its context. Pick the conversation by its `title`; if more than one fits, ask the user.
- **Start new** (omit `conversation_id`) for a separate task. A new conversation still sees everything already in the project, including work done in other conversations. Keep the returned `conversation_id` for follow-ups.

One turn runs per project at a time. If another conversation is running a turn, `conversation_send_message` says which one — `conversation_wait` on it, then send again.

## Instructions

### Create a project

If `create_or_generate_site` is available:

1. Ask the user which creation method they want (`create_or_generate_site` requires an explicit choice). For a Vibe project the method is `vibe`.
2. Call `create_or_generate_site` with `creation_method: 'vibe'` and, if the user gave one, a `default_domain_prefix`. Keep the returned `site_name`.
3. Continue with **Build or change the project** to send the first brief.

If it isn't, ask the user for an existing Vibe project to work in, then continue with **Build or change the project**.

### Build or change the project

1. Resolve `site_name` with `list_sites` if the user named the project rather than its ID.
2. Choose a new or existing conversation (see above).
3. Write the whole request as one message: what to build, the copy, colours, pages, and anything that should stay as it is. Pass reference images or documents as public URLs in `file_urls`.
4. Call `conversation_send_message`. It returns right away with a `cursor`.
5. Call `conversation_wait`, passing the latest `cursor` when you have one. Each call waits up to about a minute; while the state is `in_progress`, call it again with the new `cursor`.
6. When the state is `waiting_for_answer`, the agent is asking the user something. Show the user the `interaction_request` and send their reply with `conversation_send_message` on the same conversation:
   - `question` → `answers`, one per question, with `selected_label` copied verbatim from the request (or `selected_labels`, `other_text`, or `skipped: true`).
   - `approval` or `credential` → `approved: true` or `false`.
7. When the state is `idle`, relay the agent's `last_message`. Share the preview link from `get_site_details` (`preview_site_url`) so the user can see the result.
8. To stop a turn, call `conversation_cancel`, then `conversation_wait` until `idle`. Work already written to the project stays.

### Explain how the project works

1. `vibe_list_files` lists the project's source files. On large projects, narrow it with `path_prefixes` (e.g. `['src/routes', 'src/components/blocks']`).
2. Read what you need with one `vibe_read_files` call (up to 20 paths).
3. Answer from the code, naming the files.

### Conversation history

`conversation_get_messages` returns a conversation's log, oldest first. Pass each page's `cursor` back to read the next page.

### Form submissions

`list_form_submissions` returns what visitors sent through the project's forms. When the agent's reply mentions `{{Forms.Responses}}`, show the user their submissions with `list_form_submissions`.

### Publish

If `publish_site` is available, confirm with the user, then `publish_site` (or `unpublish_site`). Publishing makes the project's latest changes live. The live address is `site_default_domain` in `get_site_details`. The `/duda:publish` command covers this too.

If it isn't, give the user the editor link (`edit_site_url` from `get_site_details`) so they can publish from the Duda editor.
