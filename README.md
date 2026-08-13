# TrackFolio

A single-page, self-contained book tracker: Library (To Read / Reading / Finished), Search, Stats, and Settings — dark/purple themed, no backend required.

## Tech

Everything lives in `index.html` — plain HTML/CSS/JS, with [Chart.js](https://www.chartjs.org/) bundled directly into the page (no CDN dependency). There is no build step and no server-side code. A small `sw.js` service worker caches the app shell so the hosted (Netlify) version can still open with no internet connection, once you've visited it at least once.

**Data storage:** all books, settings, and the theme preference are saved in the browser's `localStorage`, on the device you're using. Nothing is synced between devices, and clearing your browser data will erase your library — use **Settings → Download Full Backup** regularly, or rely on the built-in 30-day auto CSV backup reminder.

**External APIs used (all free, no keys required):**
- [Open Library Search API](https://openlibrary.org/dev/docs/api/search) — book search and cover suggestions
- [Open Library Covers API](https://openlibrary.org/dev/docs/api/covers) — cover images
- Google Images (opens in a new tab as a manual search shortcut; no API key or integration)

## Offline behaviour

- **Book data, statuses, dates, and settings** always work offline — everything is read from and written to `localStorage` on the device, with no network round-trip.
- **The app shell itself** (this page, including the bundled chart library) is cached by `sw.js` after your first visit, so re-opening the Netlify URL works even with no connection.
- **Search, and fetching new covers**, need a connection since they call the Open Library API — these will simply show no results/an error until you're back online. Covers already saved to a book are unaffected.
- Opening `index.html` directly as a local file works fully offline for everything except search/new-cover-lookups; the service worker itself only registers when served over `https://` or `localhost`, since browsers block it for `file://`.

## Running locally

No install or build needed. Either:

- Double-click `index.html` to open it directly in a browser, or
- Serve it locally so `fetch()` calls behave exactly as they will in production:
  ```bash
  npx serve .
  # or
  python3 -m http.server 8080
  ```

## Deploying with Netlify (via GitHub)

1. Push this repo to GitHub (see below).
2. In [Netlify](https://app.netlify.com), click **Add new site → Import an existing project**.
3. Choose **GitHub** and select this repository.
4. Build settings: leave **Build command** blank and set **Publish directory** to `.` (this is already configured in `netlify.toml`, so Netlify should detect it automatically).
5. Click **Deploy site**. Netlify will give you a live URL (e.g. `your-app.netlify.app`), which you can rename or point a custom domain at under **Site settings → Domain management**.

Every push to your default branch will automatically redeploy the site.

## Pushing this repo to GitHub

From this project folder:

```bash
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin https://github.com/<your-username>/<your-repo-name>.git
git push -u origin main
```

(Create the empty repository on GitHub first via **New repository** — don't initialize it with a README there, to avoid a merge conflict.)

## Notes

- Since data is per-browser/per-device local storage, the hosted Netlify version and any copy you run locally will have **separate** libraries. Use the CSV/JSON backup and restore features in Settings to move your data between them.
- The Google Images button opens a pre-filled search in a new tab (`https://www.google.com/search?tbm=isch&q=...`) as there's no free, keyless API for embedding live Google Image results.
