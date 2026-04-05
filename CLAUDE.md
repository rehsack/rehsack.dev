# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Personal technical blog and project showcase for Jens Rehsack at https://rehsack.dev.
Jekyll 4.4 with Minimal Mistakes (remote theme, skin "air") and jekyll-asciidoc.

## Content Policy

- **AsciiDoc first**: all content files (_pages, _posts, _projects) must be `.adoc`. Markdown is only acceptable where AsciiDoc is technically unsupported (e.g. `index.md` as a pure layout stub).
- No client names in any public content (about, posts, projects). Industries and technologies are fine.
- **License**: Posts default to **CC BY-NC-SA 4.0** (set in `_config.yml` defaults). Override per-post with `license: "CC BY-NC-ND 4.0"` in front matter when needed. The About/profile page uses `license: "CC BY-NC-ND 4.0"` — sharing with attribution is fine, but no derivatives or commercial use. Projects have no license set yet — add `license:` to individual project front matter when they get fleshed out. The license is rendered as a notice at the bottom of each page/post via `assets/js/post-license.js` and exposed as `<meta name="license">` for machines. Supported values: `CC BY-NC-SA 4.0`, `CC BY-NC-ND 4.0`, `All rights reserved` (or any custom string — non-CC values render as plain text).
- **Share buttons**: Explicitly chosen per post via `share_on:` array in front matter. No default — every post requires a conscious decision. Available platforms: `linkedin`, `mastodon`, `hackernews`, `reddit`, `facebook`, `bluesky`. The custom `_includes/social-share.html` overrides the theme's default. Mastodon sharing prompts the reader for their instance (remembered in localStorage). After 10-20 posts, consider switching to category-based defaults (Option C).

## Development

The site runs inside a devcontainer based on `tpo42/adoc:latest` (Ruby 3.x, Bundler 4.x, full Asciidoctor toolchain). No local Ruby installation needed.

```bash
# Start the devcontainer (requires Docker + devcontainer CLI)
devcontainer up --workspace-folder .

# Serve locally (port 4000 mapped via appPort)
devcontainer exec --workspace-folder . bundle exec jekyll serve --host 0.0.0.0 --livereload

# Validate AsciiDoc files from the host (no devcontainer needed)
adcw validate -i _pages/about.adoc
```

After changing `.devcontainer/devcontainer.json`, remove the old container before `devcontainer up`:
```bash
docker rm -f <container-id>
```

## Build & Deploy

GitHub Actions (`.github/workflows/publish.yml`) builds and deploys to GitHub Pages on push to main/master. The workflow uses Ruby 3.2 and `bundle exec jekyll build`.

## Pitfall: Jekyll has no content whitelist

Jekyll renders **every** processable file (`.md`, `.adoc`, `.html`) in the repository root unless it is explicitly listed in `exclude:` in `_config.yml`. There is no include-only / whitelist mode. The `include:` directive only overrides Jekyll's *default* excludes (e.g. `_`-prefixed directories) — it is not a whitelist.

**When adding files to the repo root** (README.md, CLAUDE.md, docker-compose.yml, etc.), always check whether they need to be added to `exclude:` in `_config.yml` to prevent them from being deployed as pages.

## Cross-linking with rehsack.de

Sister site: https://rehsack.de (German, personal blog). Both sites link to each other via author/footer links in `_config.yml`.

**Translation workflow** — most posts appear on both sites. The canonical version is German (rehsack.de); the English version here is a translation.

1. Create a branch in **both** repos (`rehsack.de` and `rehsack.dev`)
2. Write the German post in `rehsack.de/_posts/`
3. Translate to English and place in `rehsack.dev/_posts/` (same filename/date)
4. Add `translation_url:` to the front matter of **both** posts, pointing to the other site's URL
5. Commit and push both repos, merge both PRs

The `translation_url` front matter variable triggers:
- a `<link rel="alternate" hreflang="…">` tag (SEO)
- a flag icon (🇩🇪 / 🇬🇧) appended to the post title via `assets/js/translation-link.js`, linking to the counterpart

Posts without `translation_url` render normally without any flag.

## Architecture

- **Theme**: Minimal Mistakes via `jekyll-remote-theme` (no local theme files)
- **Collections**: `_projects/` with `output: true` — listed on `/projects/` via `layout: collection`
- **Navigation**: `_data/navigation.yml` — Blog, Projects, About, Imprint
- **Plugins**: jekyll-remote-theme, jekyll-asciidoc, jekyll-sitemap, jekyll-feed, jekyll-include-cache
- **AsciiDoc config**: source-highlighter set to rouge in `_config.yml` under `asciidoc.attributes`
- **Devcontainer**: `tpo42/adoc:latest` image, gems installed to `vendor/bundle` via `postCreateCommand`, `appPort` for CLI port mapping, `forwardPorts` for VS Code

## Git

Always use `--signoff` on commits.
