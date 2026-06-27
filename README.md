# Alex Pavlov Blog

My personal blog — a fast, statically generated site built with **Astro**, edited through **Pages CMS**, and hosted on **Vercel**. Content lives as markdown in this repo (Git-as-CMS): there is no backend, no database, and near-zero client-side JavaScript.

Design based on [pycoder2000/blog](https://github.com/pycoder2000/blog).

## Demo

[alexpavlov.dev](https://alexpavlov.dev)

## How it works (Git-as-CMS)

The GitHub repository **is** the database. The loop:

1. Markdown files live in `content/{posts,projects,pages}/`.
2. **[Pages CMS](https://pagescms.org)** (configured by `.pages.yml`) edits those files through a GitHub App and commits them — editing happens at [cms.alexpavlov.dev](https://cms.alexpavlov.dev), not on the site itself.
3. The commit triggers a **Vercel** redeploy.
4. **Astro content collections** parse the markdown (typed with zod) and run it through the markdown pipeline (GFM, table of contents, Prism highlighting, heading anchors).
5. The statically generated site updates.

## 📚 Tech stack

| Concern | Tool |
| --- | --- |
| Framework | [Astro](https://astro.build) |
| Content | [Astro content collections](https://docs.astro.build/en/guides/content-collections/) (markdown + zod) |
| Editing | [Pages CMS](https://pagescms.org) (Git-based) |
| Styling | [Tailwind CSS](https://tailwindcss.com) |
| Comments | [Giscus](https://giscus.app) (GitHub Discussions) |
| Code highlighting | [rehype-prism-plus](https://github.com/timlrx/rehype-prism-plus) |
| Analytics | [Vercel Analytics](https://vercel.com/analytics) + Speed Insights |
| Deployment | [Vercel](https://vercel.com) |

## 🪜 Project structure

```bash
📦 root
├── 🗂️ content               # Markdown content (the "database")
│  ├── 🗂️ posts
│  ├── 🗂️ projects
│  └── 🗂️ pages
├── 🗂️ src
│  ├── 🗂️ components          # .astro UI components
│  ├── 🗂️ layouts             # page shell + <head>/SEO
│  ├── 🗂️ pages               # file-based routes
│  ├── 📝 content.config.ts   # content collections (glob loader + zod)
│  └── 📝 consts.ts
├── 🗂️ public                # static assets (images, favicon, robots.txt)
├── 🗂️ styles                # global CSS + Prism theme
├── 📝 .pages.yml            # Pages CMS configuration
├── 📝 astro.config.mjs      # Astro config + markdown pipeline
├── 📝 tailwind.config.js
└── 📝 vercel.json           # redirects + security headers
```

## Features

- Statically generated from markdown in `content/`
- Edited via Pages CMS (Git-based, no backend or auth to run)
- **Near-zero client JS** — no React; only small vanilla scripts for the header typewriter and the about-me notation effect
- Code highlighting (Prism) + automatic table of contents
- Comments via Giscus
- Tags, projects, RSS feed (`/feed.xml`), `sitemap`, `robots.txt`
- SEO: canonical URLs, OpenGraph, `BlogPosting` JSON-LD
- Security headers (CSP, HSTS, X-Frame-Options, …) via `vercel.json`
- Drafts: `status: draft` posts are excluded from the build, sitemap, and tag pages (and 404 on direct access)
- Self-hosted Inter (variable) font, forced dark mode, mobile-friendly

## Environment variables

**None required.** The site builds and runs with no secrets. Content editing is handled by the self-hosted Pages CMS GitHub App at [cms.alexpavlov.dev](https://cms.alexpavlov.dev); comments are handled by Giscus.

## Quick start

```bash
npm install
npm run dev       # dev server at http://localhost:4321
npm run build     # production build → dist/
npm run preview   # serve the production build locally
```

## Editing content

Either:

- **Pages CMS** — open the repo in [cms.alexpavlov.dev](https://cms.alexpavlov.dev) (with the Pages CMS GitHub App installed), edit Posts / Projects / Pages, and save. The commit triggers a Vercel redeploy.
- **Directly** — edit the markdown in `content/` and push to the repo.

Posts use frontmatter: `title`, `status` (`published`/`draft`), `slug`, `description`, `tags` (list), `coverImage`, `featured` (boolean), `publishedAt`.
