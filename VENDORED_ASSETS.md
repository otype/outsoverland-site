# Vendored third-party assets

These fonts are self-hosted under `static/fonts/` instead of being loaded
live from Google Fonts, so the site makes no third-party network requests
on page load (visitor IPs were otherwise sent to Google on every pageview —
see GDPR/Google Fonts case law, e.g. LG München I, 3 O 17493/20).

No update automation is in place. Fonts essentially never need updating;
re-check only if you want a newer variable-font axis range or subset.

| Asset | Subset | File | Fetched from | Fetched on | Where to check |
|---|---|---|---|---|---|
| Barlow Condensed 400 | latin | `static/fonts/barlow-condensed-400-latin.woff2` | Google Fonts (gstatic v13) | 2026-10-05 | https://fonts.google.com/specimen/Barlow+Condensed |
| Barlow Condensed 600 | latin | `static/fonts/barlow-condensed-600-latin.woff2` | Google Fonts (gstatic v13) | 2026-10-05 | https://fonts.google.com/specimen/Barlow+Condensed |
| Barlow Condensed 700 | latin | `static/fonts/barlow-condensed-700-latin.woff2` | Google Fonts (gstatic v13) | 2026-10-05 | https://fonts.google.com/specimen/Barlow+Condensed |
| Barlow 400 | latin | `static/fonts/barlow-400-latin.woff2` | Google Fonts (gstatic v13) | 2026-10-05 | https://fonts.google.com/specimen/Barlow |
| IBM Plex Mono 400 | latin | `static/fonts/ibm-plex-mono-400-latin.woff2` | Google Fonts (gstatic v20) | 2026-10-05 | https://fonts.google.com/specimen/IBM+Plex+Mono |

Only the `latin` subset is vendored: it covers English and German (ä ö ü ß, €, typographic quotes and dashes).

## How to re-check for a new version

Fetch
`https://fonts.googleapis.com/css2?family=Barlow+Condensed:wght@400;600;700&family=Barlow:wght@400&family=IBM+Plex+Mono:wght@400&display=swap`
with a modern browser User-Agent, take the `/* latin */` `@font-face` blocks,
compare their `unicode-range` against the `@font-face` rules at the top of
`assets/css/main.css`, and re-download the `url(...)` targets if they changed.
