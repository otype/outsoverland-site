<div align="center">

# 🏜️ OUTS Overland

**Over & Under The Sun.** Overlanding trips, trail notes and one Jeep Gladiator.

[![Deploy](https://img.shields.io/github/actions/workflow/status/otype/outsoverland-site/deploy.yml?branch=main&style=flat-square&logo=github&label=deploy)](https://github.com/otype/outsoverland-site/actions/workflows/deploy.yml)
[![Last commit](https://img.shields.io/github/last-commit/otype/outsoverland-site/main?style=flat-square&logo=github&logoColor=white)](https://github.com/otype/outsoverland-site/commits/main)
[![Hugo](https://img.shields.io/badge/built%20with-Hugo-ff4088?style=flat-square&logo=hugo&logoColor=white)](https://gohugo.io)
[![Website](https://img.shields.io/badge/site-outsoverland.com-4a7c59?style=flat-square)](https://outsoverland.com)

[**→ outsoverland.com**](https://outsoverland.com) · [🇩🇪 Deutsch](https://outsoverland.com/de/)

</div>

---

The source of [outsoverland.com](https://outsoverland.com): a small static site in English and German, built with [Hugo](https://gohugo.io) and published to GitHub Pages by GitHub Actions on every push to `main`.

## ✨ Highlights

- 🧱 **No theme, no Node, no runtime dependencies.** The layouts and CSS live in this repo and are the whole design.
- 🌍 **Two languages.** English at the root, German under `/de/`, linked by the language switcher.
- 🔒 **Privacy by default.** Fonts are self-hosted, there are no cookies, no analytics and no third-party requests on page load.
- 🗺️ **Tours as page bundles.** One folder per trip with photos, GPX tracks and short looping clips; the homepage grid picks new tours up automatically.
- 🚀 **Deploys itself.** Push to `main`, GitHub Actions builds with a pinned Hugo version and publishes.

## 🗂️ Project layout

```text
outsoverland-site/
├── hugo.toml                 config: languages, footer menus, social links
├── content/                  page copy: *.md is English, *.de.md is German
│   ├── _index.md             homepage text (front matter)
│   ├── legal-notice.md       legal notice (Impressum)
│   ├── privacy.md            privacy policy
│   └── trips/<folder>/       one page bundle per tour
├── layouts/                  templates: home, page, trips, 404, partials
├── assets/                   the stylesheet (css/main.css) and the hero image
├── i18n/                     UI strings (buttons, labels) per language
├── static/                   fonts, favicon, images, CNAME: copied to the site root
├── .github/workflows/        deploy.yml, the Pages deployment
└── VENDORED_ASSETS.md        where the self-hosted fonts come from
```

## 💻 Running it locally

Hugo is a single binary, no package manager needed.

```console
$ sudo pacman -S hugo       # Arch, or grab a release: https://github.com/gohugoio/hugo/releases
$ hugo server               # http://localhost:1313, live-reloads on save
$ hugo --minify             # one-off production build into ./public
```

Use the **extended** build, like the workflow does. A newer Hugo than the pinned one is fine for local work. The version CI uses is `HUGO_VERSION` in `.github/workflows/deploy.yml`.

## ✏️ Editing content

All homepage copy lives in the front matter of `content/_index.md` (English) and `content/_index.de.md` (German). Nothing on the homepage is hardcoded in the templates: change the text there, not in `layouts/`.

Interface strings (`Soon`, `Open`, the 404 text) are in `i18n/en.toml` and `i18n/de.toml`.

### Turning on a social channel

In `hugo.toml`, find the channel and fill it in:

```toml
[[params.social]]
  name = "YouTube"
  icon = "youtube"
  url = "https://youtube.com/@..."   # <- add
  enabled = true                     # <- flip
```

Until `enabled = true`, the card renders as a dashed "Soon" placeholder. Available `icon` values: `youtube`, `instagram`, `mail`. Instagram and email are on today, YouTube is still a placeholder.

## 🌍 Languages

English is served at the site root, German under `/de/`:

| | English | German |
| --- | --- | --- |
| Home | `/` | `/de/` |
| Legal | `/legal-notice/` | `/de/impressum/` |
| Privacy | `/privacy/` | `/de/datenschutz/` |
| Tour | `/trips/<slug>/` | `/de/touren/<slug>/` |

A page exists in a language only if the file does. `content/foo.md` gives you an English page; add `content/foo.de.md` and the language switcher links the two automatically. Pages without a translation simply don't appear in the other language.

## 🧭 Adding a tour

Each tour is a page bundle under `content/trips/<folder>/`: `index.md` (English), `index.de.md` (German), plus its photos and GPX files. Copy `content/trips/2026-09-pyrenaeen/` as the template. The front matter holds everything the page shows:

| Key | What it is |
| --- | --- |
| `title`, `slug`, `summary` | name, URL part and teaser text |
| `date`, `period` | sort date and the printed date range |
| `cover` | photo used on the homepage grid and as the page header |
| `facts` | the fact table, a list of `k` (label) and `v` (value) |
| `tracks` | GPX files, each with `file`, `name` and `note` |
| `clips` | short looping videos, each with `file`, `poster` and `title` |
| `resources` | every photo with its `src` and a per-language alt text in `title` |

URLs come from the `slug` in each language file (they may differ): `/trips/<slug>/` and `/de/touren/<slug>/`. The homepage grid picks tours up automatically, newest first.

**Shrink photos and strip their metadata** (camera GPS!) before adding them:

```console
$ magick in.jpg -auto-orient -resize '1600x1600>' -strip -quality 82 out.jpg
```

**Clips** autoplay in a loop, so keep them to about 15 s and under 3 MB, without sound or metadata (phone videos carry GPS). They go into `clips/` inside the bundle:

```console
$ ffmpeg -ss 28 -t 14 -i in.mp4 -an -map_metadata -1 -vf "scale=1280:-2,fps=30,format=yuv420p" \
    -c:v libx264 -preset slower -crf 25 -maxrate 1700k -bufsize 3400k -movflags +faststart clip.mp4
$ ffmpeg -ss 1 -i clip.mp4 -frames:v 1 -q:v 4 clip.jpg   # poster
```

This needs an ffmpeg built with libx264.

## 🚀 Deployment

[`deploy.yml`](.github/workflows/deploy.yml) runs on every push to `main` and can also be started by hand from the Actions tab (`workflow_dispatch`):

1. install the pinned extended Hugo (`HUGO_VERSION`, bump it there),
2. build with `hugo --minify --gc` for the Pages base URL,
3. upload the `public/` folder and publish it with `actions/deploy-pages`.

Only one deploy runs at a time; a newer push waits instead of cancelling a running deploy. The repository's Pages source is set to **GitHub Actions** (Settings → Pages → Build and deployment).

## 🌐 Domains

GitHub Pages allows exactly **one** custom domain per repository, so the two domains live in two repositories:

| Domain | Served by | How |
| --- | --- | --- |
| `outsoverland.com` | this repo | `static/CNAME`, DNS below |
| `outsoverland.de` | [`otype/outsoverland-de`](https://github.com/otype/outsoverland-de) | a tiny Pages site that forwards to `outsoverland.com` and keeps the path |

### DNS for outsoverland.com

Apex records at your DNS provider. Verify these against GitHub's current [apex domain docs](https://docs.github.com/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site) before entering them, GitHub has changed the set before:

```text
A     @   185.199.108.153
A     @   185.199.109.153
A     @   185.199.110.153
A     @   185.199.111.153
AAAA  @   2606:50c0:8000::153
AAAA  @   2606:50c0:8001::153
AAAA  @   2606:50c0:8002::153
AAAA  @   2606:50c0:8003::153
CNAME www otype.github.io.
```

Then Settings → Pages → Custom domain → `outsoverland.com`, wait for the DNS check to pass, and tick **Enforce HTTPS**.

> [!NOTE]
> If you later want `outsoverland.de` to serve German content on its own domain instead of forwarding, it needs a host that allows several domains per site. Cloudflare Pages and Netlify both do, both deploy this repo unchanged, and Hugo's `languages.de.baseURL` then does the rest.

## ✅ Open to-dos

- [ ] **Legal notice and privacy text.** `content/legal-notice.md`, `content/legal-notice.de.md` and `content/privacy*.md` still contain `TODO` markers, and they show on the live pages. An Impressum is legally required for a site operated from Germany (§ 5 DDG), in both languages.
- [ ] **Review the privacy text** whenever the site changes: it is accurate for the site as built (no cookies, no analytics, self-hosted fonts).
- [ ] **Link preview image.** Add `static/img/og.png` (1200×630). `hugo.toml` already points at it, and the meta tag appears once the file exists.
- [x] Contact address is set in `hugo.toml` (`params.social`).

## 🧠 Notes on choices

**Fonts are self-hosted** (`static/fonts/`), not loaded from Google's CDN. German courts have found that embedding Google Fonts from Google's servers without consent breaches the GDPR, and the `.de` domain makes that a live risk. Keep it that way. Origins and fetch dates of the font files are in [`VENDORED_ASSETS.md`](VENDORED_ASSETS.md).

**Embeds are the thing to watch.** YouTube and Instagram embeds set third-party cookies and would drag a consent banner and a longer privacy policy into this project. Linking out, as the channel cards do, does not.
