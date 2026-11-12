---
name: qa-editor-site
description: Use when the user wants to preview, check, or QA a Duda Editor site (not a Vibe project) — "show me the preview", "give me the editor link", "QA my site", "check it on mobile", "test the contact form", "is it ready to publish?", "review it before I send it to my client". For Vibe projects use `qa-vibe-project`.
---

# QA an Editor site

## Tools

`list_sites` · `get_site_details` · `list_pages` · `get_page_elements` · `get_element_schema` · `update_page_settings` · `list_form_submissions` · `publish_site` · `unpublish_site`

Depending on how the Duda server is named, tool names may carry a prefix. Use whichever Duda tools are connected.

## Get the links

1. Resolve `site_name` with `list_sites` if the user named the site rather than its ID.
2. Call `get_site_details` with `fields`: `editor`, `preview_site_url`, `edit_site_url`, `site_default_domain`, `publish_status`. If `editor` is `VIBE`, stop here: read the `qa-vibe-project` skill and follow it instead of this one.
3. Share `preview_site_url` to see the draft and `edit_site_url` to edit by hand. Don't build URLs yourself, and don't type a page path onto `preview_site_url`; reach other pages by following links from the preview.

- Changes stay drafts until `publish_site`. The preview shows the draft; the live domain shows the last published version. Say which one you're talking about.
- "Is it live yet?" → check `publish_status`. If it isn't published, there is no live site yet.
- Treat `preview_site_url` as a private link: share it in chat only, and fetch a fresh one each time.
- Don't publish just so the user can see a change. Offer the preview.

## Check the site

Use `list_pages` for the page list (path, title, description, draft status). Open the preview in the browser and go page by page. If you can't open pages, give the user this list to run through. Do the checks in order, and don't report until each one is marked passed, failed or couldn't check.

1. **Widths.** Desktop, tablet (~768px), phone (~375px). Nothing cut off, overlapping, or scrolling sideways.
2. **Content.** No leftover placeholder text, typos, or wrong names, phone numbers, addresses. `get_page_elements` reads a page's text.
3. **Links.** Every menu item and button goes to the right place. The logo goes home.
4. **Images.** All load, none stretched or blurry.
5. **Forms.** Submitting the test entry is part of the QA request, so don't ask first. Submit one entry only, marked "TEST - please ignore", confirm the success message, then check it arrived with `list_form_submissions`. Tell the user to delete the test entry.
6. **Accessibility.** Text is easy to read against its background (contrast at least 4.5:1). Every image has alt text (`get_page_elements`). One H1 per page, headings in order. Tab through the page: every link, button and field can be reached and shows a visible outline. Form fields have labels. This is a quick pass, not a full accessibility audit, so don't call the site compliant.
7. **Search and sharing.** Every page has its own title and description (`list_pages`). Pages meant to be hidden are set to draft, not live. If the site is already published, on the live domain `/sitemap.xml` and `/robots.txt` load, a made-up address shows a proper 404 page, and the share image and title are set. Business name, address and phone are written as text, not only inside an image, so search and AI answers can quote them.

## Report

One short list: what passed, what needs fixing (page, width, problem), and what you couldn't check. Sort the fixes into must fix before launch (broken forms or links, missing content, anything unreadable), should fix, and nice to have. Don't fix anything unless asked.

To fix, use the site-editing tools (`update_elements`, `update_page_settings`, and the like). Call `get_element_schema` first for the element types you will change, since the allowed properties differ by type. Then fetch a fresh preview link. There's no credit cost.
