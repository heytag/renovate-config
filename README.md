# renovate-config

Self-hosted Renovate runner and shared dependency update presets for the heytag org.

## What's in this repo

| File | Purpose |
|------|---------|
| `.github/workflows/renovate.yml` | Renovate runner workflow — copy into any repo |
| `default.json` | Shared Renovate preset — extend from any `renovate.json` |

### Why `default.json` and not `renovate.json`?

`renovate.json` is the Renovate configuration *for this repo itself* (how Renovate updates its own dependencies). `default.json` is Renovate's naming convention for **shared presets** — it's the exact filename Renovate looks for when another repo uses `"extends": ["github>heytag/renovate-config"]`.

### How does `"extends": ["github>heytag/renovate-config"]` work?

When Renovate scans a target repo (e.g. `caddy-geoheaders`) and finds `"extends": ["github>heytag/renovate-config"]` in its `renovate.json`, it:

1. Fetches `heytag/renovate-config/default.json` via the GitHub API (using the App token — works with private repos)
2. Merges the preset config as the base
3. Applies any rules from the target repo's `renovate.json` on top as overrides

The format is `github>owner/repo` for the default preset (`default.json`). Named presets are also possible: `github>heytag/renovate-config:terraform` would load `terraform.json`.

---

## Why GitHub App instead of a PAT?

| | PAT (Personal Access Token) | GitHub App |
|---|---|---|
| Token lifetime | Months to unlimited | ~1 hour (auto-rotating) |
| Bound to | Personal user account | Dedicated app identity |
| Security risk | High (account compromise = all repos affected) | Low (token is worthless after the run) |
| CI triggered on Renovate PRs | **No** | **Yes** |
| Expires | Yes (fine-grained: max. 1 year) | No |

---

## One-time Setup: Create the GitHub App

This step is only needed once. The same App works across all org repos.

### 1. Create the GitHub App

Go to: **GitHub.com → Settings → Developer settings → GitHub Apps → New GitHub App**

| Field | Value |
|-------|-------|
| GitHub App name | e.g. `Heytag Renovate Bot` |
| Homepage URL | Anything, e.g. your GitHub profile |
| Webhook | Disable (uncheck the `Active` checkbox) |

**Repository permissions:**

| Permission | Level | Why Renovate needs it |
|------------|-------|-----------------------|
| Checks | Read-only | Read CI check results to determine if automerge is safe |
| Commit statuses | Read-only | Read legacy commit status results (used alongside Checks) |
| Contents | Read & Write | Clone the repo, push update branches |
| Issues | Read & Write | Create and update the Dependency Dashboard issue |
| Metadata | Read-only | Search repos, read repository metadata (mandatory) |
| Pull requests | Read & Write | Open, update, and automerge dependency PRs |
| Workflows | Read & Write | Update GitHub Actions workflow files (e.g. `actions/checkout@v7 → v8`) |

> **Note:** Missing `Checks` or `Commit statuses` causes `integration-unauthorized` errors when Renovate tries to read CI status after opening a PR. Both must be granted and accepted in the org installation.

Select **Only on this account**, then click **Create GitHub App**.

### 2. Note the App ID

The **App ID** appears at the top of the App settings page (a number). Save it.

### 3. Generate a private key

Scroll down → **Generate a private key** → save the downloaded `.pem` file securely.

### 4. Install the App on the organization

App page → **Install App** → select the heytag org → **All repositories** (or select specific ones).

> **Important:** After creating or updating App permissions, go to
> **github.com/organizations/heytag/settings/installations** → your App → **Review request**
> to accept the updated permissions. This is a separate step from editing the App definition.

### 5. Add org-level secrets

Instead of adding secrets to each repo individually, store them once as org secrets:

**github.com/organizations/heytag/settings/secrets/actions → New organization secret**

| Secret name | Value | Access |
|-------------|-------|--------|
| `RENOVATE_APP_ID` | The App ID (number) | All repositories |
| `RENOVATE_APP_PRIVATE_KEY` | Full `.pem` file contents | All repositories |

Once set, every repo in the org can use `${{ secrets.RENOVATE_APP_ID }}` and `${{ secrets.RENOVATE_APP_PRIVATE_KEY }}` without any per-repo secret configuration.

---

## Adding Renovate to a Repository

With org-level secrets in place, only two steps are needed per repo.

### Step 1 — Add the workflow

Copy `.github/workflows/renovate.yml` from this repo into the target repo unchanged.

### Step 2 — Add `renovate.json`

Place a `renovate.json` in the repo root. Minimal version that extends the shared preset:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>heytag/renovate-config"]
}
```

Or extend and override for repo-specific rules:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>heytag/renovate-config"],
  "packageRules": [
    {
      "groupName": "Caddy",
      "matchPackageNames": ["caddyserver/caddy", "github.com/caddyserver/caddy/v2"],
      "automerge": false,
      "labels": ["dependencies", "caddy"]
    }
  ]
}
```

---

## `renovate.json` Examples by Stack

### Go module + GitHub Actions + pinned env var versions

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>heytag/renovate-config"],
  "customManagers": [
    {
      "customType": "regex",
      "description": "Update versioned env vars annotated with renovate comments in workflow files",
      "managerFilePatterns": ["/.github/workflows/.*\\.yml$/"],
      "matchStrings": [
        "# renovate: datasource=(?<datasource>[^\\s]+) depName=(?<depName>[^\\s]+)\\s+\\w+:\\s*(?<currentValue>[^\\s]+)"
      ],
      "extractVersionTemplate": "^v?(?<version>.+)$"
    }
  ]
}
```

Annotate pinned versions in workflows like this:

```yaml
env:
  # renovate: datasource=golang-version depName=go
  GO_VERSION: 1.26.4
  # renovate: datasource=github-releases depName=caddyserver/xcaddy
  XCADDY_VERSION: 0.4.5
  # renovate: datasource=github-releases depName=caddyserver/caddy
  CADDY_VERSION: 2.11.3
```

The same manager also covers `action.yml`/`action.yaml` files anywhere in the repo, for pinning a tool version an action's `default:` input falls back to when the action itself doesn't keep it current (e.g. `aquasecurity/trivy-action` bundling a stale Trivy CLI default):

```yaml
inputs:
  trivy-version:
    # renovate: datasource=github-releases depName=aquasecurity/trivy
    default: v0.72.0
```

Leave the value unquoted - a quoted `'v0.72.0'` gets captured with the quotes included, breaking the version match.

### Dockerfile pinned release downloads

Unlike the workflow-file example above, this custom manager is already built into `default.json` — no local `customManagers` entry needed. Just annotate the `ADD` line in any `Dockerfile*`:

```dockerfile
# renovate: datasource=github-releases depName=mlocati/docker-php-extension-installer
ADD --chmod=0755 https://github.com/mlocati/docker-php-extension-installer/releases/download/2.11.12/install-php-extensions /usr/local/bin/
```

Renovate extracts the version from the `/releases/download/<version>/` path segment and opens a PR when a newer GitHub release is published.

### Terraform / OpenTofu

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>heytag/renovate-config"],
  "packageRules": [
    {
      "matchManagers": ["terraform"],
      "matchUpdateTypes": ["major"],
      "automerge": false,
      "labels": ["dependencies", "manual-review"]
    }
  ]
}
```

**Common datasources for annotation comments:**

| Tool | datasource | depName |
|------|------------|---------|
| Go version | `golang-version` | `go` |
| GitHub release | `github-releases` | `owner/repo` |
| Docker image | `docker` | `image-name` |
| Terraform provider | `terraform-provider` | `hashicorp/aws` |

---

## Grouping Related Packages

When two entries must always stay in sync (e.g. Caddy binary version and its `go.mod` entry), group them into a single PR:

```json
{
  "groupName": "Caddy",
  "matchPackageNames": ["caddyserver/caddy", "github.com/caddyserver/caddy/v2"],
  "automerge": false,
  "labels": ["dependencies", "caddy"]
}
```

---

## Vulnerability Alerts

`osvVulnerabilityAlerts` cross-checks every dependency in every managed repo against the [OSV.dev](https://osv.dev) database — no extra GitHub App permission needed, since it doesn't use GitHub's own Dependabot alerts API. When a dependency has a known vulnerability, Renovate opens a fix PR **immediately**, labeled `security`, bypassing the normal `before 7am on monday` schedule (`"schedule": ["at any time"]`).

---

## Dependency Dashboard

Renovate creates a **Dependency Dashboard** GitHub Issue in each managed repo. It shows all pending and scheduled updates.

To trigger a PR immediately (without waiting for the schedule): click a checkbox in the Dashboard issue. The `issues: edited` workflow trigger fires automatically within ~1 minute.

---

## Troubleshooting

**`integration-unauthorized` on PR creation:**
The GitHub App is missing `Checks: Read` and/or `Commit statuses: Read` permissions. Add both in the App settings, then accept the updated permissions at **github.com/organizations/heytag/settings/installations**.

**Checkbox click in Dependency Dashboard doesn't trigger Renovate:**
The workflow must have the `issues: types: [edited]` trigger. See the workflow template in this repo.

**Renovate PRs don't trigger CI builds:**
Ensure the GitHub App (not `GITHUB_TOKEN`) is used. PRs opened by `GITHUB_TOKEN` don't trigger workflows; PRs opened by a GitHub App do.

**`Resource not accessible by integration` error:**
Check App permissions — especially `Contents: Write` and `Workflows: Write`.

**Renovate doesn't detect versions in workflow files:**
Verify the Custom Manager config and annotation format. Add `LOG_LEVEL: debug` to the workflow env temporarily for detailed extraction logs.

**Private key invalid:**
Store the full `.pem` content including header/footer as the secret. Do not remove line breaks.

**`app-id` input not recognized:**
`actions/create-github-app-token@v3+` renamed `app-id` to `client-id`. Use `client-id` with v3 and later.
