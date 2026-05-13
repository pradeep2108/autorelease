# autorelease

Automated semantic versioning and release notification pipeline for a SaaS product with a **Go backend** and **Svelte frontend**, hosted on GitHub.

---

## How It Works

Every time a PR is merged to `main`, the release process is fully automated:

1. **release-please** detects conventional commits and opens a Release PR
2. Team reviews and merges the Release PR
3. A Git tag is created (`backend-vX.Y.Z` or `frontend-vX.Y.Z`)
4. Deploy workflows trigger on the tag
5. Customer email notification is sent via Loops/Resend
6. In-app "What's New" banner updates automatically

---

## Branch Strategy

```
feature/* → dev → main
```

- All feature work targets `dev`
- `dev → main` merges trigger release-please
- Direct commits to `main` are not allowed

---

## Versioning

Backend and frontend are versioned **independently** using [Conventional Commits](https://www.conventionalcommits.org/).

| Commit type | Version bump |
|---|---|
| `feat:` | minor (1.**x**.0) |
| `fix:` | patch (1.x.**x**) |
| `feat!:` / `BREAKING CHANGE` | major (**x**.0.0) |
| `chore:`, `docs:`, `test:` | none |

### Examples

```
feat(auth): add OAuth2 login
fix(api): correct null pointer dereference
feat!: redesign payment flow
```

---

## Repository Structure

```
repo/
├── backend/                    # Go
│   ├── version.go
│   └── CHANGELOG.md
├── frontend/                   # Svelte
│   ├── package.json
│   ├── vite.config.js
│   ├── src/lib/WhatsNew.svelte
│   └── CHANGELOG.md
└── .github/
    └── workflows/
        ├── release.yml         # release-please
        ├── deploy-backend.yml  # triggers on backend-v* tags
        ├── deploy-frontend.yml # triggers on frontend-v* tags
        ├── notify-release.yml  # customer email on release publish
        └── pr-lint.yml         # enforce conventional commit PR titles
```

---

## Release Notification Flow

```
PR merged to main
      ↓
release-please opens Release PR
      ↓
Release PR merged → GitHub Release created
      ↓
notify-release.yml fires
      ↓
Customer email (Loops/Resend) — feat: entries only
      ↓
In-app "What's New" banner (fetches GitHub Releases API)
```

---

## Setup

### Secrets required in GitHub repository settings

| Secret | Description |
|---|---|
| `LOOPS_API_KEY` | Loops (or Resend) API key for customer emails |
| `RELEASE_PLEASE_TOKEN` | GitHub PAT with repo write access for release-please |

### Tools

| Tool | Purpose |
|---|---|
| [release-please](https://github.com/googleapis/release-please) | Automates version bumps, Release PRs, GitHub Releases |
| [action-semantic-pull-request](https://github.com/amannn/action-semantic-pull-request) | Enforces conventional commit PR titles |
| Loops / Resend | Customer email delivery |
| GitHub Releases API | Source of truth for in-app banner |
