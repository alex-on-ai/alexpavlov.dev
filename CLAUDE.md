# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with
code in this repository.

## Commands

```bash
npm i             # install dependencies
npm run dev       # Astro dev server
npm run build     # production build to dist/
npm run preview   # serve the production build locally
npm run check     # astro check
```

Stack: **Astro 5, Tailwind CSS, TypeScript**. There is no test framework.
Formatting is Prettier (`.prettierrc`: single quotes, no semicolons, 2-space
tabs, `proseWrap: always`).

## Architecture: Git-as-CMS

The GitHub repository is the database. Content lives as markdown; editing
happens by committing back to GitHub, which triggers a Vercel redeploy that
regenerates the static site.

1. Markdown files live in `content/{posts,projects,pages}/` (one `.md` file per
   entry, filename should match `slug`).
2. Astro content collections (`src/content.config.ts`) parse the markdown at
   build time with zod schemas. The body is rendered by Astro's markdown
   pipeline.
3. The public site (`src/pages/blog/**`) renders statically from the Astro
   collections. Draft posts (`status: draft`) are excluded from public listings
   and return 404 on direct access.
4. Editing is done through self-hosted [Pages CMS](https://pagescms.org) at
   [cms.alexpavlov.dev](https://cms.alexpavlov.dev), configured by the repo-root
   `.pages.yml`. It commits markdown changes directly to this repo via a GitHub
   App. A commit triggers a Vercel deploy, Astro rebuilds, and the static site
   updates.

### Astro config (`astro.config.mjs`)

Astro owns the markdown rendering pipeline. It uses `remark-gfm`, `remark-toc`,
`rehype-slug`, `rehype-autolink-headings`, `rehype-code-titles`, and
`rehype-prism-plus`. Keep markdown behavior in `astro.config.mjs` unless a
component needs page-specific rendering logic.

### Pages CMS config (`.pages.yml`)

Models the three collections against the real frontmatter. `media.input:
public` / `output: /` lets the editor browse and reference public assets.
`tags` and `technologies` are lists, `featured` is boolean, `status` is
`published` / `draft`, `body` is the `rich-text` field mapped to the markdown
body, and `filename: '{fields.slug}.md'` keeps filename == slug for new files.

### Request/render flow notes

- `astro.config.mjs` redirects `/` to `/blog`, adds sitemap support, and keeps
  the old URL shape without trailing slashes.
- Comments use Giscus (`src/components/Giscus.astro`).
- Layout and global styles force the dark visual system. Analytics use
  `@vercel/analytics` and `@vercel/speed-insights`.

## Conventions & gotchas

- Prefer existing Astro component patterns in `src/components` and
  `src/layouts`.
- Filename == slug is load-bearing for CMS ergonomics: `.pages.yml`
  `filename: '{fields.slug}.md'` preserves this for newly created content.
- Dynamic routes live in `src/pages/blog/posts/[slug].astro` and
  `src/pages/blog/tags/[tag].astro`.
- Content dir is `content/`. Three document types: Post (`content/posts`),
  Project (`content/projects`), Page (`content/pages`).
- Home/blog listing filters on `status == 'published'` and splits Featured vs
  Latest by `featured === true`.
- If forking: Pages CMS is bound to whatever repo you authorize the GitHub App
  on.

## Environment variables

The public site builds and runs with no required env vars. Pages CMS auth is
handled by the separate self-hosted CMS GitHub App; Giscus is client-side.
