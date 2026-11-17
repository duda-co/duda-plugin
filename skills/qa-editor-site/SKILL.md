---
name: qa-editor-site
description: Use when the user wants to check or QA a Duda Editor site (not a Vibe project) — "QA my site", "check it on mobile", "test the contact form", "is it ready to publish?", "review it before I send it to my client". For Vibe projects use `qa-vibe-project`.
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
5. **Forms.** Each form asks for the right things (no missing or unneeded fields), fields have clear labels, and the success message reads the way the user wants a visitor to see it. Ask the user where form emails should go and check it matches. Duda handles delivery, so don't submit test entries unless the user asks. If they do, submit one entry marked "TEST - please ignore", then check it with `list_form_submissions` and tell the user to delete it.
6. **Readability.** Text is easy to read against its background (contrast at least 4.5:1, or 3:1 for large text). To measure it, run the contrast snippet below in the browser on each page. Every image has alt text (`get_page_elements`). One H1 per page, headings in order. This is a quick pass, not a full accessibility audit, so don't call the site compliant.
7. **Search and sharing.** Every page has its own title and description (`list_pages`), written for people and specific to the page. Pages meant to be hidden are set to draft, not live. The share image and title are set. Business name, address and phone are written as text, not only inside an image, so search and AI answers can quote them.

### Contrast snippet

Run this in the browser on a page. It lists text that fails, with its contrast ratio. It can't see text over images or gradients, so check those by eye.

```js
const rgb=c=>c.match(/[\d.]+/g).map(Number);
const lum=([r,g,b])=>[r,g,b].reduce((a,v,i)=>a+[.2126,.7152,.0722][i]*((v/=255)<=.03928?v/12.92:((v+.055)/1.055)**2.4),0);
const bgOf=e=>{for(;e;e=e.parentElement){const c=rgb(getComputedStyle(e).backgroundColor);if(c[3]!==0)return c}return[255,255,255]};
[...document.querySelectorAll('body *')].filter(e=>[...e.childNodes].some(n=>n.nodeType===3&&n.textContent.trim())).map(e=>{const s=getComputedStyle(e),[a,b]=[lum(rgb(s.color)),lum(bgOf(e))].sort((x,y)=>y-x),big=parseFloat(s.fontSize)>=24||(parseFloat(s.fontSize)>=18.66&&+s.fontWeight>=700);return{text:e.textContent.trim().slice(0,40),ratio:+((a+.05)/(b+.05)).toFixed(2),need:big?3:4.5}}).filter(r=>r.ratio<r.need)
```

## Report

One short list: what passed, what needs fixing (page, width, problem), and what you couldn't check. Sort the fixes into must fix before launch (broken links, missing content, anything unreadable), should fix, and nice to have. Don't fix anything unless asked.

To fix, use the site-editing tools (`update_elements`, `update_page_settings`, and the like). Call `get_element_schema` first for the element types you will change, since the allowed properties differ by type. Then fetch a fresh preview link. There's no credit cost.
