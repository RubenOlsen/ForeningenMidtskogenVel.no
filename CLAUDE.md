# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## About

Website for Foreningen Midtskogen Vel (midtskogenvel.no), a neighbourhood association in Os, Norway. Hugo static site with Tailwind CSS 4, deployed as a Cloudflare Worker (static assets from `public/`). All content is Norwegian (nynorsk mostly). Pushing to `main` publishes the site.

## Commands

```bash
npm install          # install Tailwind, Prettier, Playwright
npm run dev          # NODE_ENV=development hugo server --disableFastRender
hugo --gc --minify   # production build into public/
hugo new content styret/2025-12-03\ -\ referat\ styremote.md   # new page from archetypes/default.md (draft = true)
```

- Needs Hugo extended (min 0.147.8; prod pins 0.152.2 in `build.sh`) and Dart Sass.
- `build.sh` is the Cloudflare build script (`wrangler.toml`). It downloads Hugo, Go, Node and Sass, then runs `hugo --gc --minify`. Bump versions there.
- `tests/example.spec.ts` is the untouched Playwright template (it hits playwright.dev). There are no real tests, and `playwright.config.ts` is gitignored.
- `public/`, `resources/`, `hugo_stats.json` and `package-lock.json` are gitignored.

## Architecture

- `hugo.yaml` sets the theme `hugo-agency-web` (in `themes/`, vendored). It also mounts `hugo_stats.json` as `assets/notwatching/hugo_stats.json`, so Tailwind only generates classes it finds in the build. If a Tailwind class has no effect, rebuild so the stats refresh.
- Site-level layouts in `layouts/` (only `shortcodes/embed-pdf.html`) override or extend the theme. Page templates live in `themes/hugo-agency-web/layouts/` (`home.html`, `list.html`, `single.html`, `_partials/`).
- **Home page is data driven**: `data/home/*.yaml` (hero, features, highlights, clients) and `data/shared/footer.yaml` feed the theme partials. The hero has an `importantTitle`/`importantText`/`importantLink` banner for announcements. Remove or edit it when the event has passed.
- **Navigation and site params**: `config/_default/menus.yaml` (`main` menu plus `buttons`) and `config/_default/params.yaml`.
- **Content sections** in `content/`: `info/` (rules, bylaws, history, grant application), `styret/` (board minutes and board correspondence), `presseskriv/` (press releases), plus `omoss.md` and `hoyhotellet.md` (the campaign against the planned high-rise hotel).
- Pages use TOML front matter (`+++`) with `date`, `title`, `draft`, and optionally `showDate`, `showImage`, `image`. The summary on list pages is whatever comes before `<!--more-->`.
- PDFs and office files sit next to the markdown in `content/` (page bundles are not used). Embed PDFs with the `embed-pdf` shortcode, which loads pdf.js from `/js/pdf-js/` (`static/`).
- Images go in `assets/images/` and are referenced as `images/<name>` in front matter and YAML.

## Conventions

- Board minutes: `content/styret/YYYY-MM-DD - referat styremote.md`.
- Press releases: `content/presseskriv/YYYY-MM-DD - <title>.md`.
- Prettier with `prettier-plugin-go-template` and `prettier-plugin-tailwindcss` is configured (`prettier.config.js`) for templates.
- Commit messages are short free text in Norwegian (no conventional commit prefix in this repo's history).
