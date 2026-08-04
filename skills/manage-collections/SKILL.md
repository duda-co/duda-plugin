---
name: manage-collections
description: Use when the user wants to view, create, or update a Duda site's dynamic content collections (e.g. Team Members, Menu Items, Service Listings, Testimonials) — creating a collection and its fields, or adding/updating/deleting rows from a brief, notes, or a spreadsheet-like list.
---

# Manage collections

## When to use

- "Add a row to the Team Members collection for [site]"
- "Update the price on the Menu Items collection"
- "Create a new collection for [site] to hold [thing]"
- Any request to bulk-populate or edit structured, repeatable site content.

## Tools

`get_collections` · `create_collection` · `create_collection_fields` · `create_collection_rows` · `update_collection_field` · `update_collection_rows` · `delete_collection_field` · `delete_collection_rows`

## Instructions

1. Resolve `site_name` — ask the user or use `list_sites` to find it.
2. Always call `get_collections` first, even if the user named a collection — do not guess field names or types. Match the existing schema exactly when adding/updating rows.
3. If the collection doesn't exist yet, confirm the field list and types with the user, then `create_collection` followed by `create_collection_fields`.
4. When adding rows from a brief or notes, map each item to the existing field names 1:1. Use `create_collection_rows` for new rows, `update_collection_rows` for edits to existing rows (identify rows by whatever unique field the collection uses — usually name or a slug-like field).
5. Confirm with the user before `delete_collection_rows` or `delete_collection_field` — both are destructive and cannot be undone via these tools.
