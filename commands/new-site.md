---
description: Create a new Duda site from a template.
---

# New site

## Required input

**A template choice** — a category, name, or explicit template ID. If missing, ask, or use `list_templates` to help the user find one.

## Workflow

Use the `create-site` skill for the full procedure: `list_templates` to find a template, `create_or_generate_site` with `creation_method: 'template'`, then `grant_site_access` if the site is for a client.

This command does not generate a site from a written brief — template only.
