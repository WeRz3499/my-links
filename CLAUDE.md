# CLAUDE.md

Guidance for Claude Code (and other agents) working in this repository.

## Project overview

This is **CALITECH Links** (`links.calitech.nc`), a "link in bio" page deployed on
Cloudflare Workers, forked from [LittleLink](https://github.com/sethcottle/littlelink) —
a static, framework-free HTML/CSS link-tree alternative.

There is **no build step, no package manager, no JS framework**. The entire site is
static assets served as-is by Cloudflare Workers (`[assets]` in `wrangler.toml`).

## Structure

```
index.html        Main page — CALITECH's live link page (French content, theme-dark)
privacy.html       Privacy policy page
css/
  reset.css        Minimal CSS reset
  style.css        Layout, theme variables (auto/light/dark), avatar, typography
  brands.css        ~100+ pre-built ".button-<brand>" styles (one per service/brand)
images/
  avatar.svg/.png   Profile avatar
  icons/            Per-brand icon assets (118 files)
fonts/             Self-hosted Open Sans (woff/woff2/ttf/eot/svg) — no Google Fonts CDN
docker/            Optional Docker dev setup (Dockerfile, compose.yaml)
.do/               DigitalOcean App Platform deploy template
.github/
  workflows/contrast-check.yml   CI: WCAG contrast check, runs only on css/brands.css PRs
  FUNDING.yml, PULL_REQUEST_TEMPLATE.md
wrangler.toml      Cloudflare Workers config — serves repo root as static assets
.pre-commit-config.yaml   trailing-whitespace, end-of-file-fixer, gitleaks, talisman
```

## Key facts

- **Deployment target: Cloudflare Workers.** `wrangler.toml` serves `./` directly as
  static assets (`html_handling = "auto-trailing-slash"`). No other deploy target
  (Vercel/Netlify/DO buttons in README) is actually wired up here — only Cloudflare is.
- **`index.html` is the real, live page** — it has been customized for CALITECH
  (French IT services business in New Caledonia). Treat it as production content, not
  a template to be overwritten with upstream LittleLink defaults.
- **`index_new.html`** is leftover scaffolding from upstream LittleLink (unrelated
  "John Emerson" demo data) — not linked from anywhere, not used by `wrangler.toml`.
  Don't treat it as a second live page; confirm with the user before editing or
  deleting it.
- **Styling is additive, not component-based.** New brand buttons = add a
  `.button-<name>` block to `css/brands.css` + an `<a class="button button-<name>">`
  in `index.html`. There is no templating system.
- **Accessibility matters here.** `contrast-check.yml` CI enforces WCAG 4.5:1 contrast
  on any new/changed `.button-*` rule in `css/brands.css`, checked against both light
  and dark theme backgrounds. Add a stroke (`button-border`) when contrast fails
  rather than picking an arbitrary lighter/darker shade.
- **Secrets hygiene is enforced via pre-commit** (`gitleaks`, `talisman`) — don't
  bypass with `--no-verify`.

## Conventions when editing

- Keep `css/style.css` framework-free — no Tailwind/Bootstrap, this is intentionally
  vanilla CSS for performance (project explicitly markets 100/100 Lighthouse scores).
- Match the existing comment style in `index.html` (HTML comments explain *which line
  to change* for common customizations — avatar, theme class, title, meta tags).
- Don't add a JS bundler, npm scripts, or framework dependency without checking with
  the user first — that would be a meaningful architecture change for this repo.
- This is a fork with local customizations; don't assume upstream LittleLink
  README instructions (e.g. other deploy buttons) reflect how *this* repo is actually
  deployed (Cloudflare only, via `wrangler.toml`).
