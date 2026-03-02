# verity-org/.github

Shared GitHub Actions for the [verity-org](https://github.com/verity-org) ecosystem.
Centralises the boilerplate that every repo in the org repeats: runner hardening,
tool installation, Go caching, and linting — so changes propagate everywhere from
a single commit.

---

## Contents

| Path | Type | Purpose |
|------|------|---------|
| [`actions/setup`](#setup-action) | Composite action | Harden runner · install mise · cache Go modules |
| [`.github/workflows/lint.yaml`](#lint-workflow) | Reusable workflow | Go lint · static analysis · optional Node lint |

---

## Setup action

> `verity-org/.github/actions/setup@main`

Combines the three steps that appear at the top of every job:

1. **Harden runner** — `step-security/harden-runner` with `egress-policy: audit`
2. **Install mise** — `step-security/mise-action` with install + cache enabled
3. **Cache Go modules** — `~/dev/go/pkg/mod` + `~/.cache/go-build`, keyed on `go.sum` *(opt-in)*

### Inputs

| Input | Default | Description |
|-------|---------|-------------|
| `go-cache` | `'false'` | Set `'true'` to restore/save the Go module cache |

### Usage

```yaml
# Minimal — just harden + mise
- uses: verity-org/.github/actions/setup@main

# With Go module cache (build / lint jobs that invoke go tooling)
- uses: verity-org/.github/actions/setup@main
  with:
    go-cache: 'true'
```

---

## Lint workflow

> `verity-org/.github/.github/workflows/lint.yaml@main`

Three parallel jobs triggered via `workflow_call`:

| Job | Tools |
|-----|-------|
| **Go Lint** | `golangci-lint` · `govulncheck` |
| **Static Analysis** | `actionlint` · `yamllint` · `shellcheck` · `markdownlint` |
| **Node Lint** *(opt-in)* | `npm ci` · `prettier` · `eslint` (runs in `./site`) |

Both the ShellCheck and markdownlint steps are no-op safe — they skip gracefully if
no scripts or markdown files exist.

### Inputs

| Input | Type | Default | Description |
|-------|------|---------|-------------|
| `node-lint` | `boolean` | `false` | Enable the Node Lint job (requires a `./site` directory) |

### Usage

```yaml
# lint.yaml — repo with a Node/Astro site
name: Lint
on:
  pull_request:
    branches: [main]
  push:
    branches: [main]
permissions:
  contents: read
  pull-requests: read
jobs:
  lint:
    uses: verity-org/.github/.github/workflows/lint.yaml@main
    with:
      node-lint: true
```

```yaml
# lint.yaml — Go-only repo
name: Lint
on:
  pull_request:
    branches: [main]
  push:
    branches: [main]
permissions:
  contents: read
  pull-requests: read
jobs:
  lint:
    uses: verity-org/.github/.github/workflows/lint.yaml@main
```

---

## Versioning

All actions and workflows are pinned to `@main`. Callers inherit changes
automatically. When breaking changes are necessary, a migration note will be
added to this file before merging.

Action SHAs pinned in this repo:

| Action | SHA | Version |
|--------|-----|---------|
| `step-security/harden-runner` | `a90bcbc6539c36a85cdfeb73f7e2f433735f215b` | v2.15.0 |
| `step-security/mise-action` | `d65cf9dcb78b983ae2530c89fd28e72286713505` | v3.5.1 |
| `actions/cache` | `cdf6c1fa76f9f475f3d7449005a359c84ca0f306` | v5.0.3 |
| `actions/checkout` | `de0fac2e4500dabe0009e67214ff5f5447ce83dd` | v6.0.2 |
