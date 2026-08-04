---
description: Publish or unpublish a Duda site.
---

# Publish

## Required input

**Site name or identifier.** If the user didn't give one, ask, or use `list_sites` to help them find it.

## Workflow

1. Resolve `site_name`.
2. Determine intent — publish (default) or unpublish, based on what the user asked.
3. Confirm the action and the site name with the user before calling `publish_site` or `unpublish_site` — both are visible, live-facing actions.
4. Report the result, including the live URL when publishing.
