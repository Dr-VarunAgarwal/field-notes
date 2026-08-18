# Site-wide standards checklist

Things every page on wander.varunagarwal.com should have, so they don't
get forgotten as more places/sections get added. When starting a new
page, copy the relevant block from an existing one (e.g.
`khajuraho/index.html`) rather than writing from scratch.

## 1. Corner monogram ("VA" mark)

Every subpage gets a circular initials badge, top-right of its sticky
header — see `.header-mark` in any subpage:

```html
<div class="header-top">
  <span class="header-mark">VA</span>
</div>
```
```js
const MONOGRAM_TEXT = 'VA'; // change if you ever want a different mark
```

## 2. Navigation

Two different patterns depending on where you are:

- **Subpages** (`london/`, `khajuraho/`, etc.): `<nav class="top-nav">` —
  a "← field notes" back-link plus the album name, sticky at the top.
- **Hub** (`index.html`): sidebar with `lucidité` home-link (top-left), a
  "Navigate" dropdown pill for Books/Notes, and the VA monogram
  (top-right) for Contact — see `.sidebar-head`.

## 3. Open Graph + Twitter Card meta tags

So link previews on WhatsApp/iMessage/Slack/Discord/X show a title,
description and image instead of nothing. Add right after `<title>` in
every page's `<head>`:

```html
<meta name="description" content="One or two sentences about this page.">
<meta name="theme-color" content="#______">
<meta property="og:type" content="website">
<meta property="og:site_name" content="Wander">
<meta property="og:url" content="https://wander.varunagarwal.com/<slug>/">
<meta property="og:title" content="<Place Name> · Wander">
<meta property="og:description" content="Same as the description above.">
<meta property="og:image" content="https://wander.varunagarwal.com/<slug>/img/<best-photo>.jpg">
<meta name="twitter:card" content="summary_large_image">
<meta name="twitter:title" content="<Place Name> · Wander">
<meta name="twitter:description" content="Same as the description above.">
<meta name="twitter:image" content="https://wander.varunagarwal.com/<slug>/img/<best-photo>.jpg">
```

- `og:image`/`twitter:image` must be an **absolute URL** — the crawler
  fetches it directly, with no page context to resolve a relative path.
- Pick one strong, representative photo. Wide/landscape crops preview
  best.
- After publishing, if a link you'd already shared keeps showing a stale
  preview, use [Facebook's Sharing Debugger](https://developers.facebook.com/tools/debug/)
  to force a re-scrape (WhatsApp shares similar crawler infrastructure).

## 4. `theme-color`

Tints the mobile browser's address-bar chrome to match the page:

```html
<meta name="theme-color" content="#______">
```

Set it to whatever's actually visible at the very top of *that* page —
check the page's own `body{ background: }` (or, on the hub, the sidebar
background on mobile, since that's what's visible first) — not just any
color from the palette. Support is strongest on Android Chrome/Edge;
iOS Safari's handling has been inconsistent across versions.

## 5. Adding a new live place to the hub

When a place goes from draft to live (e.g. Skye's promotion), check
whether it should also be added to these arrays in `index.html`:

- `PLACES` — flip `status` to `'live'`, fill in `cover`/`blurb`/`count`.
- `FEATURED_PHOTOS` — a couple of strong shots, captions optional.
- `HEADER_PHOTOS` — if it has a photo that earns the rare full-bleed
  header treatment.
- `STORIES` — a story user/slide set for the ring feed.
- The ticker pulls from all of `PLACES` (live + draft) automatically —
  nothing to do there beyond adding the place.
