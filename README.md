# ConsoliDated

A private little web app for two: a shared date-idea list, a journal of dates that happened (with each partner's own reflections and photos from the day), a calendar/album view of your history, and maps that always show both home bases — including your route across the city on multi-stop days.

Runs free on **GitHub Pages** (hosting) + **Firebase Firestore** (database, Spark plan — no credit card). Photos are compressed in the browser and stored right in Firestore, so there's no separate photo-storage service to pay for.

## Setup (one time, ~10 minutes)

### 1. Create the Firebase project
1. Go to [console.firebase.google.com](https://console.firebase.google.com) → **Create a project** (any name, e.g. `our-dates`). Google Analytics: off is fine.
2. In the project, open **Build → Firestore Database → Create database**. Pick the region nearest you (e.g. `us-east1`), and choose **production mode**.
3. Go to the **Rules** tab, replace everything with the rules below, and click **Publish**:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      allow read, write: if request.auth != null
        && request.auth.token.email in ['YOUR-EMAIL-HERE', 'HER-EMAIL-HERE'];
    }
  }
}
```

Replace the two placeholders with the exact sign-in emails you create in the next step (lowercase). These rules reject everyone except your two logins — so the repo being public exposes nothing.

### 1½. Create your two logins
1. Firebase console → **Build → Authentication → Get started**.
2. **Sign-in method** tab → enable **Email/Password** (just the first toggle; skip "email link").
3. **Users** tab → **Add user**, twice — one login for each of you. The emails don't need to be real or verified (`him@ourdates.love` / `her@ourdates.love` work fine); just make the passwords strong.
4. Optional belt-and-braces: **Authentication → Settings → User actions** → disable sign-up ("Enable create"), so nobody can register new accounts. The email allowlist in the rules already protects your data either way.

Each of you signs in with your own login, and the app maps the login to your identity — notes and reflections can't be saved under the wrong name. On iPhone, Safari/iCloud Keychain offers to save the password on first sign-in and autofills it with Face ID after that, so no password manager is needed.

### 2. Get your config keys
1. Firebase console → ⚙️ **Project settings** → **Your apps** → click the **`</>`** (Web) icon → register the app (no hosting needed).
2. Copy the `firebaseConfig = { ... }` block it shows you.
3. Open `index.html`, find the **STEP 1** banner near the top of the `<script>` section, and paste your values over the placeholders (`apiKey`, `authDomain`, `projectId` are all it needs).

### 3. Put it on GitHub Pages
1. Create a repo (public or private — Pages works on public repos on the free plan) and upload `index.html`.
2. Repo **Settings → Pages** → Source: *Deploy from a branch* → branch `main`, folder `/ (root)` → Save.
3. Your app will be live at `https://<username>.github.io/<repo>/` in a minute or two.
4. On each of your iPhones, open that URL in Safari → Share → **Add to Home Screen**. It'll feel like a real app.

### 4. First run
The app walks you through it: sign in with your own login (once per device — it stays signed in) → enter both names and both sign-in emails (this ties each login to an identity, so there's no "who am I" picking) → set both apartments in **Settings → Home base** so every map shows them.

## How it works / good to know
- **Ideas** each have a category, an optional location, and a private-ish notes box per partner (you write in yours; you can read each other's).
- **Logging a date** works from an idea ("We did this — log it") or from scratch for past dates. Add every stop of a long day out — with 2+ stops the map draws the route. Photos and reflections are added on the memory's page after saving.
- **Photos** are resized to ~1600px JPEG on your phone before upload, so each is roughly 200–500 KB. The free 1 GB Firestore tier holds ~3,000 photos. They're "screen quality," not print-res originals — keep the originals in your camera roll.
- **Journal** flips between an album feed and a calendar; tap any marked day to relive it.
- **Free-tier math**: Firestore's Spark plan gives 1 GB storage, 50K reads and 20K writes per day. Two people can't realistically dent the daily quotas; storage is the only number to watch, and the app keeps photos small.
- **Backup**: Settings → Download backup grabs everything (including photos) as one JSON file.

## Customizing
It's one file. Colors live in the `:root` CSS variables at the top; categories in the `CATS` array. Ask Claude to change anything.
