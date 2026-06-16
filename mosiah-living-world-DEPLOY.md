# Deploy "The Living Lands of Mosiah" to GitHub Pages

A task brief for **Claude Code**. Goal: take one finished, self‑contained HTML file and publish it as a live website on GitHub Pages.

---

## 0. What this is (read first)

You are **deploying an already‑built app, not writing one.** The application is a single, self‑contained HTML file named `mosiah-living-world.html`:

- Pure browser **Canvas2D + vanilla JavaScript**, all CSS and JS inline. ~143 KB, one file.
- **No build step, no bundler, no npm install, no framework.**
- **No external dependencies** except a Google Fonts `<link>` (loads over HTTPS; degrades to system fonts if blocked).
- **No absolute paths and no browser storage**, so it runs correctly from a project subpath (e.g. `user.github.io/repo/`) with no `<base>` tag and no rewrites.

> **Hard constraint:** Do **not** refactor, split, "modernize", or otherwise edit the game code inside `mosiah-living-world.html`. Treat it as a frozen build artifact. You may only (a) copy it to `index.html`, (b) create the wrapper repo files below, and (c) — *only if explicitly desired* — add optional `<head>` meta tags per §7. If `mosiah-living-world.html` is not present in the working directory, stop and say so rather than trying to recreate it.

**Deployment method:** "Deploy from a branch" (`main`, root). This is the simplest correct choice for a single static file, and GitHub automatically re‑publishes on every push to `main`, so future updates need nothing but a `git push`. (A GitHub Actions alternative is in §6 if you ever add a build step — you won't need it for this.)

---

## 1. Prerequisites

- `git` and the **GitHub CLI (`gh`)** installed.
- `gh` authenticated with an account that can create repos and manage Pages:
  ```bash
  gh auth status || gh auth login
  ```
  (Or export a token with `repo` scope as `GH_TOKEN`.)
- The file `mosiah-living-world.html` placed in an otherwise‑empty working directory. Run from that directory.

---

## 2. Target repository layout

```
mosiah-living-world/
├── index.html        # exact copy of mosiah-living-world.html  ← what Pages serves
├── 404.html          # optional, bounces stray paths back to the app
├── .nojekyll         # empty; tells Pages to skip Jekyll processing
└── README.md
```

---

## 3. Build the repo and publish (copy‑paste)

Pick a repo name. Default below makes a **project site** at `https://<user>.github.io/mosiah-living-world/`. (To instead serve at the account root `https://<user>.github.io/`, name the repo exactly `<user>.github.io` — see §5.)

```bash
set -e
REPO="mosiah-living-world"

# 0) sanity: the build artifact must exist
test -f mosiah-living-world.html || { echo "ERROR: mosiah-living-world.html not found in $(pwd)"; exit 1; }

# 1) the file Pages actually serves at the URL root
cp mosiah-living-world.html index.html

# 2) wrapper files
: > .nojekyll

cat > 404.html <<'HTML'
<!DOCTYPE html><html lang="en"><head><meta charset="utf-8">
<meta http-equiv="refresh" content="0; url=./"><title>Redirecting…</title></head>
<body>Page not found — <a href="./">return to The Living Lands of Mosiah</a>.</body></html>
HTML

cat > README.md <<'MD'
# The Living Lands of Mosiah

An interactive, illustrated study companion for the **Book of Mosiah** (Book of Mormon).
Move through twenty scenes — from Zeniff's colony and King Noah's tower to Abinadi,
the waters of Mormon, the bondage and deliverance of Limhi's and Alma's peoples, and
the gathering in Zarahemla — as an isometric diorama or a walkable first/third‑person world.

**▶ Live site:** https://REPLACE_OWNER.github.io/mosiah-living-world/

## Features
- Twenty cinematic scenes with period‑appropriate mood, light, and crowds.
- A walk mode: explore each scene on foot (WASD / arrows / on‑screen joystick), look around by dragging.
- Talk to anyone — heroes speak scene‑specific, scripture‑grounded lines; townsfolk react to the moment; (and yes, you can greet the sheep).
- Trade with merchants in Lehi‑Nephi and Zarahemla using Nephite *senum* coins.
- Minimap, landmark notes, and a scene timeline/scrubber.

## Tech
Single self‑contained `index.html`. Browser Canvas2D + vanilla JavaScript, no build step
and no dependencies (other than Google Fonts over HTTPS). Just open it, or host it anywhere static.

## Local preview
Open `index.html` in any modern browser, or:
```
python3 -m http.server 8000   # then visit http://localhost:8000
```
MD

# 3) put the real owner into the README link
OWNER=$(gh api user --jq .login)
sed -i.bak "s/REPLACE_OWNER/$OWNER/g" README.md && rm -f README.md.bak

# 4) git history
git init -b main
git add -A
git commit -m "The Living Lands of Mosiah — initial publish"

# 5) create the GitHub repo, set origin, and push main
gh repo create "$REPO" --public --source=. --remote=origin --push

# 6) enable Pages from main/root (idempotent: create, else update)
sleep 3
gh api -X POST "repos/$OWNER/$REPO/pages" --input - <<< '{"source":{"branch":"main","path":"/"}}' \
  || gh api -X PUT "repos/$OWNER/$REPO/pages" --input - <<< '{"source":{"branch":"main","path":"/"}}'

echo "Requested Pages build for https://$OWNER.github.io/$REPO/"
```

> If step 6 returns **403/404** (token can't manage Pages), do it in the browser instead:
> **Repo → Settings → Pages → Build and deployment → Source: _Deploy from a branch_ → Branch: `main` / `/(root)` → Save.**

---

## 4. Verify it's live

First publish usually takes ~30–90 seconds. Poll until it answers `200`, then confirm the page is the app:

```bash
OWNER=$(gh api user --jq .login)
REPO="mosiah-living-world"
URL="https://$OWNER.github.io/$REPO/"

for i in $(seq 1 30); do
  code=$(curl -s -o /dev/null -w "%{http_code}" "$URL")
  echo "[$i] $URL -> $code"
  [ "$code" = "200" ] && break
  sleep 10
done

curl -s "$URL" | grep -q "The Living Lands of Mosiah" \
  && echo "✅ LIVE: $URL" \
  || echo "⚠️ Reachable but title not found yet — give it another minute and re-check."
```

Also confirm in a browser that **walk mode** loads (click "Walk here") and that the fonts/canvas render. Print the final URL clearly at the end.

---

## 5. Repo‑name → URL cheat sheet

| Repo name | Resulting URL | Notes |
|---|---|---|
| `mosiah-living-world` (recommended) | `https://<user>.github.io/mosiah-living-world/` | A **project site**. The app's relative paths work as‑is. |
| `<user>.github.io` | `https://<user>.github.io/` | The single **user/root site** per account. Use this only if you want it at the bare domain. |

Future updates: just `git push` to `main` — Pages rebuilds automatically. No other action needed.

**Optional custom domain:** add a `CNAME` file containing the domain (e.g. `mosiah.example.com`), point a DNS `CNAME`/`ALIAS` at `<user>.github.io`, then enable "Enforce HTTPS" in Settings → Pages once the certificate provisions.

---

## 6. Optional: GitHub Actions instead of branch deploy

You do **not** need this for a single static file (branch deploy already auto‑publishes on push). Use it only if you later add a build step. If you do, the Pages **Source must be set to "GitHub Actions"** (Settings → Pages) *before* the workflow can deploy. Action versions below are current as of mid‑2026; bump to the latest majors if newer ones exist.

```yaml
# .github/workflows/deploy.yml
name: Deploy to GitHub Pages
on:
  push:
    branches: [main]
  workflow_dispatch:
permissions:
  contents: read
  pages: write
  id-token: write
concurrency:
  group: pages
  cancel-in-progress: false
jobs:
  deploy:
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - uses: actions/checkout@v4
      - uses: actions/configure-pages@v5
      - uses: actions/upload-pages-artifact@v3
        with:
          path: '.'        # repo root holds index.html
      - id: deployment
        uses: actions/deploy-pages@v4
```

---

## 7. Optional polish (only if asked — minimal, safe `<head>` edits)

These improve link previews and the browser tab. Insert just before `</head>` in `index.html`. **Do not touch anything else in the file.** (Replace the OG `url`/`image` with your real domain/image if you add a screenshot.)

```html
<meta name="description" content="An interactive, illustrated study companion for the Book of Mosiah — explore twenty scenes as a diorama or a walkable first/third-person world.">
<meta name="theme-color" content="#0c0e15">
<link rel="icon" href="data:image/svg+xml,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 16 16'><text y='14' font-size='14'>📜</text></svg>">
<meta property="og:title" content="The Living Lands of Mosiah">
<meta property="og:description" content="Walk the lands of the Book of Mosiah — Nephi, Mormon, Helam, Zarahemla.">
<meta property="og:type" content="website">
```

---

## 8. Troubleshooting

- **404 at the URL after deploy:** confirm `index.html` (not just `mosiah-living-world.html`) is at the repo root and on `main`; confirm Settings → Pages shows Source = branch `main` / root; wait a minute and recheck.
- **Page loads but unstyled fonts:** that's just Google Fonts being blocked on the viewer's network — the app falls back to system fonts and remains fully functional. Nothing to fix.
- **Old content after an update:** hard‑refresh (Ctrl/Cmd‑Shift‑R); Pages/CDN can cache for a short while after a push.
- **`gh api … pages` 403:** the auth token lacks Pages admin — enable Pages via the Settings UI (§3 note) instead.

---

### Done = print the live URL
End by printing the final, verified `https://<user>.github.io/<repo>/` link.
