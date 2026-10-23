<p align="center">
  <picture><source media="(prefers-color-scheme: dark)" srcset="https://global.media.stux.games/logo-light.png"><source media="(prefers-color-scheme: light)" srcset="https://global.media.stux.games/logo-dark.png"><img src="https://global.media.stux.games/logo-dark.png" height="80" alt="Stux.Games Logo"></picture>
</p>

# Contributing to Coming Soon Page

This document is for anyone working on the soonpage.stux.games page itself.

## Local setup

There's no build step or dependencies. Run `./dev-server.sh [port] [--no-dev-mode]` (or
`dev-server.bat` on Windows) and open `http://127.0.0.1:8000`. DEV_MODE is on by default: the
dev router answers `/assets/dev-mode.js` with `window.SITE_DEV_MODE = true`, so every page shows
the dev banner. `--no-dev-mode` renders exactly as production does. The server needs PHP 7.4
(`$PHP_BIN`, `php74`, or `%LOCALAPPDATA%\Programs\PHP\7.4\php.exe`).

## Project conventions

- Static HTML pages (`index.html`, `legal.html` + `legal/`, `changelog.html`, `404.html`, `sitemap/`): no framework, no build step, no backend.
- Each page's styles are inline in its `<style>` block, built on the same tokens: `--accent` is `#FFBA1A` on dark and `#855D00` on light. Don't hard-code colours outside the theme blocks.
- The Stux.Games twist on this page is the loading screen (segmented bar, PRESS START, loading tip). Keep it playful but readable, and keep every animation behind the `prefers-reduced-motion` rule.
- Oxanium is self-hosted under `assets/fonts/` (weights 300 to 600), not pulled from a third-party CDN.
- The sitemap (`sitemap.xml`, `sitemap/index.html`, `robots.txt`) is generated: after adding or removing a page, edit `PAGES` in `scripts/build-sitemap.py` and run `python scripts/build-sitemap.py`, then commit the result.
- `changelog.html` fetches and renders `CHANGELOG.md` at runtime, sorting sections into Added, Changed, Fixed, Removed, Security, Deprecated. Don't hand-copy changelog content into it.
- General contact uses `hello@stux.games`; legal contact uses `legal@stux.games`.

## Discord previews and icons

- Every page except `404.html` has a Discord link preview: a pre-built `<script id="discord:component-embed">` block in its `<head>`, written by `python scripts/build-discord-embeds.py` from the page's `<title>` and `<meta name="description">` (the coming-soon page's text and buttons are set at the top of the script; `build-sitemap.py` runs it too). Re-run it after changing a page's title or description or adding a page; `--check` only reports stale pages
- The icon set is `favicon.ico`, `apple-touch-icon.png`, `site.webmanifest` and `assets/img/icon-*.png`. Every page's `<head>` carries the same four tags for them (`favicon.ico`, `icon-512.png`, `apple-touch-icon.png`, `site.webmanifest`); copy them as-is into any new page
- `bash tests/site.sh` (Python 3.8+, no dependencies) checks every preview and the icon set; CI runs it with `tests/version.sh`

## Versioning

This project uses [Date Versioning](https://datevers.ing). To release:

1. Add a `## vYY.MM.DD` entry at the top of `CHANGELOG.md`, with its sections in this order: Added, Changed, Fixed, Removed, Security, Deprecated.
2. Set `VERSION.md` to the same bare version (no `v`): today's **UTC** date as `YY.MM.DD`, or `YY.MM.DD-1`, `-2`… for later releases the same UTC day. Never a future date. Use UTC, not local time: just after midnight in the UK it is still the previous day in UTC.
3. Run `bash tests/version.sh` and `bash tests/site.sh`.
4. Run `./commit.sh` (or `commit.bat` on Windows). It checks the version, commits and tags `vYY.MM.DD`, and never pushes.

## Before committing

- Open the page in both themes, and at phone, tablet and desktop widths.
- CI runs `html-validate` on `index.html` and `markdownlint` on every Markdown file.
