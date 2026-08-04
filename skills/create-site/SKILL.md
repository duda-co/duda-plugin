---
name: create-site
description: Use when the user wants to create a new Duda site from a template. Does not cover generating a site from a natural-language brief — that requires the AI creation method, which this skill does not use.
---

# Create site

## When to use

- "Create a new site for [client/business]"
- "Set up a site from the [X] template"
- Any request to create a site where the starting point is a template, not a written brief.

## Tools

`list_templates` · `create_or_generate_site` (`creation_method: 'template'` only) · `grant_site_access` · `create_page` · `update_page_settings` · `publish_site`

## Instructions

1. Use `list_templates` to find a suitable template — filter by category, `has_blog`, or `has_store` as relevant to the request. Show the user a short list of options if more than one fits.
2. Call `create_or_generate_site` with `creation_method: 'template'` and the chosen `template_id`. Never pass `creation_method: 'AI'` or `'populate template with AI'` — both require a natural-language business brief, which this skill does not collect or use.
3. Save the returned `site_name` — required for all further operations on this site.
4. If the site should be accessible to a client account, follow with `grant_site_access`.
5. Confirm with the user before `publish_site`.
