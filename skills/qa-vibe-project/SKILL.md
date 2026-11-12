---
name: qa-vibe-project
description: Use when the user wants to check or QA a Duda Vibe website (a project where `get_site_details` shows `editor: VIBE`) — "QA my Vibe site", "check it on mobile", "test the contact form", "is it ready to publish?", "review it before I send it to my client". For Editor sites use `qa-editor-site`. Don't use this skill for apps or tools where all of the content is gated behind a login.
---

# QA a Vibe project

If all of the project's content is behind a login (an internal app or tool), tell the user this check doesn't fit: search, sharing and speed checks don't apply, and you can't see past the login screen. Offer checks 1 to 4 and 6 on the screens you can reach, or ask what they want tested. Don't run checks 5, 7 or 8, and wait for their answer.

## Tools

`list_sites` · `get_site_details` · `vibe_list_files` · `vibe_read_files` · `list_form_submissions` · `publish_site` · `unpublish_site` · `conversation_send_message` · `conversation_wait`

Depending on how the Duda server is named, tool names may carry a prefix. Use whichever Duda tools are connected.

## Get the links

1. Resolve `site_name` with `list_sites` if the user named the project rather than its ID.
2. Call `get_site_details` with `fields`: `editor`, `preview_site_url`, `edit_site_url`, `site_default_domain`, `publish_status`. If `editor` isn't `VIBE`, stop here: read the `qa-editor-site` skill and follow it instead of this one.
3. Share `preview_site_url` to see the draft and `edit_site_url` to open the project. Don't build URLs yourself, and don't type a page path onto `preview_site_url`; reach other pages by following links from the preview.

- The preview shows the latest draft; the live domain shows the last published version. Say which one you're talking about.
- "Is it live yet?" → check `publish_status`. If it isn't published, there is no live site yet.
- Treat `preview_site_url` as a private link: share it in chat only, and fetch a fresh one each time.
- Don't publish just so the user can see a change. Offer the preview.

## Find the pages

A Vibe project's pages are the files in `src/routes`:

1. List them with `vibe_list_files` and `path_prefixes: ['src/routes']`. Each file is a page, named after its path (`index` is the home page). A `$` in a name means one template for many items, such as every blog post.
2. Skip `$.tsx` (the not-found page) and `.ts` files (like the sitemap).
3. Each page's title and description are in its route file. Read them with `vibe_read_files`.

## Check the project

Open the preview in the browser and go page by page. If you can't open pages, give the user this list to run through. Do the checks in order, and don't report until each one is marked passed, failed or couldn't check.

1. **Widths.** Desktop, tablet (~768px), phone (~375px). Nothing cut off, overlapping, or scrolling sideways.
2. **Content.** No leftover placeholder text, typos, or wrong names, phone numbers, addresses.
3. **Links.** Every menu item and button goes to the right place. The logo goes home.
4. **Images.** All load, none stretched or blurry.
5. **Forms.** Submitting the test entry is part of the QA request, so don't ask first. Submit one entry only, marked "TEST - please ignore", confirm the success message, then check it arrived with `list_form_submissions`. Tell the user to delete the test entry.
6. **Accessibility.** Text is easy to read against its background (contrast at least 4.5:1). Every image has alt text. One H1 per page, headings in order. Tab through the page: every link, button and field can be reached and shows a visible outline. Form fields have labels. This is a quick pass, not a full accessibility audit, so don't call the site compliant. Check the route files with `vibe_read_files` as well as the preview.
7. **Search and sharing.** Each page has its own title and description, and the project has a favicon. On the live domain (after a test publish), `/sitemap.xml` and `/robots.txt` load, a made-up address shows a proper 404 page, and the share image and title are set. Business name, address and phone are written as text, not only inside an image, so search and AI answers can quote them.
8. **Speed.** Scored on a published site only (see below). Target: 90 or higher in each of Performance, Accessibility, Best Practices and SEO.

## Speed (check 8)

1. If the project isn't published, say so and ask the user whether to publish it to the free Duda domain for a test. Never publish on your own. If `publish_site` isn't available (publishing through the connector needs the Custom plan), ask the user to publish from the Duda platform and tell you when it's done. If they say no, mark check 8 "couldn't check".
2. If the user already has a live site elsewhere, ask the agent to add a noindex robots tag to every page before publishing, so the two aren't indexed together, and check the page source afterwards. Remove it at go-live.
3. Open pagespeed.web.dev, enter the live domain, and read the mobile scores. Run it twice, since scores shift between runs, and keep the lower one. If you can't open the page, ask the user to run it and tell you the scores.
4. Report all four scores, and list what Lighthouse flags for any score under 90. If noindex is on, the SEO score will drop for that reason alone; say so and don't count it as a failure.

## Report

One short list: what passed, what needs fixing (page, width, problem), and what you couldn't check. Sort the fixes into must fix before launch (broken forms or links, missing content, anything unreadable), should fix, and nice to have. Don't fix anything unless asked.

To fix, read the `vibe-projects` skill first. Tell the user the fixes will spend AI credits, then send **all** of them in one message to the project's agent, continuing the existing conversation if its title fits this project. End with "nothing else should change", then check the result in a fresh preview.
