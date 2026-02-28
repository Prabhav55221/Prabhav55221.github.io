# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

A Jekyll-based personal academic website deployed via GitHub Pages at `https://prabhav55221.github.io`. There is no build step to run locally beyond standard Jekyll commands — pushing to `main` deploys automatically.

## Local Development

```bash
bundle exec jekyll serve        # serve at http://localhost:4000
bundle exec jekyll serve --drafts  # include draft notes
```

There is no Gemfile committed to the repo. If one doesn't exist locally, create a minimal one:
```ruby
source "https://rubygems.org"
gem "jekyll"
gem "kramdown-parser-gfm"
gem "rouge"
```

## Architecture

### Layout hierarchy
- `_layouts/default.html` — the single base layout. Contains the full HTML shell: sidebar with photo/nav/social links, main content area. Loads KaTeX, Font Awesome, academicons, Inter font, and Google Analytics. All pages ultimately use this.
- `_layouts/notes.html` — wraps `default`, adds note header (title, date, topic, paper reference) and a "← Back to Notes" footer link.
- `_layouts/reading-note.html` — wraps `default`, adds a private-banner warning and a "← Back to Reading Log" footer link. These pages are not linked from the main nav.
- `_layouts/page.html` — thin wrapper used for standalone pages.

### Content collections (defined in `_config.yml`)
- `_notes/` → public notes, served at `/notes/:name/`, default layout `notes`. Listed and searched on `notes.md`.
- `_reading_notes/` → private reading log, served at `/reading-notes/:name/`, default layout `reading-note`. Listed on `reading-notes/index.md`. Not linked from sidebar nav; robots-noindexed via JS.

### Pages
- `index.md` — homepage (research interests as `<details>` dropdowns, recent updates)
- `publications.md` — publications list (raw HTML for styling with `.pub-*` classes)
- `notes.md` — notes index with client-side JS keyword search filtering `.notes-item` cards
- `random.md` — misc/personal page
- `reading-notes/index.md` — private reading log index

### Styling
All custom styles live in `assets/css/prabhav.css` — there is no separate component CSS. Key design tokens (CSS variables in `:root`):
- `--accent: #db3e75` — the signature pink used for headings, borders, highlights
- `--bg: #f5f4ef` — warm beige page background
- `--sidebar-bg: #ffffff` — white sidebar
- `--font-sans: 'Inter'`, `--font-mono: 'SFMono-Regular'`

CSS class prefixes by section: `.ps-*` (sidebar/layout), `.pub-*` (publications), `.notes-*` (notes index), `.rn-*` (reading notes), `.misc-*` (random page).

### Math
KaTeX is loaded in `_layouts/default.html` (not MathJax despite `_config.yml` mentioning it). Delimiters: `$$...$$` for display math, `$...$` for inline.

## Adding Content

### New public note (`_notes/`)
```yaml
---
layout: notes
title: "Note Title"
date: 2025-01-01
topic: "NLP"           # optional — shown as colored tag
paper_title: "..."     # optional
paper_authors: "..."   # optional
paper_url: "https://arxiv.org/abs/..."  # optional
excerpt: "..."         # shown in notes index card
---
```

### New reading log entry (`_reading_notes/`)
```yaml
---
layout: reading-note
title: "Week of Jan 1"
date: 2025-01-01
week_of: "January 1, 2025"
papers: "Paper A, Paper B"   # optional
---
```

### Images
Store in `images/` at repo root. Reference as `https://Prabhav55221.github.io/images/filename.ext` (absolute URL, not relative — required for Jekyll collections).
