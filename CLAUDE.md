# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Jekyll-based personal blog hosted on GitHub Pages, using the [Jekyll Now](https://github.com/barryclark/jekyll-now) theme. Deployment is automatic when pushing to `master`.

## Local Development

```bash
# Install dependencies (if Gemfile exists locally)
bundle install

# Serve locally with live reload
jekyll serve
# or
bundle exec jekyll serve

# View at http://127.0.0.1:4000/
```

No `Gemfile` or `package.json` is committed — these are generated locally and excluded via `.gitignore`.

## Architecture

**Jekyll directory layout:**
- `_layouts/` — Page templates: `default.html` (base), `page.html`, `post.html` (includes Disqus)
- `_includes/` — Reusable partials: `disqus.html`, `analytics.html`, `meta.html`, `svg-icons.html`
- `_posts/` — Blog posts in Markdown, filename format: `YYYY-MM-DD-title.md`
- `_sass/` — Sass partials imported by `style.scss`
- `images/` — Static assets

**Key config (`_config.yml`):**
- Markdown: Kramdown with GFM
- Syntax highlighting: Rouge
- Plugins: `jekyll-sitemap`, `jekyll-feed`
- Comments: Disqus (shortname: `gyaan-1`)

## Writing Posts

Create `_posts/YYYY-MM-DD-post-title.md` with front matter:

```yaml
---
layout: post
title: "Post Title"
---
```

Posts support Markdown with Rouge syntax highlighting via fenced code blocks.
