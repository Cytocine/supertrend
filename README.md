# TrendCloud Scanner — PWA

A self-contained, installable PWA: ATR Supertrend & Cloud multi-symbol scanner with live charting (Alpaca API), a position-sizing calculator, and offline app-shell caching via a service worker.

## Files

```
index.html          the app
manifest.json        PWA manifest (name, icons, colors, display mode)
sw.js                 service worker (offline app-shell caching)
icons/                app icons, all required sizes + maskable variants
favicon-16.png
favicon-32.png
.nojekyll             GitHub Pages: serve files as-is, no Jekyll processing
_headers               Cloudflare Pages: correct headers for manifest/sw/icons
```

No build step — it's plain HTML/CSS/JS. Deploy the folder as-is.

## Deploy to GitHub Pages

1. Create a new GitHub repo (or use an existing one) and push these files to it:
   ```bash
   git init
   git add .
   git commit -m "TrendCloud Scanner PWA"
   git branch -M main
   git remote add origin https://github.com/<your-username>/<your-repo>.git
   git push -u origin main
   ```
2. On GitHub: **Settings → Pages → Build and deployment → Source** → select **Deploy from a branch**, branch `main`, folder `/ (root)`. Save.
3. Your app will be live at `https://<your-username>.github.io/<your-repo>/` within a minute or two.
4. Because the manifest uses relative paths (`./`), it works fine whether the repo is served from the domain root or a subpath like `/<your-repo>/`.

**Note:** `.nojekyll` is included so GitHub Pages doesn't run its default Jekyll build (which can otherwise mangle files/folders starting with `_` or `.`).

## Deploy to Cloudflare Pages

**Option A — connect the GitHub repo (recommended, auto-deploys on push):**
1. Push the folder to a GitHub repo (see steps above).
2. In the Cloudflare dashboard: **Workers & Pages → Create → Pages → Connect to Git**, pick the repo.
3. Build settings: **Framework preset** = None, **Build command** = (leave empty), **Build output directory** = `/` (root).
4. Deploy. You'll get a `https://<project>.pages.dev` URL, plus you can attach a custom domain in the project's **Custom domains** tab.

**Option B — direct upload with Wrangler (no GitHub needed):**
```bash
npm install -g wrangler
wrangler login
wrangler pages deploy . --project-name=trendcloud-scanner
```

The included `_headers` file tells Cloudflare Pages to serve `manifest.json` with the correct MIME type, keep `sw.js` and `manifest.json` from being aggressively cached (so updates roll out promptly), and cache icons long-term.

## Installing the app

Once deployed and opened in a mobile or desktop browser (Chrome, Edge, or Safari on iOS via Share → Add to Home Screen), the app is installable as a standalone PWA — it'll get its own icon and launch full-screen, no browser chrome.

## Updating after changes

Service workers cache aggressively by design. After you push a change:
1. Bump `CACHE_NAME` in `sw.js` (e.g. `tc-scanner-v1` → `tc-scanner-v2`) so old caches are invalidated.
2. Redeploy. Users who already installed the app will pick up the update automatically the next time they open it (usually within one reload).

## API keys

Alpaca API key/secret are entered in-app (Settings, ⚙) and stored in the browser's local storage on the user's device only — they are never sent anywhere except directly to Alpaca's API from the browser, and are not part of these deployed files.
