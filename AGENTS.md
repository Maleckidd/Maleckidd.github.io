# AGENTS.md

Guidelines for agentic coding agents working in this repository.

## Project Overview

This is a personal brand / blog site for **Damian Malecki** (Senior QA / Test
Automation Engineer), built with **Jekyll 4.3.2** using the
[al-folio](https://github.com/alshedivat/al-folio) theme. Posts are written in
Markdown and rendered with the Kramdown parser. The site is deployed to GitHub
Pages at `https://maleckidd.github.io`.

**Deployment model:** GitHub Pages cannot build al-folio natively (requires
gems not on the GH Pages whitelist). The site is **pre-built locally** and the
`_site/` output is pushed directly to the `gh-pages` branch with a `.nojekyll`
file. Source code lives on `main`.

No Cursor rules (`.cursor/rules/`, `.cursorrules`) or Copilot instructions
(`.github/copilot-instructions.md`) exist in this repository.

---

## Build & Serve Commands

All commands should be run from the repository root with Bundler.

| Command | Purpose |
|---|---|
| `bundle install` | Install all gem dependencies |
| `bundle exec jekyll serve` | Build and serve locally at `http://localhost:4000` (watch mode) |
| `bundle exec jekyll serve --livereload` | Same with live reload |
| `bundle exec jekyll build` | Build the site to `_site/` without serving |
| `bundle exec jekyll doctor` | Check configuration and flag known issues |

Jekyll has **no automated test suite**. "Testing" is done by building and
visually inspecting the local server output.

### Quick sanity check (build without serving)

```sh
bundle exec jekyll build 2>&1
```

A successful build exits with code `0` and outputs to `_site/`. Liquid errors
or broken includes will surface here. Sass deprecation warnings and
Jekyll-Minifier `harmony` filter messages are known and harmless.

### Deploy to gh-pages

```sh
bundle exec jekyll build

# In a temp dir:
cd /tmp && rm -rf gh-pages-deploy && mkdir gh-pages-deploy && cd gh-pages-deploy
git init && git remote add origin git@github.com:Maleckidd/Maleckidd.github.io.git
git fetch origin gh-pages && git checkout gh-pages
git rm -rf .
cp -r /path/to/repo/_site/. .
touch .nojekyll
git add -A && git commit -m "deploy: <message>"
git push origin gh-pages
```

---

## Repository Structure

```
.
├── _config.yml              # Site-wide configuration (al-folio settings)
├── Gemfile                  # Ruby gem dependencies (al-folio gems)
├── _layouts/                # Overridden al-folio Liquid layouts
│   ├── about.liquid         # About page — profile in post-header, full name bolded
│   ├── cv.liquid            # CV — uses _data/cv.yml (jekyll-get-json disabled)
│   ├── post.liquid          # Post layout — bibliography block removed
│   └── page.liquid          # Generic page — bibliography block removed
├── _includes/
│   └── header.liquid        # Navbar — last_name bolded
├── _pages/                  # Site pages
│   ├── about.md             # Homepage (nav_order: 1)
│   ├── blog.md              # Blog listing (nav_order: 2)
│   └── cv.md                # CV page (nav_order: 3)
├── _posts/                  # Blog posts (Markdown, filename: YYYY-MM-DD-slug.md)
├── _data/
│   └── cv.yml               # Career data for CV page
├── _sass/                   # Sass stylesheets (al-folio)
├── _plugins/                # Jekyll plugins
├── assets/
│   ├── img/
│   │   ├── prof_pic.jpg     # Profile photo
│   │   └── posts/           # Blog post header images (01.jpg, 02.jpg, 03.jpg)
│   ├── css/
│   ├── js/
│   └── json/                # (empty — resume.json removed)
├── _site/                   # Build output — gitignored, deployed to gh-pages
└── AGENTS.md                # This file
```

---

## Navigation

The site has **3 pages** in the navbar (in order):

1. **About** — homepage, profile, bio, social links
2. **Blog** — paginated post listing
3. **CV** — career / experience data from `_data/cv.yml`

Projects and Repositories pages have been **intentionally removed**. Do not
re-add them.

---

## Front Matter Conventions

Every post in `_posts/` must include YAML front matter:

```yaml
---
layout: post
title: "Post Title"
date: YYYY-MM-DD HH:MM:SS +0200
description: "Short description shown in post listing"
tags: [tag1, tag2]          # optional
categories: category-slug   # optional
thumbnail: assets/img/posts/NN.jpg  # optional header image
---
```

### Post filename format

```
YYYY-MM-DD-kebab-case-slug.md
```

- Date in filename must match the `date` field in front matter.
- Use `.md` extension.
- Slug is lowercase, words separated by hyphens.

---

## Markdown & Content Style

- Markdown flavor: **Kramdown** (`input: GFM` in `_config.yml` for al-folio).
- Syntax highlighting: **Rouge** — use standard triple-backtick fences:
  ````markdown
  ```javascript
  // code here
  ```
  ````
- al-folio also supports `{% highlight language %}...{% endhighlight %}` Liquid
  tags if needed, but backtick fences are preferred.

---

## CV Data (`_data/cv.yml`)

The CV page is driven by `_data/cv.yml`. The `jekyll-get-json` plugin is
**disabled** in `_config.yml` so that `_data/cv.yml` is used instead of a
JSONresume file.

Date format convention: **numeric `MM/YYYY`** (e.g., `04/2024`). Use `now`
for current positions (e.g., `04/2024 – now`).

Do **not** append `, Poland` to institution names. Spain locations are allowed
(e.g., for ERASMUS entries).

---

## Configuration (`_config.yml`) Notes

- `title: blank` — al-folio convention; the navbar brand shows
  `first_name last_name` from config instead.
- `blog_name: ""` — no separate header name on the blog listing page.
- `jekyll-get-json` is listed in plugins but disabled via `get-json: false`
  (or equivalent) so the CV uses `_data/cv.yml`.
- Dark mode is enabled (`dark_mode: true`).
- No Google Analytics (`google_analytics: ""`).
- No Giscus/Disqus comments.
- Social links: GitHub (`Maleckidd`) and LinkedIn (`damian-malecki`) only.
- Plugins are declared under `plugins:` — any new plugin also needs a
  corresponding `gem` entry in `Gemfile`.

---

## Images

- Profile photo: `assets/img/prof_pic.jpg`
- Post header images: `assets/img/posts/NN.jpg` (e.g., `01.jpg`, `02.jpg`)
- Reference post images in front matter as `thumbnail: assets/img/posts/NN.jpg`
  or in post body as `![alt]({{ 'assets/img/posts/NN.jpg' | relative_url }})`.

---

## Branches

| Branch | Contents |
|---|---|
| `main` | Source code (Jekyll project) |
| `gh-pages` | Pre-built `_site/` output + `.nojekyll` — served by GitHub Pages |

**Never commit `_site/` to `main`** — it is in `.gitignore`.

---

## Common Pitfalls

- Running `jekyll` without `bundle exec` may use a system gem version that
  differs from the pinned version in `Gemfile`.
- Posts dated in the future will not appear by default; pass `--future` to
  `jekyll serve`/`build` to include them.
- The `gh-pages` branch must always contain a `.nojekyll` file, otherwise
  GitHub Pages will try (and fail) to build the site with Jekyll.
- `_site/` is gitignored on `main` — never commit it there.
- Sass deprecation warnings (re: `@import`, `if-function`, `global-builtin`)
  are from al-folio's upstream dependencies and are harmless.
- Jekyll Minifier `harmony` filter warnings are harmless.
