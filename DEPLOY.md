# Deployment

Static one-page site for **https://saunascout.co.uk** — a "coming soon" page with
App Store / Google Play buttons for the SaunaScout iOS/Android app.

Plain HTML + CSS, no build step. Hosted on **GitHub Pages**, domain registered at
**names.co.uk**.

Follows the app's **"Ember & Pine"** design system: warm cream canvas, deep pine
green, ember-orange accent, generous soft radii. Fonts are **Fraunces** (serif
headings), **Figtree** (body) and **Caveat** (the handwritten flourish), loaded
from Google Fonts. Light and dark ("cabin at night") both supported via
`prefers-color-scheme`. Colour tokens mirror `mobile/src/theme/colors.ts` in the
app repo — keep them in step if the app palette changes.

```
index.html        # the page — "Ember & Pine" look, matching the app
privacy.html      # privacy & cookies (covers the website; app policy still to be added)
terms.html        # website terms of use
404.html          # not-found page
assets/           # app icon in a few sizes, favicon, pages.css (shared text-page styles)
CNAME             # custom domain for GitHub Pages (saunascout.co.uk)
robots.txt, sitemap.xml
.nojekyll         # tell GitHub Pages to serve files as-is
```

## Local preview

```bash
python3 -m http.server 8777
# open http://localhost:8777
```

---

## Adding the store links (do this when the apps are live)

Everything routes through **two variables** near the bottom of `index.html`:

```js
var APP_STORE_URL  = "";   // "https://apps.apple.com/gb/app/idXXXXXXXXXX"
var PLAY_STORE_URL  = "";   // "https://play.google.com/store/apps/details?id=uk.co.saunascout"
```

Paste the real URLs, commit, push. Every badge on the page turns into a working
link and the label switches from "Coming soon" to "Download on the / Get it on".
Leave a value blank to keep that badge in its coming-soon state (e.g. ship iOS
first, add Android later).

Optional: swap the placeholder button art for the official
[Apple](https://developer.apple.com/app-store/marketing/guidelines/) and
[Google Play](https://play.google.com/intl/en_us/badges/) badges once you have
store URLs — their guidelines require the official artwork.

---

## One-time deployment

### 1. Push this repo to GitHub

Already done if you're reading this on github.com. Otherwise:

```bash
git remote add origin https://github.com/WuXu1/saunascout-web.git
git push -u origin main
```

### 2. Turn on GitHub Pages

Repo → **Settings → Pages**
- **Source:** Deploy from a branch
- **Branch:** `main` / `/ (root)` → **Save**
- **Custom domain:** enter `saunascout.co.uk` → **Save**
  (this matches the `CNAME` file already in the repo)
- Leave **Enforce HTTPS** unchecked for now — you can tick it once the
  certificate is issued (see step 4).

### 3. Point the domain at GitHub (names.co.uk)

Log in to names.co.uk → your domain → **Manage DNS** / **Advanced DNS**.

**Delete** any existing `A` / `AAAA` / `CNAME` records on the root (`@`) and on
`www` first — names.co.uk usually ships a parking-page record that will conflict.

Then add:

| Type  | Host / Name | Value / Points to        | TTL  |
|-------|-------------|--------------------------|------|
| A     | `@`         | `185.199.108.153`        | 3600 |
| A     | `@`         | `185.199.109.153`        | 3600 |
| A     | `@`         | `185.199.110.153`        | 3600 |
| A     | `@`         | `185.199.111.153`        | 3600 |
| AAAA  | `@`         | `2606:50c0:8000::153`    | 3600 |
| AAAA  | `@`         | `2606:50c0:8001::153`    | 3600 |
| AAAA  | `@`         | `2606:50c0:8002::153`    | 3600 |
| AAAA  | `@`         | `2606:50c0:8003::153`    | 3600 |
| CNAME | `www`       | `wuxu1.github.io`        | 3600 |

Notes:
- The `AAAA` rows are optional (IPv6). If names.co.uk won't let you add them,
  the four `A` records are enough.
- Some panels want a trailing dot on the CNAME value: `wuxu1.github.io.`
- Do **not** set web forwarding / URL redirect on the domain — it clashes with
  the DNS records above.

### 4. Wait, then lock in HTTPS

- DNS usually propagates within 15–60 min (can be up to 24 h).
- Back in **Settings → Pages**, GitHub shows a green "DNS check successful".
- GitHub then provisions a Let's Encrypt certificate (another few minutes).
- Once available, tick **Enforce HTTPS**.

### 5. Verify

```bash
dig +short saunascout.co.uk          # → the four 185.199.x.153 addresses
dig +short www.saunascout.co.uk      # → wuxu1.github.io + those addresses
curl -sI https://saunascout.co.uk    # → HTTP/2 200
```

Check in a browser:
- `https://saunascout.co.uk` loads the page
- `http://` redirects to `https://`
- `www.saunascout.co.uk` redirects to the apex

---

## Updating the site

Edit files, commit to `main`, push. GitHub Pages redeploys in ~1 minute.

## Before app-store submission checklist

- [ ] Add the **app** privacy policy (the current `privacy.html` covers only the
      website). Both stores require a working privacy-policy URL, and Google Play
      needs a Data Safety declaration.
- [ ] Set `APP_STORE_URL` / `PLAY_STORE_URL` in `index.html`
- [ ] Set up the `hello@saunascout.co.uk` mailbox (or change the address in
      `index.html`, `privacy.html`, `terms.html` to one that works)
- [ ] Replace placeholder store buttons with official badge artwork
- [ ] Add real app screenshots to the page (optional but recommended)
- [ ] If website analytics are added later, use a cookieless tool (e.g. Plausible)
      or add a consent banner, and update the Cookies section of `privacy.html`
