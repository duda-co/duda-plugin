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

Before you start, send the user one message that lists what the check covers (widths, content, links, images, forms, readability, search and sharing) and asks:
- Which version to check: the live site or the latest draft in the preview. Recommend the live site if the project is published (`site_default_domain`, or the connected domain, from `get_site_details`). An unpublished project has only the draft.
- Whether to include the form test, which submits a real entry and sends an email.

Skip anything they decline, and mark anything that needs the live site and wasn't checked as "couldn't check".

Use `list_pages` for the page list (path, title, description, draft status). Open the preview in the browser and go page by page. If you can't open pages, give the user this list to run through. Do the checks in order, and don't report until each one is marked passed, failed or couldn't check.

1. **Widths.** Desktop, tablet (~768px), phone (~375px). Nothing cut off, overlapping, or scrolling sideways.
2. **Content.** No leftover placeholder text ("Lorem ipsum", "Your business name here"), no typos or broken sentences. Business name, address and contact information are consistent across all site content, including in the header, footer and page content. `get_page_elements` reads a page's text. Compare with the business details saved on the site (`site_business_info` in `get_site_details`); if they're empty, say so and suggest the user fills them in. List the details you found and ask the user to confirm them.
3. **Links.** Every menu item and button goes to the right place. The logo goes home.
4. **Images.** All load, none stretched or blurry.
5. **Forms.** List each form's fields, labels and success message and ask the user if that is what they want to collect and show. Then, if the user agreed to a test submission, test that the notification reaches the right inbox: submit one entry marked "TEST - please ignore", confirm the success message, and ask them to confirm the email arrived at the address they expect (check spam too). Check it with `list_form_submissions` and tell the user to delete the entry.
6. **Readability.** Contrast: text is at least 4.5:1 against its background (3:1 for large text). Measure it with the contrast snippet below, run in the browser on each page; it also reads inside the preview's frames. If it checks only a handful of text elements, mark contrast "couldn't check". Alt text: every image has it (`get_page_elements`). Headings: one H1 per page, in order. Don't call the site accessibility-compliant.
7. **Search and sharing.** Every page has its own title and description (`list_pages`), written for people and specific to the page. Pages meant to be hidden are set to draft, not live. The share image and title are set. Business name, address and phone are written as text, not only inside an image, so search and AI answers can quote them.

### Contrast snippet

Run this in the browser on a page. It lists visible text that likely fails, with its contrast ratio, and marks text over an image. It is a quick check, not a WCAG audit: text over images, gradients, logos, disabled controls and form inputs need a human look, and for an authoritative report the user can run Lighthouse in Chrome DevTools. If you can't run it, mark contrast "couldn't check" instead of guessing, and report how many text elements it checked.

```js
const ctx=document.createElement('canvas').getContext('2d',{willReadFrequently:true});ctx.canvas.width=ctx.canvas.height=1;
const rgb=c=>{ctx.clearRect(0,0,1,1);ctx.fillStyle='#000';ctx.fillStyle=c;ctx.fillRect(0,0,1,1);const d=ctx.getImageData(0,0,1,1).data;return[d[0],d[1],d[2],d[3]/255]};
const lum=c=>c.slice(0,3).reduce((a,v,i)=>a+[.2126,.7152,.0722][i]*((v/=255)<=.04045?v/12.92:((v+.055)/1.055)**2.4),0);
const mix=(f,b,a=f[3]??1)=>[0,1,2].map(i=>f[i]*a+b[i]*(1-a));
const gcs=e=>e.ownerDocument.defaultView.getComputedStyle(e);
const bg=e=>{const l=[];let img=false;for(;e;e=e.parentElement){const s=gcs(e),c=rgb(s.backgroundColor);if(s.backgroundImage!=='none')img=true;if(c[3]!==0){l.push(c);if(c[3]===1)break}}return{c:l.reduceRight((b,x)=>mix(x,b),[255,255,255]),img}};
const op=e=>{let o=1;for(;e;e=e.parentElement)o*=+gcs(e).opacity;return o};
const docs=[document,...[...document.querySelectorAll('iframe')].filter(f=>f.clientWidth>0).map(f=>{try{return f.contentDocument}catch(e){return null}}).filter(Boolean)];
let checked=0;
const failures=docs.flatMap(d=>[...d.querySelectorAll('body *')].filter(e=>e.checkVisibility({opacityProperty:true,visibilityProperty:true})&&!e.closest(':disabled,[aria-disabled="true"]')&&gcs(e).webkitBackgroundClip!=='text'&&rgb(gcs(e).color)[3]>0&&[...e.childNodes].some(n=>n.nodeType===3&&n.textContent.trim())).map(e=>{checked++;const s=gcs(e),b=bg(e),f=rgb(s.color),[x,y]=[lum(mix(f,b.c,f[3]*op(e))),lum(b.c)].sort((p,q)=>q-p),big=parseFloat(s.fontSize)>=24||(parseFloat(s.fontSize)>=18.66&&+s.fontWeight>=700);return{view:d===document?'page':((d.defaultView.frameElement.src.match(/dm_device=(\w+)/)||[])[1]||'frame'),text:e.textContent.trim().slice(0,40),raw:(x+.05)/(y+.05),need:big?3:4.5,...(b.img&&{note:'over an image, check by eye'})}}).filter(r=>r.raw<r.need||r.note).map(({raw,...r})=>({...r,ratio:+raw.toFixed(2)})));
JSON.stringify({checked,failureCount:failures.length,failures})
```

## Report

One short list: what passed, what needs fixing (page, width, problem), and what you couldn't check. Sort the fixes into must fix before launch (broken forms or links, missing content, anything unreadable), should fix, and nice to have. Don't fix anything unless asked.

To fix, use the site-editing tools (`update_elements`, `update_page_settings`, and the like). Call `get_element_schema` first for the element types you will change, since the allowed properties differ by type. Then fetch a fresh preview link. There's no credit cost.
