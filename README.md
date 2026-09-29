# rsquared-quarto-theme

Reusable Quarto book template for Rsquared Academy ebooks. Extracted from the [bash-intro](https://github.com/rsquaredacademy-education/bash-intro) pilot migration (Bookdown → Quarto + Netlify CI/CD).

## Use for a new migration

1. Copy `_quarto.yml`, `.github/workflows/deploy.yml`, `scripts/`, `netlify.toml`, `404.html`, `robots.txt`, `.gitignore` into the book repo.
2. Replace all `__PLACEHOLDERS__` (`__BOOK_TITLE__`, `__AUTHOR__`, `__SUBDOMAIN__`, `__REPO__`, `__COVER__`).
3. Rename `.Rmd` → canonical `.qmd` slugs (filename == output URL slug; order via `chapters:`).
4. Replace Bookdown `\@ref()` with Quarto `@sec-` anchors or plain `.qmd` links (`.qmd` links are Typst-safe on 1.6).
5. Convert `kableExtra` tables → markdown tables with `tbl-colwidths`; replace callouts with blockquotes (callouts break Typst on 1.6); replace `knitr::include_graphics()` with `![](){#fig-…}`.
6. Package chapter fixtures into `seed/<dir>-seed.tar.gz`; add the working dir to `.gitignore`; adapt `scripts/reset-fixture.R`.
7. Scope `knitr: opts_knit: root.dir` via frontmatter **only** in fixture-consuming chapters; pin `engine: knitr` in every bash-only `.qmd` (Quarto defaults those to Jupyter).
8. Port legacy redirects into root `netlify.toml`; set `RULE_COUNT` if `test-redirects.sh` needs it.
9. Add repo secrets `NETLIFY_SITE_ID` + `NETLIFY_AUTH_TOKEN`; in Netlify UI set **Build status → Stopped builds** (Actions pushes the artifact via API).
10. Fix CI-discovered issues in the book: install other missing runner packages (`bsdmainutils` precedent), bot-blocked/dead external links, Libertinus font fallback in PDF.

## Pinned versions

`ubuntu-24.04`, R `4.4.2`, Quarto `1.6.40`, Typst via `typst-community/setup-typst@v4`, deploy via `nwtgck/actions-netlify@v3`. R packages via `renv` (keep `rmarkdown` — Quarto drives knitr through it).

## Gotchas (learned in the pilot)

- `quarto render --to typst` is a no-op for books; use `--to pdf` with `pdf-engine: typst`.
- Every `quarto render --to <fmt>` wipes `output-dir` → stage each format to its own `--output-dir` and assemble.
- `quarto-required` and `sitemap` are top-level keys; `sitemap: true` may emit nothing locally — verify on production.
- GH runner font packages: `fonts-linuxlibertine` (no `fonts-libertine`); no `fonts-libertinus` on Noble.
- `cal` is missing on runners → `bsdmainutils`.
- lychee: use `--root-dir docs`; bot-blocked URLs (403) may need removal.
