# Site Redesign Plan — Damian Małecki

## Overview

Migrate from Jekyll `startbootstrap-clean-blog-jekyll` (remote theme) + separate WordPress About Me
to a single, consolidated **al-folio** Jekyll site hosted on GitHub Pages.

**Goal:** Professional personal brand site positioning Damian Małecki as a Senior QA / Test Automation Engineer.

---

## Theme & Setup

- **Theme:** [al-folio](https://github.com/alshedivat/al-folio) — use "Use this template" (not fork)
- **Repo:** `maleckidd.github.io` (unchanged)
- **Deployment:** GitHub Actions workflow → builds to `gh-pages` branch → served by GitHub Pages
- **Language:** English
- **Dark mode:** enabled (respects `prefers-color-scheme`; manual toggle available)
- **Comments:** disabled (no Giscus)
- **Google Analytics:** disabled for now

---

## Navigation

| Page | Source | Notes |
|---|---|---|
| **About** (homepage) | `_pages/about.md` | Bio, photo, news, latest posts |
| **Blog** | `_pages/blog.md` + `_posts/` | Migrated posts + future writing |
| **Projects** | `_pages/projects.md` + `_projects/` | Real repos from GitHub |
| **CV** | `_pages/cv.md` + `_data/cv.yml` | Full career data, optional PDF |
| **Repositories** | `_pages/repositories.md` | Auto-cards from GitHub |

**Disabled al-folio sections:** Publications (BibTeX), Teaching, Bookshelf, People.

---

## Social Links

- LinkedIn: `damian-malecki`
- GitHub: `Maleckidd`
- Instagram: **removed** (private, unrelated to professional brand)
- Email: optional **removed** 

---

## About Page

### Bio

> Senior Test Automation Engineer at Allegro with 8+ years of experience in software quality.
> Currently ensuring quality across the entire Smart loyalty program division, with experience
> coordinating projects such as the Smart lottery. Specialized in test automation with Cypress
> and Playwright, web and mobile application testing, and quality engineering. Previously helped
> build QA processes at Infakt and Efento. Electrical Engineering background (MSc, Lublin
> University of Technology) with international experience including ERASMUS in Spain.

### Profile photo
- Source: current WordPress About Me (`myPic2-1024x1024.jpg`)
- Target: `assets/img/prof_pic.jpg`

### News (3 entries in `_news/`)
- Senior Test Automation Engineer promotion @ Allegro (Apr 2024)
- Mobile Application Security course — Niebezpiecznik.pl (Sep 2023)
- Web Application Security course — Niebezpiecznik.pl (Sep 2021)

---

## Blog — Post Migration

Migrate all 3 existing posts from `_posts/`. Changes required per post:

1. Rename `.markdown` → `.md`
2. Update front matter:
   ```yaml
   layout: post
   title: "..."
   date: YYYY-MM-DD HH:MM:SS +0200
   description: "short description for listing"
   tags: [tag1, tag2]
   giscus_comments: false
   related_posts: true
   ```
3. Replace `{% highlight language %}...{% endhighlight %}` + `<div style="background-color:lightgrey;">` wrappers with standard backtick fences (` ```language `). Al-folio handles syntax highlighting natively.
4. Move images from `img/posts/` to `assets/img/posts/` and update paths from raw GitHub URLs to local relative paths.

### Posts to migrate

| Filename | Tags |
|---|---|
| `2022-07-26-cypress-waits.markdown` | `[cypress, test-automation]` |
| `2022-11-09-bugBonty-first-try.markdown` | `[security, bug-bounty]` |
| `2023-03-28-test-fest.markdown` | `[conferences, quality-engineering]` | Title: "My takeaways from test:fest 2023" |

---

## Projects (`_projects/`)

Based on real public GitHub repositories:

| Project | Repo | Description |
|---|---|---|
| Playwright + OWASP Juice Shop | `PlaywrightExample-OWASPJuiceShop` | Test automation suite for OWASP Juice Shop using Playwright |
| Cypress + OWASP Juice Shop | `CypressExample-OWASPJuiceShop` | Test automation suite for OWASP Juice Shop using Cypress |
| Playwright Playground | `playwright-playground` | Experiments and examples with Playwright |

> **Note:** Add descriptions to these GitHub repos before launching — repos without descriptions
> look abandoned when rendered as cards.

---

## CV (`_data/cv.yml`)

### Experience

**Senior Test Automation Engineer** — Allegro (Apr 2024 – present)
> Complex testing of web applications and native iOS and Android apps. Web test automation with Cypress. Ensuring quality across the entire Smart loyalty program division (5 dev teams). Coordinating projects such as the Smart lottery and coordination of testing during expansion into new markets. Building tools to support testing across the company. Recruitment team member.

**Test Automation Engineer** — Allegro (Nov 2023 – Mar 2024)

**Senior Software Test Engineer** — Allegro (Jun 2022 – Oct 2023)

**QA Engineer** — Infakt Sp. z o.o. (Nov 2019 – May 2022)
> Complex testing of web applications (REST API, security, acceptance tests). Test automation with Java/Selenium and Postman. BDD test automation with Cucumber. Collaboration with the dev team (Agile/Scrum) and customer service.

**Software Tester** — Efento (Jul 2018 – Oct 2019)
> Testing of web applications (REST API, acceptance tests), mobile applications (Android), Bluetooth and IoT (hardware/software). Technical documentation, risk analysis and test reports. Implementation of QA processes into company structures. Introduced web application test automation with Cypress.

### Education

| Institution | Degree | Years |
|---|---|---|
| Politechnika Lubelska | MSc, Electrical Engineering | 2017–2018 |
| Politechnika Lubelska | BEng, Electrical Engineering | 2012–2017 |
| Universidad de Jaén | ERASMUS exchange, Electrical Engineering | 2015 |
| ZSP im. Jana Pawła II, Radzyń Podlaski | IT Technician | 2008–2012 |

### Certifications

| Name | Issuer | Date |
|---|---|---|
| Bezpieczeństwo Aplikacji Mobilnych (Mobile App Security) | Niebezpiecznik.pl | Sep 2023 |
| Atakowanie i Ochrona Webaplikacji (Web App Security) | Niebezpiecznik.pl | Sep 2021 |

### Skills

- **Test Automation:** Cypress, Playwright, Selenium
- **Languages:** JavaScript, TypeScript, Java
- **API & Tools:** Postman, Cucumber
- **Security Testing:** Burp Suite, OWASP ZAP
- **Project Management:** Jira, Zephyr
- **Testing:** Web, Mobile (iOS, Android), API, IoT, Security

---

## Repositories Page (`_data/repositories.yml`)

- Auto-display repos from GitHub user: `Maleckidd`
- Pinned repos to feature: `PlaywrightExample-OWASPJuiceShop`, `CypressExample-OWASPJuiceShop`

---

## Implementation Steps

1. **Prepare repo** — clone al-folio template, preserve `_posts/` and `img/`, remove old files, copy al-folio structure
2. **Configure `_config.yml`** — personal data, URL, baseurl, disable unused features
3. **About page** — write bio, place profile photo
4. **Setup `_data/`** — `cv.yml`, `socials.yml`, `repositories.yml`
5. **Migrate 3 blog posts** — front matter, code blocks, images
6. **Projects pages** — create 3 project cards from real repos
7. **CV page** — fill `_data/cv.yml` with career data
8. **News entries** — 3 items in `_news/`
9. **Cleanup** — remove all al-folio demo content, update `AGENTS.md` and `README.md`
10. **Test build** — `bundle exec jekyll build` or `docker compose up`
11. **Deploy** — push, verify GitHub Actions, check live site

---

## Out of Scope (this iteration)

- New blog posts
- Custom domain
- Google Analytics / other analytics
- SCSS / color customization (default al-folio is clean and professional)
- SEO optimization beyond basic `description`
- Giscus comments

---

## Notes & Risks

1. **Post URLs will change** — al-folio uses `/blog/:year/:title/` instead of the old pattern.
   Acceptable given low traffic and only 3 posts.
2. **WordPress About Me** — not part of this repo; can be left as-is or taken down separately.
3. **Local dev** — al-folio recommends Docker (`docker compose up`, serves at `localhost:8080`).
   Native Ruby also works: `bundle install && bundle exec jekyll serve`.
4. **Repo descriptions** — GitHub repos used in Projects/Repositories pages need descriptions
   added directly on GitHub before launch.
