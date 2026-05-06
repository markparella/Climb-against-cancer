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

Five hand-maintained HTML pages share one stylesheet (`styles.css`). There is no templating, so several blocks are **duplicated verbatim across pages** and must be edited in lock-step:

- `<nav class="nav">` — present on all 5 pages (with the active link's `class="is-active"` differing per page)
- `<footer class="site">` + `.foot-bottom` — identical on all 5 pages
- `<div class="page-head__ridge">` inline mountain-ridge SVG — identical on the 4 non-home pages

When adding/removing a nav link, changing the footer, or tweaking the ridge silhouette, update **every page that contains that block**.

Each page follows one of two header patterns:

- **`index.html`** uses `<section class="hero">` — full-bleed photo hero (`assets/hero.jpg`), dark gradient overlay, the `<nav>` sits inside it, content centered.
- **All other pages** use `<header class="page-head">` — quieter dark-navy header with the same `<nav>`, a Fraunces serif title, and the duplicated ridge SVG at the bottom.

After the header, every page composes from a shared section vocabulary defined in `styles.css`:

- `section` (max-width 1120px wrapper) with `.section-label` + `.section-title`
- `.pillars` — three-column mission/team triplets
- `.exp-list` / `.exp` — expedition table rows
- `.posts` / `.post` — journal entries
- `.bio-grid`, `.contact-grid`, `.summits` — page-specific compositions
- `.quote` — pull-quote block with `<blockquote>` + `<cite>`

### About vs. Expeditions — two different lists

Don't conflate them when editing:

- `about.html` → `.summits` "Summit Log" — the 5 peaks Mark has summited personally (Aconcagua, Everest, Ama Dablam, Denali, Mt. Shasta).
- `stories.html` → `.exp-list` — the 6 campaign expeditions (some with year `—` placeholders for the foundational/training climbs).
- `index.html` → `.exp-list` — a shorter recent-expeditions teaser linking to `stories.html`.

## Design system (in `styles.css`)

Tokens live on `:root` and are used pervasively — change them there, not inline:

- `--navy-0/1/2/3` — dark blue palette (background, panels)
- `--ice` — primary text
- `--mist` — secondary text, hairline metadata
- `--accent` (`#c7d6ea`) — italic highlights inside Fraunces headlines (used as `<em>`)
- `--rule` / `--rule-strong` — hairline border colors
- Type pairing: **Fraunces** (display serif, italics for accent) + **Inter** (body sans), loaded from Google Fonts via `@import` at the top of `styles.css`

The italic `<em>` highlight inside h1/h2 is a deliberate stylistic motif. Preserve it when editing copy.

## Contact form (no backend)

`contact.html` posts via `<form action="mailto:climb@climbagainstcancer.com" method="post" enctype="text/plain">`. There is no server, no JS, no validation beyond native HTML. Don't introduce a fetch/AJAX submit unless you're also wiring up an actual endpoint.

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

Pinned facts — don't change without confirmation:

- Beneficiary: **Cancer Support Community South Bay** (CSC South Bay), Redondo Beach, California.
- Donation portal: `https://cscrb.gnosishosting.net/Portal/Donate/ClimbAgainstCancer`.
- Contact email: `climb@climbagainstcancer.com`.
- Instagram: `https://www.instagram.com/climbagainstcancer/` (`@climbagainstcancer`).
- Founder copy: Mark Parella, US Navy veteran, Denver, Colorado, founded 2016.
- The "USA × Nepal Friendship Team" reference and June 8, 2022 Denali summit are factual claims about real expeditions — don't invent new ones; ask first.
- Repeated tagline / creed: *"Bringing happiness and self-worth by making a difference in someone else's life."*
