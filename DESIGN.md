# ConsoliDated — design brief

Hello, and thank you for helping.

This is a private journal app for two people — a shared list of date ideas, and a record of
dates that actually happened, with each partner's own photos and written reflections. It is
used by exactly two people, on two iPhones, and nobody else will ever see it. It does not
need to look like a product. It should feel like something that belongs to them.

You do not need to know how it's built. What follows is what to design against, and how to
hand the result back so it gets implemented exactly as you drew it rather than approximately.

**Everything here is open to you.** In rough order of how much of the app each one touches:

| | |
|---|---|
| Colour palette | every screen |
| Type — families, weights, sizes | every screen |
| Corner radii | every screen |
| Spacing and density | every screen |
| Screen layouts | per screen |
| Map pins, route lines, popups | the Map tab and every detail sheet |
| Motion — sheets, fades, the spinner | throughout |
| App icon, favicon, browser theme colour | home screen and browser chrome |
| The "JUL 19" calendar mark | the login screen |
| Icons — currently emoji | tab bar, section headings |
| Wording — placeholders, empty states, button labels | throughout |

The only thing genuinely out of scope is how data is stored and synced, which has no visual
surface. If something isn't listed above and you want to change it, ask — the list is
descriptive, not a fence.

---

## How to send the design back

Ranked by how literally it can be implemented. Mixing these is fine — tokens for colour,
screenshots for layout, a sentence for flow.

**1. Fill in the tables below.** Colour, type, shape and spacing are all named variables in
one place in the code. Whatever you put in the "New" column gets pasted in, unchanged, with
no interpretation. This is by far the highest-fidelity route and it covers most of a
restyle.

**2. Change it live in the browser.** Open the app on a laptop, open developer tools, and
edit until it looks right. Send the CSS you changed. Nothing is lost in translation because
there is no translation. You don't need to write code for this — devtools is a colour picker
and a set of number fields.

**3. Annotated screenshots for layout.** A screenshot with numbers on it — "24 gap here, 16
card padding, title 20" — is directly implementable. A beautiful frame with no numbers is
where guessing starts, and guessing is what we're trying to avoid.

**4. Plain sentences for flow.** "Tapping a photo should open it full screen with the date
shown at the top" needs no tooling at all.

Figma is a perfectly good place to *work*. Just be aware the `.fig` file itself can't be
read — what arrives is whatever you export out of it. So export PNGs at 390×844, and read
the numbers off Figma's inspector into the tables below.

---

## Tokens to fill in

### Colour

| Token | Now | Used for | New |
|---|---|---|---|
| `--bg` | `#faf5ef` | app background | |
| `--card` | `#fffdf9` | cards and sheet surfaces | |
| `--ink` | `#3d2c33` | primary text | |
| `--muted` | `#9a8790` | secondary text, placeholders | |
| `--rose` | `#c9536f` | primary accent, buttons — **and partner A** | |
| `--rose-dark` | `#a83a55` | pressed states, link text | |
| `--rose-soft` | `#f7dfe5` | tinted chip / highlight backgrounds | |
| `--teal` | `#3e8e8c` | **partner B** | |
| `--teal-soft` | `#dff0ee` | partner B tint | |
| `--plum` | `#5b3a4a` | small bold labels, toast background | |
| `--line` | `#eadfd6` | borders and dividers | |
| `--gold` | `#c9a227` | "cover photo" star | |
| `--mem` | `#1f5fd0` | memory pins on the map | |
| `--shadow` | `0 2px 10px rgba(91,58,74,.08)` | card elevation | |

Two constraints worth knowing. **Rose and teal identify the two partners** throughout —
their names, their note boxes, their home markers on the map — so they need to stay clearly
distinct from each other and readable as small dots. And `--mem` (blue) marks memory pins
against rose idea pins on the map; those two must be tellable apart at pin size, which an
earlier palette failed at.

### Type

Two families, loaded from Google Fonts.

| Token | Now | Used for | New |
|---|---|---|---|
| `--font-display` | Fraunces | headings, titles, the app name | |
| `--font-body` | Nunito Sans | everything else | |
| `--w-normal` | 400 | body text | |
| `--w-bold` | 700 | labels, buttons | |
| `--w-heavy` | 800 | small caps-ish labels, emphasis | |

| Token | Now | Roughly where | New |
|---|---|---|---|
| `--fs-2xs` | 10px | tab bar labels | |
| `--fs-xs` | 11px | badges | |
| `--fs-sm` | 12px | hints, chips, filter buttons | |
| `--fs-ms` | 13px | form labels, small buttons | |
| `--fs-md` | 14px | body text, section titles | |
| `--fs-lg` | 15px | reflections and notes | |
| `--fs-xl` | 16px | inputs (16px minimum — see constraints) | |
| `--fs-title-sm` | 17px | idea card titles | |
| `--fs-title` | 18px | memory card titles | |
| `--fs-title-lg` | 19px | sheet headers | |
| `--fs-screen` | 28px | screen titles ("Date ideas") | |

Any new font must be available on **Google Fonts** — the project has a hard no-paid-services
rule, so commercial foundries are out.

A handful of sizes sit outside the scale because they're one-offs on single ornamental
elements. Call them out by name if you want them changed: the empty-state emoji (44px), the
login hero emoji (56px) and its title (30px), the `+` button (30px), the calendar's month
arrows (22px), the tab bar icons (21px), the "add photo" plus (24px), the photo viewer's
close ✕ (26px), and the date-block digits on the login screen (29px).

### Shape

| Token | Now | Used for | New |
|---|---|---|---|
| `--r-xs` | 6px | checkboxes | |
| `--r-sm` | 10px | small buttons, search results | |
| `--r-md` | 12px | inputs, toggles | |
| `--r-lg` | 14px | primary buttons, photo cells | |
| `--r-xl` | 16px | cards | |
| `--r-sheet` | 20px | the top corners of bottom sheets | |
| `--r-pill` | 99px | chips and pills | |

### Rhythm

| Token | Now | Used for | New |
|---|---|---|---|
| `--gutter` | 16px | left/right page margin | |
| `--card-pad` | 14px | padding inside a card | |
| `--card-gap` | 12px | vertical space between cards | |

These three are the knobs for "roomier" or "tighter" overall. Padding *within* individual
components is still per-component; describe those on a screenshot.

### Identity — icon, favicon, theme colour

Very much designable. It was not deliberately withheld from this brief; assume it's yours.

| Thing | Now | Notes |
|---|---|---|
| **Home screen icon** | `apple-touch-icon.png`, 180×180 | A single date fruit — a pun on the app's name. Supply a square at 1024×1024 and it'll be downscaled. No transparency, and don't bake in rounded corners: iOS applies its own mask. |
| **Favicon** | inline SVG — a small calendar page reading JUL 19 | Only shows in a desktop browser tab. |
| **Browser theme colour** | `#faf5ef` | Tints the browser UI around the app. Should normally match the page background. |
| **Splash screen** | none | iOS improvises one from the icon and theme colour. A real one needs a separate image per device size — possible, probably not worth it. Your call. |

### The "JUL 19" calendar mark

A custom element — a small calendar page, 64×64, red header band reading `JUL`, large `19`
below — that appears on the login and welcome screens. It's drawn in CSS, not an image, so
it's fully restylable: proportions, colours, the band, the type.

**The date itself is fixed and meaningful to the owners. Please don't change it to anything
else.** Two earlier attempts to use a stock 📆 emoji were rejected for exactly this reason:
that emoji hardcodes July 17.

### The map

Tiles can't be restyled (see Constraints), but everything drawn on top of them can:

| Marker | Now |
|---|---|
| Home base | White chip with a plum border and a 🏠, showing the place's name, sitting above the point |
| Idea pin | 18px rose teardrop, white outline |
| Memory pin | 17px blue (`--mem`) dot, white outline, slightly heavier shadow |
| Numbered stop | 26px rose circle, white outline, white number — used for the stops of one day, in order |
| Route line | Dashed, `#a83a55`, 3px, 6/8 dash pattern — joins the stops of a multi-stop day |
| Popup | Leaflet's default bubble, lightly restyled |

The thing to protect here: **idea pins and memory pins must be tellable apart at actual pin
size**, on a busy map, at a glance. That's why one is rose and one is blue. An earlier
palette had them too similar and it was a real usability problem.

### Motion

Currently minimal and deliberately quick. All of it is open to change — including adding
more, or removing what's there.

| Moment | Now |
|---|---|
| Sheet opening | slides up, 220ms, ease-out |
| Overlay behind a sheet | fades in, 180ms |
| Toast | fades in and out, 250ms |
| Photo still loading | spinner, 800ms per turn, appears only after a 180ms delay so quick loads don't flash |

One note: **the default tap highlight is switched off** app-wide, so pressed feedback is
whatever is designed rather than the browser's grey flash. Several buttons currently have a
custom pressed state; it'd be good to make that consistent.

### Components worth naming explicitly

Beyond the screens themselves, these each have their own look and are all fair game: cards,
chips and category filter pills, primary/secondary/danger buttons, text inputs and
textareas, the two-per-screen note boxes, the photo grid, the month calendar grid and its
day marks, the tab bar, the floating `+` button, the toast, the trim handles for video
clips, and the checklist checkboxes inside idea descriptions.

### Wording

Placeholder text, empty-state messages and button labels are all editable and currently
written for warmth rather than precision ("What do you remember? How did it feel?"). If
you'd write them differently, say so — it's part of the design.

---

## Screens

Design at **390×844** (iPhone 14/15). Check **430×932** too. Portrait is primary; landscape
must not break, but doesn't need to be beautiful.

Before the tabs, two full-screen states you'll see rarely but which set the first
impression:

- **Login** — the JUL 19 calendar mark, the app name, a tagline, and email/password fields.
  Seen once per phone, then never again.
- **"Who are you?"** — a fallback shown only if the app can't work out which partner is
  signed in. Two big buttons, one per person.

Then four tabs, a bottom tab bar, and a floating `+` button:

1. **Ideas** — search box, category filter chips, list of idea cards. Dated ideas float to a
   "⏳ Coming up" block at the top.
2. **Journal** — toggles between an album feed (paged by month) and a month calendar.
   Also holds a Trash section.
3. **Map** — full-screen Leaflet map with pins for ideas and for places they've been, both
   home bases, and route lines connecting the stops of a multi-stop day.
4. **Settings** — home base for each partner, account, backup, version.

Detail views open as **bottom sheets** over the current screen:

- **Idea sheet** — title, category chip, optional date, description (which may contain a
  tappable checklist), an optional map, and a note box for each partner.
- **Memory sheet** — title, date, photo grid, map of the day's stops, a reflection box for
  each partner.
- **Photo viewer** — full screen, pinch to zoom, prev/next.
- **Forms** — logging a date, adding an idea, picking a location.

### Please design the awkward states too

This is where handoffs usually fall apart. For any screen you touch:

- **Empty** — no ideas yet, no memories yet, a month with nothing in it, a memory with no
  photos, an idea with no location.
- **Long** — a 3-line title, a reflection running to 400 words, 20 photos in one memory,
  a 40-item checklist.
- **Loading** — a photo mid-load, a map before tiles arrive.
- **Error** — a photo that won't load, a search that finds nothing.
- **Two people** — every note area appears twice, once per partner, and the signed-in
  partner's box always comes first.

Designing against "Dinner ✨" and one perfect square photo produces something that breaks the
first time it meets real content.

---

## Constraints

Things that will be expensive or impossible, worth knowing before you draw them.

- **Map tiles can't be restyled.** The map is OpenStreetMap through Leaflet, which is free.
  Custom-styled map tiles mean a paid provider, which this project won't use. Pins, popups
  and route lines *are* fully stylable — the tiles underneath are not.
- **Dark mode does not exist yet.** It's a genuine piece of work needing a second full
  palette, not a switch. Worth deciding up front rather than discovering late.
- **Text inputs must be at least 16px.** Below that, iOS zooms the page when you focus a
  field. This is a hard platform rule, not a preference.
- **The bottom of the screen is partly spoken for** — the tab bar plus the iPhone home
  indicator. There's a safe-area inset reserved; don't put anything critical in the last
  ~34px.
- **No component library.** Every element is hand-written, which cuts both ways: nothing is
  free, but nothing is off-limits either. A custom control costs about the same as a
  conventional one.
- **Photos are real and unpredictable** — any aspect ratio, portrait and landscape mixed in
  one grid, sometimes a short looping video clip instead of a photo.
- **Minimal romantic theming, please.** Deliberate choice by the owners: no hearts, no pink
  cursive, no "couples app" iconography. Warm, but restrained.

## Where the effort probably isn't worth it

Not off-limits — just low return, so spend your time elsewhere unless you disagree.

- **Marketing, onboarding, sign-up flows.** There are two users, both already set up, and
  nobody else will ever install this.
- **Anything about storage, syncing or backup.** No visual surface worth designing.
- **A bespoke splash screen.** Needs a separate image per device size for a screen that
  shows for under a second.

### One thing that isn't settled, if you want it

The icons throughout are **emoji** — 💡 📖 🗺️ ⚙️ in the tab bar, 🏠 and 📍 and ⏳ in
headings. That was a pragmatic choice, not a considered one: emoji are free, need no
drawing, and render everywhere. A proper icon set would look considerably better and is
entirely doable — it just means someone drawing perhaps fifteen icons and them being
carried as inline SVG. If you'd enjoy that, it's probably the single biggest visual
upgrade available. If not, the emoji are fine and nothing else depends on the choice.
