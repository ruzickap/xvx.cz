# AI Agent Guidelines

## Project Overview

Hugo static site (personal landing page at xvx.cz). No application
code -- content is in `data/links.yml`, configuration in `hugo.toml`,
theme via git submodule in `themes/`. CI enforces linting, link
checking, and security scanning via MegaLinter.

## Build / Lint / Test Commands

```bash
# Build the site (requires hugo + initialized submodule)
git submodule update --init --recursive
hugo build --minify --printPathWarnings --printI18nWarnings \
  --panicOnWarning

# Local preview
hugo server

# Markdown linting (rumdl - Rust-based markdown linter)
rumdl .         # lint all markdown
rumdl README.md # lint a single file

# Shell linting (for scripts and markdown code blocks)
shellcheck --exclude=SC2317 script.sh
shfmt --case-indent --indent 2 --space-redirects -d script.sh

# Link checking
lychee --root-dir ./public --no-progress public

# JSON linting
jsonlint --comments file.json

# GitHub Actions validation
actionlint

# Security scanning (CI only, via MegaLinter)
# checkov, DevSkim, KICS (--fail-on high), trivy (HIGH,CRITICAL)
```

There are **no unit tests or test framework**. Quality is enforced
entirely through linting, link checking, and security scanning in CI.

## Markdown Style

- Wrap lines at **80 characters**
- Use proper heading hierarchy (never skip levels)
- Include language identifiers in code fences (e.g., `bash`, `json`)
- Prefer code fences over inline code for multi-line examples
- Use semantic HTML only when necessary
- Files must pass `rumdl` checks (config: `.rumdl.toml`)
  - `CHANGELOG.md` is excluded from linting (auto-generated)
  - Code blocks are exempt from line length checks (MD013)
- Shell code blocks (`bash`, `shell`, `sh`) are extracted in CI
  and validated with `shellcheck` and `shfmt`
- All URLs must be reachable (validated by `lychee`, config:
  `lychee.toml`)

## Shell Script Style

- **Linting**: Must pass `shellcheck` (SC2317 excluded)
- **Formatting**: `shfmt --case-indent --indent 2 --space-redirects`
- **Variables**: Uppercase with braces: `${MY_VARIABLE}`
- **Conditionals**: Use `[[ ]]` (bash-specific)
- **Quoting**: Always double-quote variable expansions
- **Error handling**: Use `set -euxo pipefail` or the equivalent
  `bash -euxo pipefail {0}` shell default in workflows

## YAML Style

- Start files with `---` document separator
- Use 2-space indentation
- Add descriptive comments above configuration blocks
- Use `# keep-sorted start` / `# keep-sorted end` markers for
  lists that should remain sorted (see `.mega-linter.yml`)

## JSON Style

- Must pass `jsonlint` validation (comments allowed via `--comments`)
- `.devcontainer/devcontainer.json` is excluded from linting

## GitHub Actions Style

- **Pin actions** to full SHA commits with version comment:
  `uses: actions/checkout@de0fac2... # v6.0.2`
- **Permissions**: Set `permissions: read-all` at workflow level;
  use minimal per-job overrides
- **Shell**: Default to `bash -euxo pipefail {0}`
- **Timeouts**: Set `timeout-minutes` on every job (typically 5)
- **Runner**: Prefer `ubuntu-24.04-arm`
- **Step names**: Use emoji prefixes for visual clarity
- Validate changes with `actionlint` before committing

## Security Scanning

CI runs multiple scanners (all configured in `.mega-linter.yml`):

- **Checkov**: IaC scanner (skips `CKV_GHA_7`)
- **DevSkim**: Pattern scanner (ignores DS162092, DS137138;
  excludes `CHANGELOG.md`)
- **KICS**: Fails only on HIGH severity
- **Trivy**: HIGH/CRITICAL only, ignores unfixed vulnerabilities

## Version Control

### Commit Messages

Conventional commit format validated by `commit-check` action:

```text
<type>: <description>
```

- **Types**: `feat`, `fix`, `docs`, `chore`, `refactor`, `test`,
  `style`, `perf`, `ci`, `build`, `revert`
- Subject: imperative mood, lowercase, no period, max 72 chars
- Body (optional): explain what and why, wrap at 72 chars
- Reference issues: `Fixes #123`, `Closes #456`, `Resolves #789`

Example:

```text
docs: update link checker configuration

- Add cache exclusion for 403 responses
- Exclude private IP addresses from checks

Resolves: #42
```

### Branching

Follow [Conventional Branch](https://conventional-branch.github.io/)
format: `<type>/<description>`

- `feature/` or `feat/`: New features
- `bugfix/` or `fix/`: Bug fixes
- `hotfix/`: Urgent fixes
- `release/`: Release prep (e.g., `release/v1.2.0`)
- `chore/`: Non-code tasks (dependency updates, docs)

Use lowercase, hyphens, no consecutive/leading/trailing hyphens.
Include issue numbers when applicable: `feature/issue-123-add-page`

### Pull Requests

- Always create as **draft** initially
- Title must follow conventional commit format
  (validated by `semantic-pull-request` action)
- Include clear description of changes and motivation
- Reference related issues with `Fixes`, `Closes`, `Resolves`

## General Quality Rules

- Two spaces for indentation (no tabs)
- Pass all pre-commit hooks
- Make atomic, focused commits
- Update documentation for user-facing changes
- `CHANGELOG.md` is auto-generated -- never edit manually
- Code owner: `@ruzickap` (see `.github/CODEOWNERS`)
