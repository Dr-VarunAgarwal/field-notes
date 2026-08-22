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

- `PLACES` — flip `status` to `'live'`, fill in `cover`/`blurb`/`count`,
  and set `kind` (see below).
- `FEATURED_PHOTOS` — a couple of strong shots, captions optional.
- `HEADER_PHOTOS` — if it has a photo that earns the rare full-bleed
  header treatment.
- `STORIES` — a story user/slide set for the ring feed. Insert it in
  array position by its latest slide date, newest first (the feed
  renders in array order, it isn't sorted at runtime).
- The ticker pulls from all of `PLACES` (live + draft) automatically —
  nothing to do there beyond adding the place.

**`kind` — `'album'` vs `'walk'`** (decided 2026-08-22): every live
place is one of two formats, and the hub splits them into separate
"Albums" / "Photo Walks" grid sections accordingly.
- `kind:'album'` (the default — most places) — a grid gallery page
  (filter chips, click-to-open drawer), like `london/`, `camden/`,
  `khajuraho/`, `udupi/`, `glasgow/`, `concerts/`. Built by copying an
  existing album page's structure.
- `kind:'walk'` — a day-journal page with prose, hand-placed photos,
  and a real Leaflet route map, like `skye/` or `glasgow-edinburgh/`.
  Reserve this format for a trip with a genuine day-by-day arc and a
  real route worth mapping — most batches are still albums. Built by
  copying `skye/index.html`'s structure (masthead, intro, route-map
  figure, `.day` sections, DATA-by-id hydration script) rather than a
  grid album's.
- The walk's `count` field reads as `'N days · M photos'` instead of
  an album's `'M pieces'`, since a walk isn't really counted by piece.

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
- "What are you doing?" is three tap-buttons (not a `<select>` — long
  labels read better as a stacked list of full-width buttons on a phone
  than as dropdown option text), backed by a hidden `#mode` input so the
  rest of the script's `document.getElementById('mode').value` reads
  didn't need to change.
- If a picked photo has GPS EXIF (JPEG only — HEIC stores it in a
  different, non-APP1 container this tool doesn't parse), the location
  field auto-fills via reverse geocoding (OpenStreetMap Nominatim, no
  key needed) once the field is empty — never overwrites something
  already typed. Falls back to raw decimal coordinates if the lookup
  fails; silently leaves the field blank if there's no GPS tag at all
  (very common for photos that have been through WhatsApp/Instagram).
- After any publish, a "Check if live" control appears: GitHub Pages
  rebuilds the whole site per push (no per-file deploy), so confirming
  one thing we just changed is visible on the public site (the new
  image's URL, or — for a reorder — the live `STORIES` id order) means
  the whole build finished. Needs no token, since Pages content is
  public regardless of repo visibility.
- "▶ Preview story" (existing/new modes only) mirrors the real
  `#story-modal` viewer from the hub — same markup, CSS class names,
  and tap-through mechanics, copied over rather than reimplemented, so
  it looks and behaves identically. Shows the pending photos (straight
  from their resized blobs — works before publishing) appended after
  that story's already-live slides in "existing" mode, so a batch reads
  in its real final order. Loops instead of paging to a "next user"
  (there's only one story to preview solo). Not available in reorder
  mode — there's no slide content to preview there, just the id list
  the reorder screen already shows.
- Three modes:
  - **Add photos to an existing story** — the place dropdown is
    populated live from the hub's own `STORIES` array (fetched once,
    cached for the session), not a hardcoded list, so it can't drift
    out of sync with the site again. Add photo(s) with a location
    (autosuggested from that story's own existing slides) and optional
    caption; each gets a `date` stamped as the upload time. Splices new
    slide entries into that place's existing `STORIES` block. Images
    always upload to `stories/<id>/img/`, even for places that also
    have their own full album page (khajuraho/skye/glasgow/...) —
    Stories are meant to be a separate curated edit (§8), not mixed
    into that page's own numbered batch files.
  - **Create a brand new standalone story** — a story doesn't have to
    correspond to one of the "Travel stories" cards (the ring feed and
    `PLACES` grid are independent arrays; a `STORIES` entry's `slug`
    field isn't even read by the viewer). Give it a name (auto-slugged
    into an id) and a cover photo, add photo(s) the same way. Images
    land in a new `stories/<id>/img/` folder rather than an existing
    place's own folder. Inserts a whole new entry at the end of
    `STORIES` — checks the id doesn't already collide before uploading
    anything.
  - **Rearrange story order** — the ring feed renders `STORIES` in
    array order (§5), so this is reordering that array directly. A
    list of every current story (avatar + name) with ▲/▼ buttons per
    row; Save re-fetches and re-splits the array's raw entry text fresh
    (rather than trusting whatever was cached when the list loaded), so
    a story added by someone else in the meantime can't get clobbered —
    it's just appended at the end if it wasn't part of the order being
    saved.
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

## 8. Batch-processing a new raw photo/video dump

The repeatable workflow for turning a fresh raw batch (dropped somewhere
under `field-notes/`, past ones named `assess`, `check` — look for a new
folder, or ask if unclear) into a live section. Do this in small batches,
committing after each logical chunk — never attempt the whole batch in one
pass.

**Current site shape** (check this hasn't drifted before trusting it):
- Hub `index.html` — `PLACES` array, split by `kind` (§5) into Albums
  and Photo Walks. Live albums: Shoreditch, Camden, Khajuraho, Concerts,
  Udupi, Glasgow, Manchester. Live walks: Isle of Skye, Glasgow & Edinburgh.
  Story-only (§7, no full page): Udupi-Manipal, Edinburgh, Pushkar, Parvati
  Valley, Mauritius. Draft (not yet promoted): Rishikesh, Mussoorie, Dehradun.
- Album pages (`london/`, `camden/`, `khajuraho/`, `udupi/`, `glasgow/`,
  `manchester/`, each a standalone HTML file) — own theme (CSS custom
  properties, fonts, category glyphs, button/chip style; don't reuse one
  city's palette or chrome for another — each place should read as
  visually distinct, not a reskin) and a `DATA` array: `id, img,
  type('photo'|'video'), category, artist, title, location, notes`
  (Varun's own words, blank `""` if none), `altText` (Claude's
  description of the piece — wired into the image's accessible name
  (`aria-label`/`img.alt`), never shown as visible copy on the page, kept
  separate from `notes`), `tags, ratio('w/h'), date` (ISO, from EXIF),
  `order, colorFrame` (optional `'r,g,b'`, only on the most vivid
  pieces). Each such page also has its own `extractVividColor` JS
  function (in-browser version of the same algorithm — mirror it in
  Python for the baked-in `colorFrame` value). `title` renders only when
  set — no placeholder text when it's blank.
- Walk pages (`skye/`, `glasgow-edinburgh/`) — same `DATA` fields as an
  album page, but HTML is hand-placed into `.day` sections rather than
  generated into a grid, and DATA is read by `id` to hydrate each
  hand-placed `<figure data-id="N">` rather than to build the layout.
  Adds a real Leaflet route map (`STOPS`/`COLORS`, own script block,
  independent of DATA) — reuse the two-tileLayer CARTO setup and swap
  in that trip's own coordinates.
- `concerts/index.html` — different schema (ticket-stub style): `id,
  artist, img, type, video, extras` (array of extra photo paths,
  side-scrolling in the drawer — must include the cover photo itself in
  that strip, not just the extras, or the scroll breaks one direction),
  `venue, city, date, billing, notes, tags, order`. No `altText` field
  — keep commentary out of `notes` here too, unless it's a plain fact
  ("Lollapalooza India, day one").
- `street-art/index.html` — themed cross-city aggregator (§6 above), own
  `DATA` extending the city-page schema with `city, citySlug, curated`.
  Not a live pull — when a place's new photos are genuinely street art
  (murals, paste-ups, stencils, stickers, sculptural pieces), copy them
  in by hand per §6's id-offset/city-chip rules. Leave out
  museum/scenery/food shots.
- `stories/<id>/img/` + the hub's `STORIES` array + `log/index.html`
  (§7 above) — the Stories ring feed. **Known rule (decided
  2026-08-22):** Stories must be a curated, distinct edit — a handful of
  the strongest shots, different framing/sequencing/captions than the
  gallery, possibly material that never made the album — not a
  copy-pasted subset of that place's own `DATA`. When a batch produces
  or updates a live place, don't auto-populate `STORIES` by lifting
  entries straight out of the new `DATA`; curate it as its own pass.
- `fonts/` — custom TTFs via `@font-face` in a `<style>` block, never
  inside a `data:` URI stylesheet (relative `url()` paths don't resolve
  there).

**Steps:**

1. **Discover** — find the new folder. Get EXIF `DateTimeOriginal` for
   photos (PIL) and `creation_time` for videos (`ffprobe`). Group by
   timestamp proximity as a first guess at bursts — but never discard a
   "duplicate" without actually looking at both images; most
   same-timestamp photos turn out to be different subjects shot seconds
   apart, not real bursts.
2. **Visually review** — build contact-sheet grids (PIL, batches of 6)
   and read them back rather than eyeballing files one at a time. For
   video, pull identifying frames with
   `ffmpeg -ss <t> -update 1 -q:v 2 -vframes 1`, trying a few timestamps
   if the first is dark/blurry.
3. **Identify carefully** — trust on-screen text (banners, jumbotrons,
   stage names) over guessing from vibes. When identification is
   genuinely ambiguous (which artist, which extra act, what a "burst"
   resolves to), stop and ask rather than guessing. Never fabricate
   venue/city for real events with no visual evidence — leave it blank
   instead.
4. **Process images** — PIL, `ImageOps.exif_transpose`, resize to
   ~1280px max dimension, JPEG quality ~72-78. Video meant for playback
   (not just a poster frame): transcode with ffmpeg to h264/aac +
   `-movflags +faststart`, scaled to ~640-720px wide.
5. **`colorFrame`** — for a handful of the most vivid photos per batch
   (not all), compute a dominant vivid color via HSV-bin weighting,
   mirroring that page's own `extractVividColor` JS function in Python.
6. **Write `DATA` entries** — match the schema/style of whichever page
   is being edited exactly (see shapes above). `notes` stays blank
   unless it's Varun's own words; AI observations go in `altText` (or
   get left out entirely on `concerts`, which has no such field).
   `altText` is not visible UI copy — it's the image's accessible name,
   so write it as a plain description a screen reader would read out,
   not as editorial commentary directed at the page's viewer.
   If any pieces are street art, also feed them into
   `street-art/index.html` per §6. If a draft place is being promoted to
   live, also update `PLACES`, `FEATURED_PHOTOS`, `HEADER_PHOTOS` per §5
   — and treat `STORIES` as its own curated pass, not a `DATA` copy.
7. **Verify before committing:** (a) Node syntax-check the extracted
   `<script>` block, (b) confirm every referenced img/video path exists
   on disk, (c) load the page in a browser and click through — open a
   drawer, confirm images render, exercise any new interactive feature
   in both directions (e.g. the `extras` side-scroll must round-trip,
   not just scroll one way).
8. **Git** — raw source dumps (`assess/`, `check/`, or whatever the new
   folder is named) never get committed, only the processed `img/`
   outputs plus the edited `index.html`(s). Stage explicitly — don't
   blind `git add -A`, since raw folders sit alongside processed output.
   Write a commit message describing what was found/decided, not just
   "update". Ask before pushing unless auto-push is already established
   as fine for the current session.

## 9. Fixing existing captions/tags from a phone — `edit/index.html`

Same hidden-URL pattern as `log/` (§7): unlinked, blocked in
`robots.txt`, shares the same GitHub token (same repo scope, so saving
one also authorizes the other). For quick text-content fixes — a
caption, a street-art artist, a description — not for adding photos or
building new pages.

- **Which page** — the dropdown is derived from the hub's own live
  `PLACES` entries (same anti-staleness reasoning as `log/`'s place
  dropdown), plus `street-art/` added on top since it's a themed
  aggregator with no `PLACES` entry of its own.
- **Browsing** — every entry on the chosen page, thumbnail + a short
  preview (first of title/caption/artist/location that isn't blank),
  with a text search box across those same fields plus id — needed once
  a page has 75+ pieces.
- **Editing** — every text-ish field an entry actually has (caption,
  artist, title, location, notes, altText, note, tags, venue, billing,
  blurb — whatever's present) renders as an editable input, generically,
  not from a per-page-type schema list. Structural/positional/enum-like
  fields never render as editable: `id, img, type, category, citySlug,
  ratio, order, colorFrame, date, slug, extras, curated, video` — a
  free-text edit on any of those could silently break rendering (a
  category not matching its CSS class, a slug not matching a filter
  chip) without the tool having any way to catch it.
- `tags` (an array field) edits as a comma-separated string, split back
  into an array on save. `artist:null` edits as an empty text field;
  clearing it back to empty writes `null` again rather than `''`, since
  the two aren't treated the same elsewhere on the site (see `khajuraho`
  album's own `artist:null` convention, per §8's DATA fields list).
- **Saving** — re-fetches and re-splits the page's `DATA` array fresh at
  save time (same reasoning as `log/`'s reorder mode), rewrites only the
  edited fields on the one target entry (matched by `id`, not by array
  position), and leaves every other entry — and every other field on the
  edited entry — byte-for-byte untouched. Preserves each string's
  original quote character (`'` vs `"`, both used across this codebase)
  rather than normalizing it, so an edit's diff is just the edit.
- Not covered yet (basics-first): `STORIES` slides (still edited only
  by re-adding through `log/`, not fixed in place), bulk/multi-entry
  edits, and new fields beyond what an entry already has.
