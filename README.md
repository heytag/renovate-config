# renovate-config

Shared [Renovate](https://docs.renovatebot.com/) preset and runner workflow for the heytag org.

## Using the preset

Add to any repo's `renovate.json`:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>heytag/renovate-config#1.1.0"]
}
```

Pin to a specific release tag so updates are intentional. Renovate opens a PR to bump the pin when a new version is released.

## Adding the runner

Copy `.github/workflows/renovate.yml` into the target repo. It requires two repository secrets:

| Secret | Value |
|--------|-------|
| `RENOVATE_APP_ID` | GitHub App client ID |
| `RENOVATE_APP_PRIVATE_KEY` | GitHub App private key (`.pem` contents) |

The workflow uses a GitHub App instead of `GITHUB_TOKEN` so that Renovate PRs trigger CI checks.

## Automerge behavior

The preset automerges patch and minor updates (for safe managers) after a 3-day hold. Merging is done by Renovate itself via API — not by GitHub's native auto-merge — so it works without branch protection rules.

## Annotating pinned versions

For versions Renovate can't detect natively, add a comment above the value:

```yaml
# renovate: datasource=github-releases depName=caddyserver/caddy
CADDY_VERSION: 2.11.3
```

```dockerfile
# renovate: datasource=github-releases depName=mlocati/docker-php-extension-installer
ADD --chmod=0755 https://github.com/mlocati/docker-php-extension-installer/releases/download/2.11.12/install-php-extensions /usr/local/bin/
```

The annotation format is built into `default.json` — no local `customManagers` entry needed.
