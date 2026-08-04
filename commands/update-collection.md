---
description: Add, update, or review rows in a Duda site's content collection (e.g. Team Members, Menu Items).
---

# Update collection

## Required input

**Site name** and **collection name**. If either is missing, ask, or use `list_sites` / `get_collections` to help the user find them.

## Workflow

Use the `manage-collections` skill for the full procedure: resolve the site, call `get_collections` to confirm the existing schema before writing anything, then map the requested changes onto the existing fields with `create_collection_rows` / `update_collection_rows` / `delete_collection_rows` as appropriate.

Confirm with the user before any delete.
