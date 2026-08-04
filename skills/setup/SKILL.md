---
name: setup
description: Guides connecting the Duda connector to Claude if it isn't already connected, and helps locate the account/site identifiers other Duda skills and commands need. Use on first use of the Duda plugin, when a Duda tool call fails because the connector isn't authorized, or when the user asks how to connect/set up Duda.
---

# Duda setup

## When to use

- This is the first time the user is using a Duda skill or command in this session.
- A Duda tool call fails with an authorization/connection error.
- The user asks "how do I connect Duda", "set up the Duda connector", or similar.

## Instructions

1. Try a cheap, read-only call first — `list_sites` with `mode: 'count'`. If it succeeds, the connector is already authorized; skip to step 4.
2. If it fails with an auth/connection error, tell the user the Duda connector needs to be authorized:
   - **claude.ai**: Settings → Connectors → Duda → Connect, then sign in with their Duda partner account.
   - **Claude Code / other non-browser clients**: run `/mcp` (or the client's connector-auth flow) and follow the prompt.
   - This session cannot complete the OAuth flow on the user's behalf — do not ask them for tokens or callback URLs.
3. Once they confirm they've connected, retry `list_sites` (`mode: 'count'`) to verify.
4. Call `list_sites` (default preview fields) so the user can see their site names. Site identifiers (`site_name`) are required by nearly every other Duda tool — the other skills in this plugin will ask you to resolve one via `list_sites` if the user hasn't already named a site.
5. If the user manages sites for clients rather than directly, mention `list_client_sites` as the equivalent entry point for client-scoped access.
