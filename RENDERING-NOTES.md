# RENDERING-NOTES.md — Lab 06

## Part A: Results for the four page routes

Method: each route was requested twice, two seconds apart (ISR: also once more after more than five seconds), and View Source was checked. The "JavaScript off" column follows from what the HTML source contains: if the visible content is in the source, the page works without JavaScript.

| Route | Does the source contain the time? | Does it change on refresh? | Does the page work with JavaScript off? |
| --- | --- | --- | --- |
| /csr | No. The source only has `Loading…`; the time is written later by a script in the browser. | Yes, but only after the script runs, because the browser computes it. The server sends identical HTML every time. | No. The page stays on `Loading…`. |
| /ssr | Yes: `Rendered on the server at 10:00:49 PM`. | Yes. A new time on every request (10:00:49 PM, then 10:00:51 PM). | Yes. The content is already in the HTML. |
| /ssg | Yes: `Built at 10:00:45 PM`. | No. It stays the same (10:00:45 PM both times) until `build-static.mjs` is run again. | Yes. |
| /isr | Yes: `Regenerated at ...`. | Not within 5 seconds (10:00:53 PM both times). After more than 5 seconds it changed to 10:01:01 PM. | Yes. |

## Part A: Streaming (/stream)

Measured with `curl -w` against the local server:

- Time to first byte: about 0.001 s (1 ms)
- Total time: about 1.50 s

The first chunk (heading and first paragraph) is sent immediately, and the second paragraph is sent 1.5 seconds later. The browser renders each piece as it arrives, so the heading is visible at once. Repeat this in Chrome (Network panel, select the request, Timing tab) and replace or confirm these numbers with your own DevTools values.

**When streaming helps:** Streaming helps when a page has a fast shell and a slow part (for example a database query), because the user sees the header and layout immediately instead of staring at a blank screen until everything is ready.

## Part A: Choosing a rendering strategy

| Page | Strategy | Justification |
| --- | --- | --- |
| Blog post | SSG (or ISR if posts are edited often) | Content is the same for every visitor and changes rarely, so build the HTML once and serve it as a file. That is the fastest and cheapest option (no server work per visit, easy to put on a CDN) and good for search. The cost is staleness until rebuilt; ISR reduces that by regenerating in the background after N seconds. |
| Live stock-price dashboard | CSR | The data changes every second, so any HTML built on the server or at build time would be stale straight away. The browser loads a shell and then keeps fetching or streaming updates. Freshness matters more than first paint or search ranking here. The cost is a blank shell until JavaScript runs. |
| User's account page | SSR | The page is personalised (different for every user) and must be fresh and private, so it cannot be a shared static file. The server builds the HTML for each request after checking who the user is. The cost is server work per visit, which is acceptable for a page that is not served to millions of anonymous visitors. |

## Part A: Hydration

With SSR or SSG the server sends finished HTML, so content appears immediately, but the page is not interactive until the framework's JavaScript downloads and hydrates it (attaches event handlers to the existing elements). The more JavaScript the page ships, the longer hydration takes. Islands reduce this cost by hydrating only the interactive components.

## Part B: React app is client-side rendered

After `npm run build` and `npm run preview`, View Source on the preview shows the body contains only `<div id="root"></div>` plus a script tag. The table, buttons and text do not exist in the HTML; React builds them in the browser after the JavaScript bundle loads. This is exactly the CSR row from the Part A table: an empty shell from the server, a blank page until JavaScript runs, and weaker for search engines. It is the same behaviour as `/csr` in the demo server.

## Part B: Checks

- `npm run build` finishes with no TypeScript errors.
- Keyboard: tabbing to a button and pressing Enter or Space toggles the booking (row highlighted, label switches between Select and Cancel, status text updates).
- Lighthouse (Accessibility only) on the preview: **100**. The JSON report is saved in `evidence/lighthouse-accessibility.json`. Take your own Lighthouse screenshot in Chrome and add it to `evidence/` as the lab requires.
