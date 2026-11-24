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

Before you start, send the user one message that lists what the check covers (widths, content, links, images, forms, readability, search and sharing, speed) and asks:
- Which version to check: the live site or the latest draft in the preview. Recommend the live site if the project is published (`site_default_domain`, or the connected domain, from `get_site_details`). An unpublished project has only the draft.
- Whether to include the form test, which submits a real entry and sends an email.
- Whether to include the speed check. It needs a published project, so for an unpublished one it means a temporary publish to the free Duda domain, which you unpublish afterwards. Never publish without their yes.

Skip anything they decline, and mark anything that needs the live site and wasn't checked as "couldn't check".

Open the preview in the browser and go page by page. If you can't open pages, give the user this list to run through. Do the checks in order, and don't report until each one is marked passed, failed or couldn't check.

1. **Widths.** Desktop, tablet (~768px), phone (~375px). Nothing cut off, overlapping, or scrolling sideways.
2. **Content.** No leftover placeholder text ("Lorem ipsum", "Your business name here"), no typos or broken sentences. Business name, address and contact information are consistent across all site content, including in the header, footer and page content. `vibe_read_files` reads the page code. List the details you found and ask the user to confirm them.
3. **Links.** Every menu item and button goes to the right place. The logo goes home.
4. **Images.** All load, none stretched or blurry.
5. **Forms.** List each form's fields, labels and success message and ask the user if that is what they want to collect and show. Then, if the user agreed to a test submission, test that the notification reaches the right inbox: submit one entry marked "TEST - please ignore", confirm the success message, and ask them to confirm the email arrived at the address they expect (check spam too). Check it with `list_form_submissions` and tell the user to delete the entry. If a CAPTCHA appears, ask the user to complete it; you can't. If the entry isn't in `list_form_submissions`, don't call the form broken: it may not be recorded until the project is published. Ask the user to check the inbox, and mark the form "couldn't confirm" if no email arrives. If the preview refuses to send ("This preview can't send"), test on the published site instead, or mark the form test "couldn't check".
6. **Readability.** Contrast: text is at least 4.5:1 against its background (3:1 for large text). Measure it with the contrast snippet below, run in the browser on each page; it also reads inside the preview's frames. If it checks only a handful of text elements, mark contrast "couldn't check". Alt text: every image has it. Headings: one H1 per page, in order. Labels: every form field has one. Check the route files with `vibe_read_files` as well as the preview. Don't call the site accessibility-compliant.
7. **Search and sharing.** Each page has its own title and description, and the project has a favicon. On the live domain, `/sitemap.xml` loads, a made-up address shows a proper 404 page, and the share image and title are set. Business name, address and phone are written as text, not only inside an image, so search and AI answers can quote them.
8. **Speed.** Scored on a published site only (see below). Target: 90 or higher in each of Performance, Accessibility, Best Practices and SEO.

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

## Speed (check 8)

1. If the project isn't published, ask whether to publish it temporarily for the test. If they already have a live site elsewhere with the same content, warn that the free-domain copy could be indexed next to it, and offer to skip or to publish with noindex (the project's agent adds it, which spends AI credits, so ask first). If `publish_site` isn't available (publishing through the connector needs the Custom plan), ask them to publish it themselves and tell you when it's done. If they say no, mark check 8 "couldn't check".
2. Open pagespeed.web.dev, enter the live domain, and read the mobile scores. Run it twice, since scores shift between runs, and keep the lower one. If you can't open the page, ask the user to run it and tell you the scores.
3. Report all four scores, and list what Lighthouse flags for any score under 90. If noindex is on, the SEO score drops for that reason alone; say so and don't count it as a failure.
4. If you published the project for this test, ask whether to unpublish it now (`unpublish_site`). If you added noindex and they unpublish, offer to remove it through the project's agent (AI credits, so ask first). If the project stays published with noindex, remind them again to remove it at go-live.

## Report

One short list: what passed, what needs fixing (page, width, problem), and what you couldn't check. Sort the fixes into must fix before launch (broken forms or links, missing content, anything unreadable), should fix, and nice to have. Don't fix anything unless asked.

To fix, read the `vibe-projects` skill first. Tell the user the fixes will spend AI credits and wait for their approval. Then send **all** of them in one message to the project's agent, continuing the existing conversation if its title fits this project. End with "nothing else should change", then check the result in a fresh preview.
