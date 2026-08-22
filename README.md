# OUTS Overland — outsoverland.com / .de

Static site for **OUTS Overland** (*Over & Under The Sun*). Built with
[Hugo](https://gohugo.io), deployed to GitHub Pages by GitHub Actions on every
push to `main`.

No theme submodule, no Node build step, no runtime dependencies — the layouts
and CSS live in this repo and are the whole design.

```
assets/css/main.css   the entire stylesheet
layouts/              templates (home, page, 404, partials)
content/              page copy — *.md is English, *.de.md is German
i18n/                 UI strings (buttons, labels) per language
static/               fonts, favicon, CNAME — copied verbatim to the site root
hugo.toml             config, languages, footer menus, social links
```

## Running it locally

Hugo is a single binary — no package manager needed.

```sh
# macOS
brew install hugo

# or grab a release: https://github.com/gohugoio/hugo/releases
hugo server          # http://localhost:1313, live-reloads on save
hugo --minify        # one-off production build into ./public
```

Use the **extended** build (the workflow does); it's the default on Homebrew.

## Editing content

All homepage copy lives in the front matter of `content/_index.md` (English)
and `content/_index.de.md` (German). Nothing on the homepage is hardcoded in
the templates — change the text there, not in `layouts/`.

Interface strings (`Soon`, `Open`, the 404 text) are in `i18n/en.toml` and
`i18n/de.toml`.

### Turning on a social channel

In `hugo.toml`, find the channel and fill it in:

```toml
[[params.social]]
  name = "YouTube"
  icon = "youtube"
  url = "https://youtube.com/@..."   # <- add
  enabled = true                     # <- flip
```

Until `enabled = true`, the card renders as a dashed "Soon" placeholder.
Available `icon` values: `youtube`, `instagram`, `mail`.

## Languages

English is served at the site root, German under `/de/`:

| | English | German |
|---|---|---|
| Home | `/` | `/de/` |
| Legal | `/legal-notice/` | `/de/impressum/` |
| Privacy | `/privacy/` | `/de/datenschutz/` |

A page exists in a language only if the file does. `content/foo.md` gives you
an English page; add `content/foo.de.md` and the language switcher links the
two automatically. Pages without a translation simply don't appear in the
other language.

## Deployment

A GitHub Actions workflow builds and publishes on every push to `main`. The
Hugo version is pinned in it (`HUGO_VERSION`) — bump it there.

**The workflow is not in place yet.** It sits at `ci/pages-deploy.yml`
because the token used to create this branch lacked GitHub's `workflow`
scope, which is required to write anything under `.github/workflows/`. Move
it with your own credentials:

```sh
mkdir -p .github/workflows
git mv ci/pages-deploy.yml .github/workflows/deploy.yml
rmdir ci
git commit -m "Move Pages deploy workflow into place"
```

(Delete the explanatory comment block at the top of the file while you're
there — it's only a signpost.)

**Then, one-time in the repo settings:** Settings → Pages → Build and
deployment → Source: **GitHub Actions**. Without this the workflow builds
fine but has nothing to publish to.

## Domains

GitHub Pages allows exactly **one** custom domain per repository. This site
uses `outsoverland.com` (set in `static/CNAME`); `outsoverland.de` redirects
to it at the registrar.

### DNS for outsoverland.com

Apex records at your DNS provider (verify these against GitHub's current
[apex domain docs](https://docs.github.com/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site)
before entering them — GitHub has changed the set before):

```
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

Then Settings → Pages → Custom domain → `outsoverland.com`, wait for the DNS
check to pass, and tick **Enforce HTTPS**.

### outsoverland.de

Set up an HTTP 301 redirect to `https://outsoverland.com` at the registrar
(most German registrars call this *Weiterleitung* and include it free).

If you later want the `.de` domain to serve German content on its own domain
rather than redirect, that needs a host that allows multiple domains per site —
Cloudflare Pages or Netlify both do, both deploy this repo unchanged, and
Hugo's `languages.de.baseURL` then does the rest.

## Before launch

- [ ] Fill in `content/legal-notice.de.md` — **Impressum is legally required**
      for a site operated from Germany (§ 5 DDG). Same for the English copy.
- [ ] Review `content/privacy.*.md` — accurate for the site as built today
      (no cookies, no analytics, self-hosted fonts); must be updated if that
      changes.
- [ ] Set a real contact address in `hugo.toml` (`params.social`) and in the
      legal pages.
- [ ] Add `static/img/og.png` (1200×630) for link previews — the meta tag
      appears automatically once the file exists.

## Notes on choices

**Fonts are self-hosted** (`static/fonts/`), not loaded from Google's CDN.
German courts have found that embedding Google Fonts from Google's servers
without consent breaches the GDPR, and the `.de` domain makes that a live
risk. Keep it that way.

**Embeds are the thing to watch.** YouTube and Instagram embeds set
third-party cookies and would drag a consent banner and a longer privacy
policy into this project. Linking out, as the channel cards do, does not.

## Growing this

The layout system is deliberately small. Adding a blog:

```sh
mkdir -p content/trips
hugo new trips/first-trip.md
```

That needs a `layouts/trips/list.html` and `single.html` (or just
`layouts/list.html` / `layouts/single.html` to cover everything). Re-enable
tags and categories by deleting the `disableKinds` line in `hugo.toml`.
