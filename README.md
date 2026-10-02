# Task Tracker

A personal kanban board that runs as an installable web app. It is plain HTML/CSS/JS: no build step, no framework, no server of your own.

- **Board:** Backlog / This Week / Today / Waiting / Done, with drag-and-drop ordering, search, category filter, repeating cards, and a Columns/Rows layout toggle
- **Quick-add tags:** `Call plumber #today !high @home due:fri *weekly [Kitchen reno] // notes`
- **Focus mode:** one card at a time from Today, with a 15 / 25 / 50 minute timer
- **Parking lot:** capture "what I was doing / what interrupted me / next step" so you can find your place again
- **Projects:** draggable priority list with % complete, health, deadlines and linked cards
- **Import:** paste a list or an email, or upload a CSV / Excel file
- **Dark mode, offline use, JSON backup and restore**

It works **immediately with no setup** and saves to the browser you open it in. To sync between your phone and computer, add a free Firebase project (below).

---

## 1. Try it locally

Open `index.html` in a browser, or serve the folder:

```
python3 -m http.server 8000
```

then visit http://localhost:8000. (The service worker and install prompt need `http://localhost` or `https://`, not `file://`.)

## 2. Put it on GitHub Pages

1. Create a new **empty repository** on GitHub in your personal account (public is fine: there are no secrets in this code).
2. Upload everything in this folder to it (drag the files onto the repo page, or use git).
3. In the repo go to **Settings → Pages → Build and deployment**, set **Source: Deploy from a branch**, **Branch: main / (root)**, then **Save**.
4. After a minute your app is live at `https://YOUR-USERNAME.github.io/YOUR-REPO/`.
5. On your phone, open that link, then **Share → Add to Home Screen** (iPhone/Safari) or **Install** (Android/Chrome).

When you later change `index.html`, bump `CACHE = 'task-tracker-v1'` to `v2` in `sw.js` so installed copies pick up the update.

## 3. Turn on cloud sync (Firebase, free tier)

Do this in your **own personal Google account**, not a work one.

1. Go to https://console.firebase.google.com → **Add project** (skip Google Analytics).
2. **Build → Authentication → Get started → Sign-in method → Google → Enable** (pick a support email, Save).
3. **Build → Firestore Database → Create database.** Choose a location near you and start in **production mode**.
4. In Firestore open the **Rules** tab, replace the contents with the text of `firestore.rules` from this folder, and **Publish**. This limits each signed-in user to their own data.
5. **Project settings (gear icon) → Your apps → the `</>` web icon**, register an app (no hosting needed). Firebase shows a `firebaseConfig = { ... }` block.
6. Paste those six values into `FIREBASE_CONFIG` at the top of the script in `index.html`.
7. **Authentication → Settings → Authorized domains → Add domain:** `YOUR-USERNAME.github.io`. (`localhost` is already allowed.) Without this, Google sign-in fails on your live site.
8. Commit/upload the edited `index.html`, open the app, tap the **☁ Sign in to sync** chip and sign in with Google.

The first device you sign in on uploads its existing cards. Your other devices then show the same data within seconds, and changes made offline sync when you reconnect.

**About the Firebase config values:** the `apiKey` etc. are identifiers, not passwords. It is normal and safe for them to be in a public repo. What protects your data is the security rules in step 4 plus Google sign-in. Do not skip step 4.

## 4. Customizing

Everything you are likely to change is at the top of the `<script>` in `index.html`:

- `CATEGORIES`: names and colours (hue 0-360, saturation %). `@tag` shortcuts follow the names automatically.
- `DEFAULT_CATEGORY`: what new cards get when you don't pick one.
- The columns are in `LANES`, the repeat options in `REPEATS`, project health states in `HEALTH`.

## Data and backups

- Local data lives in your browser's `localStorage`. Clearing site data removes it, so use **Settings → Download backup** now and then, especially if you don't use cloud sync.
- With sync on, every completed card is also kept in a `log` collection in Firestore, even after you clear it from the board.
- **Settings → Restore** merges a backup file into what's already there.

## Files

| File | Purpose |
| --- | --- |
| `index.html` | The whole app |
| `manifest.webmanifest`, `icon*.png`, `icon.svg`, `apple-touch-icon.png` | Home-screen install |
| `sw.js` | Offline cache for the app shell |
| `firestore.rules` | Security rules to paste into Firebase |
