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

## 6. Feeding a new place into `street-art/index.html`

`street-art/` is a themed, cross-city page (same idea as `concerts/`) —
it aggregates every street-art photo across place pages into one
filterable gallery, rather than living under one city. It is **not**
a live pull: there's no build step or shared data source anywhere on
this site, so when a place's photos should count as street art, copy
the relevant items into `street-art/index.html`'s own `DATA` array by
hand, same as `index.html`'s `FEATURED_PHOTOS` already duplicates
photos that live in each place's own `DATA`.

- Only copy the photos that are actually street art (murals,
  paste-ups, stencils, stickers, yarn bombs, sculptural pieces on the
  street) — leave out museum/scenery/food shots from that place's
  walk.
- `img` path needs adjusting to point back at the source place's own
  folder, e.g. `img/foo.jpg` becomes `../glasgow/img/foo.jpg`.
- `id` needs a fresh offset per source so ids stay unique once merged
  — check the top-of-DATA comment in `street-art/index.html` for the
  next free hundred (e.g. Edinburgh would be +400).
- Add `city` (display string) and `citySlug` (must match an entry in
  the `CITIES` array in that file) to each copied item — that's what
  drives the city filter chips.
- Add the new city to `CITIES`, and give it a `.chip.city-<slug>` /
  `.tag.city-<slug>` CSS rule with its own accent colour so its badge
  reads distinctly from the others.
- Update the hub's `street-art` entry in `PLACES` (`count`, `blurb`)
  to reflect the new total.

## 7. Adding stories from a phone — `log/index.html`

Unlinked from the site nav and blocked from search engines via
`robots.txt`, so it's only reachable if you know the URL. Bookmark it
on mobile for one-tap access.

- Enter a GitHub personal access token once (fine-grained, scoped only
  to this repo, `Contents: Read and write`) — stored in that browser's
  localStorage, not re-asked after that.
- Resizing happens client-side (Canvas, same ~1280px/quality 0.75
  treatment as everywhere else); uploads go through the GitHub Contents
  API — no server involved.
- Two modes:
  - **Add photos to an existing story** — pick one of the 5 places,
    add photo(s) with a location and optional caption. Splices new
    slide entries into that place's existing `STORIES` block.
  - **Create a brand new standalone story** — a story doesn't have to
    correspond to one of the "Travel stories" cards (the ring feed and
    `PLACES` grid are independent arrays; a `STORIES` entry's `slug`
    field isn't even read by the viewer). Give it a name (auto-slugged
    into an id) and a cover photo, add photo(s) the same way. Images
    land in a new `stories/<id>/img/` folder rather than an existing
    place's own folder. Inserts a whole new entry at the end of
    `STORIES` — checks the id doesn't already collide before uploading
    anything.
- A brand new *place* (its own city page — cover, blurb, full gallery,
  editorial judgment on what's worth including) is a different, bigger
  thing than a standalone story and still needs the fuller pass in
  section 5 above — better suited to a proper session than a phone
  form.
- The repo is public, so the tool's source is technically visible to
  anyone who goes looking at the GitHub file tree directly — the real
  gate is the access token, not the page being hidden. If that's ever
  not private enough, the fix is switching Pages to a GitHub Actions
  deploy so the repo itself can go private.
