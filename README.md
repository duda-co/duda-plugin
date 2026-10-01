# duda

Build and manage Duda websites from Claude — Vibe projects, collections, content, blog, ecommerce, and accounts. Connects Claude to the Duda Partner API via the [Duda MCP server](https://developer.duda.co/docs/dudas-mcp).

## Install

- `/plugin marketplace add duda-co/duda-plugin`
- `/plugin install duda@duda`

Or, once listed in the community directory:

- `/plugin install duda@claude-plugins-community`

## What's inside

### Skills
Loaded automatically when relevant to the conversation.

- **`vibe-projects`** — build and change Vibe projects (websites and apps) with Duda's AI agent: start a new conversation or continue an existing one, answer the agent's questions, read the project's code, view form submissions, and publish.
- **`setup`** — connects the Duda connector if it isn't already authorized, and helps locate account/site identifiers.
- **`manage-collections`** — view, create, and update dynamic content collections (Team Members, Menu Items, Service Listings, etc.).
- **`bulk-collections-import`** — load a spreadsheet, CSV, or list of repeated entries into a collection without duplicating rows on a re-run.
- **`content-library`** — update shared/global site content (business info, hours, contact details) from client notes.
- **`blog-workflow`** — draft, edit, publish, unpublish, and delete blog posts; enable a blog on a new site.
- **`client-accounts`** — agency operations: create client accounts, grant/revoke site access, manage permissions, generate SSO/reset links.

### Commands
Explicit, user-invoked shortcuts.

- **`/duda:vibe-build`** — build a new Vibe project (website or app) from a description.
- **`/duda:vibe-iterate`** — send a change to a Vibe project's agent, in a new or existing conversation, and share the result.
- **`/duda:publish`** — publish or unpublish a site.
- **`/duda:update-collection`** — add/update/review collection rows.
- **`/duda:blog`** — draft or publish a blog post.

### MCP server
Configured in `.mcp.json`:

- **`duda`** — `https://mcp.duda.co/mcp` (Streamable HTTP, OAuth 2.0).

## Credits & publishing

- **Credits** — each message sent to a Vibe project's agent (`conversation_send_message`, used by `/duda:vibe-build` and `/duda:vibe-iterate`) spends AI credits from your Duda account.
- **Publishing** — `publish_site` makes the latest changes live to visitors.
- **Plan** — creating, publishing, and unpublishing projects need the Custom plan.

## Fast-follow skills

- `ecommerce` — products, orders, tax/shipping.
- `site-audit` — activity log and stats reporting.

## Releases

Versioning and the changelog are automated with
[release-please](https://github.com/googleapis/release-please). Commit messages
must follow [Conventional Commits](https://www.conventionalcommits.org/)
(`type(scope)?: short description`) — the type decides the bump:

| Commit | Bump |
|---|---|
| `fix: ...` | patch (0.1.0 → 0.1.1) |
| `feat: ...` | minor (0.1.0 → 0.2.0) |
| `feat!: ...` or `BREAKING CHANGE:` in the body | major (0.1.0 → 1.0.0) |
| `docs:` / `refactor:` / `build:` / `revert:` | no bump; listed in the changelog |
| `style:` / `test:` | no bump; hidden from the changelog |

Pushing to `main` opens or updates a **release PR** that accumulates the pending
changes. Nothing ships until that PR is merged — merging it bumps the version in
`.claude-plugin/plugin.json` and `version.txt`, writes `CHANGELOG.md`, tags the
commit, and publishes a GitHub Release.
