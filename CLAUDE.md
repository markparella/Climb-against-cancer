# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

Static marketing site for **Climb Against Cancer**, a benefit-mountaineering campaign founded by Mark Parella in 2016. The site has no build step, no framework, and no JavaScript — it is plain HTML + CSS deployed to GitHub Pages.

## Local development

```bash
# Preview the site (any of these works):
open index.html                              # quickest
python3 -m http.server 8000                  # if you need http:// (e.g. fonts/CORS)
npx --yes http-server -p 8000                # alternative
```

For visual regression / preview screenshots, the existing pattern uses Playwright via the system Chromium at `/opt/pw-browsers/.../headless_shell` (see prior commits). Output PNGs go under `_preview*/` which is gitignored.

There are **no tests, linters, or build commands**. The deploy is the test.

## Page → nav-label mapping (non-obvious)

The HTML filenames are legacy from an earlier design; the displayed nav labels diverge:

| File | Nav label  |
|------|------------|
| `index.html`   | Home       |
| `about.html`   | About      |
| `stories.html` | **Expeditions** |
| `blog.html`    | **Journal**     |
| `contact.html` | Contact    |

Don't rename the files casually — incoming links and the deploy workflow assume these paths. Update labels in nav, footer, and `<title>` together.

## Architecture

Five hand-maintained HTML pages share one stylesheet (`styles.css`). There is no templating, so the `<nav>` block and the `<footer class="site">` + `.foot-bottom` block are duplicated verbatim across every page. When adding/removing a nav link or changing the footer, update **all five pages**.

Each page follows one of two header patterns:

- **`index.html`** uses `<section class="hero">` — full-bleed photo hero (`assets/hero.jpg`), dark gradient overlay, the `<nav>` sits inside it, content centered.
- **All other pages** use `<header class="page-head">` — quieter dark-navy header with the same `<nav>`, a Fraunces serif title, and a subtle SVG mountain-ridge silhouette at the bottom (the same inline SVG is duplicated on each non-home page).

After the header, every page composes from a shared section vocabulary defined in `styles.css`:

- `section` (max-width 1120px wrapper) with `.section-label` + `.section-title`
- `.pillars` — three-column mission/team triplets
- `.exp-list` / `.exp` — expedition table rows
- `.posts` / `.post` — journal entries
- `.bio-grid`, `.contact-grid`, `.summits` — page-specific compositions
- `.quote` — pull-quote block with `<blockquote>` + `<cite>`

## Design system (in `styles.css`)

Tokens live on `:root` and are used pervasively — change them there, not inline:

- `--navy-0/1/2/3` — dark blue palette (background, panels)
- `--ice` — primary text
- `--mist` — secondary text, hairline metadata
- `--accent` (`#c7d6ea`) — italic highlights inside Fraunces headlines (used as `<em>`)
- `--rule` / `--rule-strong` — hairline border colors
- Type pairing: **Fraunces** (display serif, italics for accent) + **Inter** (body sans), loaded from Google Fonts via `@import` at the top of `styles.css`

The italic `<em>` highlight inside h1/h2 is a deliberate stylistic motif. Preserve it when editing copy.

## Assets

- `assets/hero.jpg` — homepage hero photograph (Mark's iPhone summit photo, optimized to 2400px wide / ~340 KB / progressive JPEG). When replacing or adding photos, target the same dimensions and quality so page weight stays predictable.
- Additional photos (e.g. `assets/denali.jpg`, `assets/ama-dablam.jpg`) are **not yet wired in**; if added, hand-edit the relevant page to reference them.

## Deployment

`.github/workflows/pages.yml` deploys the entire repo root to GitHub Pages, triggered on push to **`claude/brutalist-cancer-campaign-site-oOOSw`** only.

This means:
- The default deploy branch is **not** `main`. If the working branch is renamed, update the workflow's `branches:` filter.
- GitHub Pages must be enabled in repo settings with **Source: GitHub Actions** for the workflow to publish.
- Live URL (once enabled): `https://markparella.github.io/climb-against-cancer/`

## Editorial conventions

- Beneficiary is the **Cancer Support Community South Bay** (CSC South Bay); donation portal: `https://cscrb.gnosishosting.net/Portal/Donate/ClimbAgainstCancer`. Don't change either without confirmation.
- Founder copy: Mark Parella, US Navy veteran, Denver, Colorado, founded 2016.
- The "USA × Nepal Friendship Team" reference and June 8, 2022 Denali summit are factual claims about real expeditions — don't invent new ones; ask first.
- Repeated tagline / creed: *"Bringing happiness and self-worth by making a difference in someone else's life."*
