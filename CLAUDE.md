# ConsoliDated

A private two-person date-journal web app: shared date-idea list, journal of logged dates (per-partner reflections + photos), calendar/album history, Leaflet maps showing both home bases and multi-stop day routes.

## Architecture (deliberate constraints — keep them)

- **Single file.** The entire app is `index.html` — inline CSS + vanilla JS, no build step, no framework. Keep it that way unless the user asks otherwise.
- **Hosting:** GitHub Pages from this repo's `main` root. Deploy = commit + push; live ~1 min later.
- **Backend:** Firebase Firestore only, **Spark (free) plan — a hard project constraint**. No Cloud Storage (requires Blaze/credit card), no phone auth (same reason), no paid anything.
- **Video clips:** a picked video never reaches Firestore. It is decoded locally, the chosen 1 second is redrawn through a canvas and re-encoded with MediaRecorder (`CLIP_MS/CLIP_FPS/CLIP_DIM` = 1s/12fps/1080p cap), and only that silent loop is stored — as a normal `photos` row with `kind:'clip'`, so ordering, covers, arrange mode and trash all work unchanged. Some encoders accept a format then emit **zero bytes** (Chromium's mp4 encoder does this at 1080p under load), which would save a broken clip, so every recording is validated against `MIN_CLIP` and `compressClip` steps down through format→resolution→bitrate, memoising the combo that worked in `clipEncoder`.
- **Photos** live *in Firestore* as base64 JPEG data URLs: client-side compression to ≤1600px / <~930K chars (Firestore 1MiB doc limit). Collections: `photos` (thumbnail + meta) and `photoFulls` (full image), split so list views only load thumbs.
- **Maps:** Leaflet + OpenStreetMap tiles — all keyless/free. Place search: Photon (photon.komoot.io, home-biased) with Nominatim as fallback; reverse geocoding: Nominatim. Never introduce Google Maps (billing).
- **Auth:** Firebase email/password. Two fixed accounts: "Mr. Soccerboy" (partner A, rose) and "Ms. Chairgirl" (partner B, teal). The `PARTNERS` constant in `index.html` holds **SHA-256 hashes** of the two sign-in emails, never the plaintext — identity is derived by hashing the signed-in email (`applyIdentity()`) — and there is intentionally no in-app name/identity editing. **PII policy: the real emails, real names, and personal addresses must never appear in this repo (it's public); they live only in the Firebase console.** If the emails ever change, recompute the hashes.
- **Firestore rules** allowlist exactly the two real emails (see README for the shape, with placeholders). Firebase config in `index.html` is public-safe by design; the rules are the security boundary.

## Data model (Firestore)

- `ideas`: {title, category, desc, loc{name,lat,lng}|null, notes{A,B}, created}
- `dates`: {title, day 'YYYY-MM-DD', time 'HH:MM'|'', ideaId|null, refl{A,B}, locs[{name,lat,lng}...], created, coverId|null (photo doc id shown on the calendar; null = first photo), trash:{by:'A'|'B',at}|null (soft-deleted; hidden everywhere but the Journal's Trash section — only the *other* partner can delete forever, either can restore)}
- `photos`: {dateId, thumb(dataURL), fullId, created, order?, kind?:'clip'} — `kind:'clip'` means `photoFulls.data` holds a silent looping video instead of a JPEG (thumb is its poster frame) — display order is (order ?? created) ascending; `order` is written 0..n to every photo of a date when the couple rearranges them · `photoFulls`: {data(dataURL)}
- `meta/settings`: {homes:[{label,lat,lng,addr|null}|null ×2]} — created lazily via merge writes; no other settings remain. `addr` is the exact address chosen from search when the home was set (shown in the home picker); when null, the picker falls back to reverse geocoding, which is only approximate.

**Data safety:** all user data lives in Firestore, none in this repo. Never write migrations that rewrite existing docs without explicit user sign-off, and remind the user to use Settings → Download backup before schema-touching changes. There is currently no restore-from-backup feature (known gap).

## Code conventions / gotchas

- Rendering is `innerHTML` templates + global `onclick` handlers; `esc()` everything user-authored.
- `render()` skips same-screen re-renders while an input inside `#app` is focused (protects typing from snapshot refreshes) but **must never block screen transitions** — that caused a real login-stall bug on iOS (keychain autofill leaves focus in the password field). The `lastScreen` mechanism handles this; don't regress it.
- `[hidden]{display:none!important}` exists because component CSS uses `display:flex` — removing it re-exposes the FAB/tabbar on the login screen.
- Emoji/theming: the user wants **minimal love/heart theming**. The calendar mark on hero screens is a custom `.calico` element showing **JUL 19** (a date meaningful to the user — the 📆 emoji hardcodes July 17, don't swap back). Home-screen icon is `apple-touch-icon.png`, a single date fruit (the pun).
- Journal album is **paged by month**, sharing `S.cal` with the calendar view (so switching views keeps your place). Album arrows (`albumNav`) skip to the next month that *has* memories; the calendar's own arrows (`calNav`) step month by month (empty months in between are worth seeing) but both are clamped: the album to months that have memories, the calendar (`calBounds`) to the oldest memory month through the later of the newest memory month and the current month, so today stays reachable. `initCalMonth()` runs once on first data load so the album doesn't open blank. Trash is listed outside the month filter.
- `SEED` array = one-tap import of the couple's original idea list; shown only while the `ideas` collection is empty.

## Testing

No test framework. Verify changes headlessly with Playwright: load `index.html` with CDN scripts stubbed from npm packages (`leaflet`, `firebase` UMD builds), inject state into the global `S` object, assert on DOM, screenshot at 390×844. Firebase network calls can't run in tests — inject state instead of signing in.

## Free-tier numbers (for advice consistency)

Firestore Spark: 1 GiB storage, 50K reads / 20K writes per day. ~3,000 photos of headroom at current compression; a 1s clip measures ~100 KB stored (bounded at ~500 KB by the 3 Mbps target), so clips are cheaper than photos. GitHub Pages requires the repo stay public on the free plan.
