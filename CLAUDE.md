# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
# Install dependencies
bundle install

# Run local dev server (auto-rebuilds on file changes)
bundle exec jekyll serve

# Build for production
bundle exec jekyll build

# Build with drafts visible
bundle exec jekyll serve --drafts
```

The site is deployed automatically via GitHub Actions on push to `main`. The workflow (`.github/workflows/jekyll.yml`) builds with `JEKYLL_ENV=production` and deploys to GitHub Pages.

## Architecture

This is a Jekyll static blog hosted at `https://blog.guarismo.net` (custom domain via `CNAME`), deployed to GitHub Pages. Theme: `pages-themes/hacker@v0.2.0` (remote theme — dark terminal aesthetic).

### Custom layout system

The site overrides the remote theme's default layout entirely:

- `_layouts/default.html` — Base layout: includes sidebar, header, main content area, footer, and sidebar JS (toggle, collapsible menus, localStorage state persistence)
- `_layouts/post.html` — Wraps `default`, adds post metadata (date, author, category, tags) and back link
- `_includes/sidebar.html` — Renders the navigation tree from `_data/navigation.yml`, supports 3-level nesting: top-level → children → sub-children (with `tag`-based anchor links)

### Navigation data

`_data/navigation.yml` drives the sidebar. Each item can have:
- `url` — direct link
- `icon` — emoji prefix
- `children` — nested items (also support `children` for a third level)
- `tag` — instead of `url`, links to an anchor on the parent's `url` page (used for tag-filtered sections)

### Content pages

- `_posts/` — All blog posts. Frontmatter fields used: `layout`, `title`, `date`, `author`, `category`, `tags` (array)
- `ctf/sans-hhc.md`, `ctf/metasploitable.md` — Category index pages; use Liquid `where_exp` to filter posts by tag into `<div id="tag-name">` sections matching the nav's `tag` anchors
- `index.md` — Home page showing recent 10 posts + all posts
- `about.md`, `misc.md` — Static pages

### Post naming and frontmatter

Posts follow standard Jekyll convention: `_posts/YYYY-MM-DD-slug.md`

Minimal frontmatter for a new post:
```yaml
---
layout: post
title: "Post Title"
date: 2026-02-25
author: gu4r15m0
category: ctf
tags: [hhc-2022, some-other-tag]
---
```

Tags connect posts to the category index pages — e.g., `tags: [hhc-2022]` makes the post appear under the HHC 2022 section on `/ctf/sans-hhc/`.

### Assets

- `assets/css/style.css` — Full custom CSS (sidebar layout, responsive hamburger menu on mobile, hacker theme color palette: `#1a1a2e` bg, `#b5e853` green, `#63c0f5` blue links)
- `assets/images/` — Downloaded post images (migrated from Blogger)
