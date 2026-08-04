---
name: blog-workflow
description: Use when the user wants to draft, edit, publish, unpublish, or delete blog posts on a Duda site, enable a blog on a site that doesn't have one yet, or audit existing posts across sites.
---

# Blog workflow

## When to use

- "Write a blog post about [topic] for [site] and publish it"
- "Unpublish/delete the post about [topic]"
- "Does [site] have a blog? List its posts."
- "Set up a blog on [site]"

## Tools

`read_blog` · `list_blog_posts` · `get_blog_post` · `create_blog` · `update_blog` · `create_blog_post` · `update_blog_post` · `publish_blog_post` · `unpublish_blog_post` · `delete_blog_post`

## Instructions

1. Resolve `site_name`.
2. Call `read_blog` to check whether the site already has a blog. If not, confirm with the user, then `create_blog` before creating posts.
3. Draft new posts with `create_blog_post` — posts are created unpublished by default, which is the right default unless the user explicitly asks to publish immediately.
4. Use `update_blog_post` for edits to an existing draft or live post; use `list_blog_posts` / `get_blog_post` to find the post first if the user refers to it by topic rather than ID.
5. Confirm with the user before `publish_blog_post` (goes live), `unpublish_blog_post` (takes it down but keeps the post), and especially `delete_blog_post` (irreversible).
