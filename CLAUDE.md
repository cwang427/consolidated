# ConsoliDated

A private two-person date-journal web app: shared date-idea list, journal of logged dates (per-partner reflections + photos), calendar/album history, Leaflet maps showing both home bases and multi-stop day routes.

## Architecture (deliberate constraints — keep them)

- **Single file.** The entire app is `index.html` — inline CSS + vanilla JS, no build step, no framework. Keep it that way unless the user asks otherwise.
- **Hosting:** GitHub Pages from this repo's `main` root. Deploy = commit + push; live ~1 min later.
- **Backend:** Firebase Firestore only, **Spark (free) plan — a hard project constraint**. No Cloud Storage (requires Blaze/credit card), no phone auth (same reason), no paid anything.
- **Photos** live *in Firestore* as base64 JPEG data URLs: client-side compression to ≤1600px / <~930K chars (Firestore 1MiB doc limit). Collections: `photos` (thumbnail + meta) and `photoFulls` (full image), split so list views only load thumbs.
- **Maps:** Leaflet + OpenStreetMap tiles, Nominatim search — all keyless/free. Never introduce Google Maps (billing).
- **Auth:** Firebase email/password. Two fixed accounts, baked into the `PARTNERS` constant in `index.html`:
  `partner-a@example.com` → "Mr. Soccerboy" (partner A, rose), `partner-b@example.com` → "Ms. Chairgirl" (partner B, teal).
  Identity is derived from the signed-in email — there is intentionally no in-app name/identity editing.
- **Firestore rules** allowlist exactly those two emails (see README). Firebase config in `index.html` is public-safe by design; the rules are the security boundary.

## Data model (Firestore)

- `ideas`: {title, category, desc, loc{name,lat,lng}|null, notes{A,B}, created}
- `dates`: {title, day 'YYYY-MM-DD', time 'HH:MM'|'', ideaId|null, refl{A,B}, locs[{name,lat,lng}...], created}
- `photos`: {dateId, thumb(dataURL), fullId, created} · `photoFulls`: {data(dataURL)}
- `meta/settings`: {homes:[{label,lat,lng}|null ×2]} — created lazily via merge writes; no other settings remain.

**Data safety:** all user data lives in Firestore, none in this repo. Never write migrations that rewrite existing docs without explicit user sign-off, and remind the user to use Settings → Download backup before schema-touching changes. There is currently no restore-from-backup feature (known gap).

## Code conventions / gotchas

- Rendering is `innerHTML` templates + global `onclick` handlers; `esc()` everything user-authored.
- `render()` skips same-screen re-renders while an input inside `#app` is focused (protects typing from snapshot refreshes) but **must never block screen transitions** — that caused a real login-stall bug on iOS (keychain autofill leaves focus in the password field). The `lastScreen` mechanism handles this; don't regress it.
- `[hidden]{display:none!important}` exists because component CSS uses `display:flex` — removing it re-exposes the FAB/tabbar on the login screen.
- Emoji/theming: the user wants **minimal love/heart theming**. The calendar mark on hero screens is a custom `.calico` element showing **JUL 19** (a date meaningful to the user — the 📆 emoji hardcodes July 17, don't swap back). Home-screen icon is `apple-touch-icon.png`, a single date fruit (the pun).
- `SEED` array = one-tap import of the couple's original idea list; shown only while the `ideas` collection is empty.

## Testing

No test framework. Verify changes headlessly with Playwright: load `index.html` with CDN scripts stubbed from npm packages (`leaflet`, `firebase` UMD builds), inject state into the global `S` object, assert on DOM, screenshot at 390×844. Firebase network calls can't run in tests — inject state instead of signing in.

## Free-tier numbers (for advice consistency)

Firestore Spark: 1 GiB storage, 50K reads / 20K writes per day. ~3,000 photos of headroom at current compression. GitHub Pages requires the repo stay public on the free plan.
