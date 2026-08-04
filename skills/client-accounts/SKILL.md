---
name: client-accounts
description: Use for agency operations that manage clients at scale — creating client accounts, granting or revoking their access to specific sites, updating site permissions, and generating SSO or password-reset links. This is Duda's core agency-workflow differentiator versus a single-site builder.
---

# Client accounts

## When to use

- "Create a client account for [company] and give them access to [site]"
- "Revoke [client]'s access to [site]"
- "Send [client] a password reset / SSO link"
- "What sites does [client] have access to?"

## Tools

`create_account` · `get_account_details` · `update_account` · `delete_account` · `list_site_permissions` · `get_client_permission_for_site` · `list_client_sites` · `grant_site_access` · `update_site_permissions` · `revoke_site_access` · `create_welcome_link` · `generate_reset_password_link` · `generate_sso_link`

## Instructions

1. If the client doesn't have an account yet, `create_account`, then `grant_site_access` for the site(s) they should manage, setting permissions with `update_site_permissions` as needed.
2. Use `list_client_sites` / `get_client_permission_for_site` to answer "what does this client have access to" questions before making changes — don't assume.
3. `create_welcome_link` is the standard way to onboard a newly-created client account; `generate_reset_password_link` / `generate_sso_link` are for existing accounts.
4. Confirm with the user before `revoke_site_access` or `delete_account` — both remove access and are hard to reverse from this side (the client would need to be re-invited/re-granted).
