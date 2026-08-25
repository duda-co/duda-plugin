---
name: bulk-collections-import
description: Use when the user has a list of items to load into a Duda collection in bulk — a pasted spreadsheet, CSV, or a document of repeated entries (menu items, staff bios, listings, rosters). Covers mapping source columns onto collection fields, coercing values, and re-running an import without duplicating rows. For editing a handful of rows by hand, or setting up a collection with no data to load, use `manage-collections` instead.
---

# Bulk collections import

## When to use

- "Here's the client's menu spreadsheet — get it into the Menu Items collection"
- "Import these 60 listings"
- "I resent the roster with 3 new players, import it again"
- Any request moving a repeated list from a file or a paste into a collection.

## Tools

`get_collections` · `create_collection` · `create_collection_fields` · `create_collection_rows` · `update_collection_rows`

## Constraints

- Images must already be public URLs — there is no media tool. Do not offer to
  upload files.
- Do not write rows to a collection whose data comes from an external endpoint;
  that endpoint owns them.
- There is no upsert. Re-running an import without matching existing rows
  duplicates all of them.
- Do not delete existing rows to "clean up" before importing unless the user
  asks.

## Instructions

1. Resolve `site_name`. Read the source list in full before writing anything —
   count the rows and note which columns are actually populated.
2. `get_collections` for the target: field names, types, and existing rows. If it
   doesn't exist, propose fields and types inferred from the source, confirm,
   then `create_collection` + `create_collection_fields`.
3. Propose the column → field mapping and show 2–3 fully mapped sample rows.
   Wait for approval before any write. Name unmapped source columns out loud
   rather than dropping them silently.
4. Coerce each value to its field's type — strip currency symbols and thousands
   separators, normalize dates, convert yes/no to booleans. Flag values that
   won't convert instead of writing an empty cell.
5. Pick a key field (usually name or a slug) and compare against the existing
   rows from step 2. New → `create_collection_rows`. Matches →
   `update_collection_rows` with their row ids. Say how many will be created
   versus updated before writing.
6. `create_collection_rows` returns the ids of the rows it created. If a write
   fails, re-read the collection to see what landed before retrying — never
   re-run the import blind.
