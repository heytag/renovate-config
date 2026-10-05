@.claude/conventions/template/base.md

# renovate-config

Shared Renovate preset for all heytag repos, consumed via `extends: ["github>heytag/renovate-config#X.Y.Z"]`.

## Versioning

Versioning is handled automatically by release-please:
- Bump `version` in `package.json` only via a Conventional Commits PR — release-please reads commit history and opens a release PR.
- Tags have no `v`-prefix (`include-v-in-tag: false`) — the custom regex manager in `default.json` expects `#1.2.0`, not `#v1.2.0`.
- Never create tags manually (`git tag`). The `release-please.yml` workflow tags automatically once a release PR merges.

## Making changes

`default.json` is the single source of truth for all heytag Renovate presets. Edit it directly — no sub-files.

When adding or modifying a packageRule, test the logic at [https://docs.renovatebot.com/module/config/](https://docs.renovatebot.com/module/config/) before committing.

## Key rules in default.json

- `minimumReleaseAge: "3 days"` on the automerge rule — intentional safety buffer after a silent Grafana regression (13.0.2→13.2.2) broke Prometheus-backed alert rules for ~1 week.
- `gitSubmodules: true` — keeps `.claude/conventions` submodules current across heytag repos.
- Tags without `v`-prefix everywhere.
