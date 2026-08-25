# duda

Build and manage Duda websites from Claude — sites, collections, content, blog, ecommerce, and accounts. Connects Claude to the Duda Partner API via the [Duda MCP server](https://developer.duda.co/docs/dudas-mcp).

## Install

- `/plugin marketplace add duda-co/duda-plugin`
- `/plugin install duda@duda`

Or, once listed in the community directory:

- `/plugin install duda@claude-plugins-community`

## What's inside

### Skills
Loaded automatically when relevant to the conversation.

- **`setup`** — connects the Duda connector if it isn't already authorized, and helps locate account/site identifiers.
- **`manage-collections`** — view, create, and update dynamic content collections (Team Members, Menu Items, Service Listings, etc.).
- **`bulk-collections-import`** — load a spreadsheet, CSV, or list of repeated entries into a collection without duplicating rows on a re-run.
- **`content-library`** — update shared/global site content (business info, hours, contact details) from client notes.
- **`blog-workflow`** — draft, edit, publish, unpublish, and delete blog posts; enable a blog on a new site.
- **`client-accounts`** — agency operations: create client accounts, grant/revoke site access, manage permissions, generate SSO/reset links.

### Commands
Explicit, user-invoked shortcuts.

- **`/duda:publish`** — publish or unpublish a site.
- **`/duda:update-collection`** — add/update/review collection rows.
- **`/duda:blog`** — draft or publish a blog post.

### MCP server
Configured in `.mcp.json`:

- **`duda`** — `https://mcp.duda.co/mcp` (Streamable HTTP, OAuth 2.0).

## Fast-follow skills

- `ecommerce` — products, orders, tax/shipping.
- `site-audit` — activity log and stats reporting.
