# Vendored third-party assets

These fonts are self-hosted under `static/fonts/` instead of being loaded
live from Google Fonts, so the site makes no third-party network requests
on page load (visitor IPs were otherwise sent to Google on every pageview —
see GDPR/Google Fonts case law, e.g. LG München I, 3 O 17493/20).

No update automation is in place. Fonts essentially never need updating;
re-check only if you want a newer variable-font axis range or subset.

| Asset | Subset | File | Fetched from | Fetched on | Where to check |
|---|---|---|---|---|---|
| Archivo (variable, wght 400–900) | latin | `static/fonts/archivo-latin.woff2` | Google Fonts (`fonts.googleapis.com/css2?family=Archivo:wght@400..900`) | not recorded at fetch time | https://fonts.google.com/specimen/Archivo |
| Archivo (variable, wght 400–900) | latin-ext | `static/fonts/archivo-latin-ext.woff2` | Google Fonts (`fonts.googleapis.com/css2?family=Archivo:wght@400..900`) | not recorded at fetch time | https://fonts.google.com/specimen/Archivo |

## How to re-check for a new version

Fetch `https://fonts.googleapis.com/css2?family=Archivo:wght@400..900` with
a modern browser User-Agent, compare the `unicode-range` values in the
returned `@font-face` blocks against `assets/css/main.css` to confirm subset
coverage hasn't changed, and re-download the `url(...)` targets if it has.
