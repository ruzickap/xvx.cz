# AGENTS.md

Guidance for agents working in this repo. Repo-specific gotchas only; assume
standard knowledge otherwise.

## What this is

Hugo static site for the personal landing page <https://xvx.cz>. It is a thin,
data-driven start page: there is **no `content/` or `layouts/` directory**. The
visible links come from `data/links.yml`, and look-and-feel comes entirely from
the theme. `hugo.toml` is the only site config.

## Critical: the theme is a git submodule

`themes/bootstrap-bp-hugo-startpage` is a git submodule and is often **not
checked out** (a fresh/automation clone leaves it empty). `hugo` will fail or
render a blank site until you initialize it:

```bash
git submodule update --init --recursive
```

Do not commit edits to files inside `themes/` — they belong to the upstream
submodule repo.

## Build & preview

```bash
hugo server   # local preview at http://localhost:1313/
hugo --minify # production build into public/ (gitignored)
```

CI builds with stricter flags that **fail on any warning** — match these before
assuming a change is clean:

```bash
hugo build --minify --printPathWarnings --printI18nWarnings --panicOnWarning
```

There is no test suite; `public/`, `resources/_gen/`, and
`.pre-commit-config.yaml` are gitignored.

## Linting (MegaLinter)

All linting runs through **MegaLinter** (`documentation` flavor), configured in
`.mega-linter.yml`. It only runs on push to **non-`main`** branches. Notable
points an agent will otherwise get wrong:

- Markdown is linted by **`rumdl`** (config `.rumdl.toml`), not markdownlint.
- Links are checked by **`lychee`** (config `lychee.toml`), not
  markdown-link-check.
- **Shell code blocks inside Markdown are extracted and shellcheck-validated.**
  Any ```bash`/`shell`/`sh` fence must be valid, runnable shell.
- `CHANGELOG.md` is excluded from every linter — never hand-edit it (see below).
- Active scanners: checkov, DevSkim, Trivy, plus tflint/Terraform linters that
  exist in config but currently have no Terraform files to act on.

## Release & changelog

Releases are automated by **release-please** (`release-type: simple`) on merge
to `main`. `CHANGELOG.md` and version bumps are generated from conventional
commit messages — do not edit `CHANGELOG.md` by hand.

## Deploy flow

`gh-pages-build` workflow runs on changes to `content/`, `data/`, `hugo.toml`,
`layouts/`, `static/`, or `themes/`:

- push to `main` → deploy to **GitHub Pages** (then link-checked with lychee).
- push to any other branch → deploy a **Cloudflare Pages** preview.

## Conventions

- Commits: Conventional Commits, subject ≤ 72 chars, validated by
  `commit-check`. Branches follow Conventional Branch (`feature/`, `fix/`,
  `chore/`, etc.). PR titles are validated by `semantic-pull-request`; open PRs
  as drafts.
- GitHub Actions: pin actions to full commit SHAs, use minimal permissions, and
  validate workflow edits with `actionlint`.
- `keep-sorted` markers are used in several config files (`.gitignore`,
  `.mega-linter.yml`) — preserve the sort order when editing those blocks.
