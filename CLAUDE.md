# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```sh
npm run dev       # Start local dev server at localhost:4321
npm run build     # Build production site to ./dist/
npm run preview   # Preview the built site locally
npx astro check  # Type-check .astro files
```

Deploy is via Cloudflare Workers (static assets): `npx wrangler deploy` (requires `npm run build` first). The `wrangler.jsonc` points at `./dist` as the asset directory.

## Architecture

This is an [Astro](https://astro.build) static site (blog template) deployed as a Cloudflare Worker serving static assets.

**Two layout systems coexist:**
- `src/layouts/BaseLayout.astro` — custom minimal layout used by most pages (home, about, resume, contact, impact, projects). Embeds its own inline styles.
- `src/layouts/BlogPost.astro` + `src/components/BaseHead.astro` — the original Astro blog template layout used exclusively by blog posts. Pulls in `src/styles/global.css` and the Atkinson font via `BaseHead`.

This means blog posts get different styling (Atkinson font, CSS vars from `global.css`) than the rest of the site (system fonts, inline styles in `BaseLayout`).

**Content layer:** Blog posts live in `src/content/blog/` as `.md` or `.mdx` files. The collection schema (`src/content.config.ts`) requires `title`, `description`, and `pubDate`; `updatedDate` and `heroImage` are optional. The dynamic route `src/pages/blog/[...slug].astro` generates one page per post using `getStaticPaths`.

**Global site constants** (`src/consts.ts`): `SITE_TITLE` and `SITE_DESCRIPTION` are used by `BaseHead` for RSS and meta tags — update these if the site title changes.

**Font:** Atkinson Hyperlegible is self-hosted in `src/assets/fonts/` and registered in `astro.config.mjs` as a local font provider with CSS variable `--font-atkinson`. It only applies to pages using `global.css` (blog posts), not `BaseLayout` pages.
