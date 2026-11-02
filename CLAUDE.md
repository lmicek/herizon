# CLAUDE.md

Guidance for Claude Code when working in this repository.

## Project overview

Herizon is a North Carolina nonprofit corporation (incorporated October 2025) that
empowers girls, women, and those who identify as female in North Carolina to achieve
independence through AI literacy, mentorship, and opportunities to build sustainable
careers. The current offering is free, one-on-one AI guidance and mentorship.

This repo contains the public website (`src/`) and internal documentation (`docs/`).

## Repository layout

```text
src/                    Public website, plain HTML + CSS (no build step)
  index.html            Single-page site
  styles.css            All styles; brand colors are CSS variables at the top
  images/               Logo, favicon, team photos
docs/                   Internal documentation (MkDocs). NOT part of the public site
mkdocs.yml              MkDocs config (Material theme); new pages must be added to `nav:`
pyproject.toml          uv project; MkDocs dependencies live in the `docs` group
uv.lock                 Locked dependencies (commit this)
.github/workflows/      static.yml deploys src/ to GitHub Pages
```

## Commands

Website: open `src/index.html` in a browser. There is nothing to build.

Docs (uses [uv](https://docs.astral.sh/uv/); run from the repo root):

```powershell
uv sync                 # create .venv and install dependencies
uv run mkdocs serve     # preview at http://127.0.0.1:8000/docs/
uv run mkdocs build     # build into site/ (gitignored); strict mode fails on warnings
```

Add dependencies with `uv add --group docs <package>`, not `pip install`.

## Deployment

Pushing to `main` runs `.github/workflows/static.yml`, which publishes only the `src/`
folder to GitHub Pages. Anything outside `src/` (including `docs/`) is not published.
`static.yml` should be the only workflow that deploys to Pages; a second Pages workflow
can overwrite the site. See `docs/deployment.md` for details.

## Website conventions

- Keep it a static, dependency-free page: no frameworks, no build tooling, no external
  scripts. Use semantic HTML, keep accessibility in mind (alt text, labels, color
  contrast, `prefers-reduced-motion`), and check layouts at desktop and phone widths.
- Colors come from the logo: purple (`--violet`), teal (`--teal`), and sun orange
  (`--coral`), plus deep plum for dark sections. Change the variables in `styles.css`
  instead of hard-coding colors.
- Public contact is `support@ontheherizon.org`. Do not put personal addresses or phone
  numbers on the site.

## Content rules

- Do not invent statistics, testimonials, partners, or outcomes. Use real ones only, and
  only with permission.
- Do not describe programs, curricula, or services that do not exist yet. Describe what
  Herizon actually does today.
- Team members are listed with their consent. Add or change people only when asked.

## Docs conventions

- Docs are Markdown in `docs/`, built with MkDocs Material. Keep `strict: true` passing.
- Each new page needs an entry under `nav:` in `mkdocs.yml`.
