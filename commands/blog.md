---
description: Draft, edit, or publish a blog post on a Duda site.
---

# Blog

## Required input

**Site name** and either a **topic/brief** (to draft a new post) or a way to identify an **existing post** (to edit/publish/unpublish/delete it). If missing, ask.

## Workflow

Use the `blog-workflow` skill for the full procedure: confirm the site has a blog (`read_blog`, creating one with `create_blog` if needed and confirmed), then draft with `create_blog_post` or locate the existing post with `list_blog_posts`/`get_blog_post` before editing.

Confirm with the user before `publish_blog_post`, `unpublish_blog_post`, or `delete_blog_post`.
