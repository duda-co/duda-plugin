---
name: content-library
description: Use when updating a Duda site's global content library — shared business info and content reused across pages and widgets (e.g. business name, hours, phone, address) — from client notes, a brief, or a request to change site-wide text, and when the user wants that change to go live.
---

# Content library

## When to use

- "Update the business hours / phone number / address for [site]"
- "Change this text everywhere it appears on the site"
- Any edit to shared, global content rather than a single page or collection row.

## Tools

`get_content_library` · `update_content_library` · `publish_content_library`

## Instructions

1. Resolve `site_name`.
2. Call `get_content_library` first to see the existing structure and field keys — don't guess key names or invent new ones for existing concepts.
3. Call `update_content_library` with only the fields that changed.
4. Content library updates are saved as a draft by default. Ask the user whether to `publish_content_library` now or leave it staged — publishing pushes the change to the live site immediately.
