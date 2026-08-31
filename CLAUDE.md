# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Jekyll-based academic personal website using the **Academic Pages** theme (forked from Minimal Mistakes). It is deployed via GitHub Pages at `di-viner.github.io` and belongs to Siye Wu, an M.S. student in CS at Fudan University.

## Commands

### Local Development

```bash
# Install Ruby dependencies
bundle install

# Run Jekyll dev server (live reload, accessible at localhost:4000)
bundle exec jekyll serve -l -H localhost

# Run with Docker
docker compose up

# Build JavaScript (uglify vendor JS + custom JS into assets/js/main.min.js)
npm run build:js
```

### CV Pipeline

The CV page uses a two-step pipeline: a Markdown source (`_pages/cv.md`) is converted to JSON (`_data/cv.json`) which is then rendered by `_includes/cv-template.html`.

```bash
# Convert _pages/cv.md → _data/cv.json
bash scripts/update_cv_json.sh

# Or run the Python converter directly:
python3 scripts/cv_markdown_to_json.py \
  --input _pages/cv.md \
  --output _data/cv.json \
  --config _config.yml
```

### Generating Publications & Talks

Python scripts in `markdown_generator/` convert TSV files into Jekyll collection markdown files:

```bash
cd markdown_generator
python3 publications.py publications.tsv   # generates _publications/*.md
python3 talks.py talks.tsv                 # generates _talks/*.md
```

## Architecture

### Key Directories

| Directory | Purpose |
|---|---|
| `_config.yml` | Site-wide configuration (author info, collections, plugins, Sass settings) |
| `_pages/` | Top-level pages: about, cv, teaching, talks, portfolio |
| `_data/` | Structured data files: `cv.json` (CV content), `navigation.yml` (nav menu), `authors.yml`, `ui-text.yml` |
| `_includes/` | Template partials. `cv-template.html` renders the CV from `site.data.cv`. `author-profile.html` renders the sidebar. |
| `_layouts/` | Page layouts: `default.html`, `single.html`, `cv-layout.html` (CV-specific), `talk.html`, `compress.html` |
| `_sass/` | SCSS stylesheets organized into `theme/`, `layout/`, `vendor/`, `include/` |
| `_publications/`, `_talks/`, `_teaching/`, `_portfolio/` | Jekyll collections — each file is a markdown entry with YAML front matter |
| `assets/` | Static assets: `js/main.min.js` (generated), `css/`, images |
| `files/` | Downloadable files (PDFs, etc.) served at `/files/...` |
| `images/` | Image assets referenced throughout the site |

### CV Rendering Flow

1. `_pages/cv.md` — Markdown source with sections (Education, Work Experience, Skills, etc.)
2. `scripts/cv_markdown_to_json.py` — Parses the markdown and `_config.yml`, outputs `_data/cv.json`
3. `_pages/cv-json.md` — A page that uses `layout: cv-layout` and includes `cv-template.html`
4. `_includes/cv-template.html` — Renders the JSON into styled HTML, handles GPA display, date formatting, publication/project listings

### Navigation

Defined in `_data/navigation.yml` as one-page anchor links (`/#about-me`, `/#educations`, etc.). The `_includes/masthead.html` renders the nav bar. To add a new nav item, add an entry to `navigation.yml`.

### Theme Customization

- Theme colors and typography are in `_sass/_themes.scss`
- Site theme is selected via `site_theme` in `_config.yml` (options: `default`, `air`, `sunrise`, `mint`, `dirt`, `contrast`)
- Custom CSS for the CV layout lives in `assets/css/cv-layout.css`

### Ruby Version

The `.ruby-version` file pins Ruby to 3.1.6. The Dockerfile uses Ruby 3.2.
