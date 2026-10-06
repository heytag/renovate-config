# renovate-config — agent instructions

Shared Renovate preset for all heytag repos, consumed via `extends: ["github>heytag/renovate-config#X.Y.Z"]`.

## Commits & branches

- Conventional Commits: `type(scope): summary` — types: `feat`, `fix`, `chore`, `docs`, `ci`, `refactor`, `test`, `perf`, `build`.
- Never commit directly to `main`. Create a feature branch first.
- No force-pushing to `main`.
- No `Co-Authored-By` or AI-attribution trailers in commit messages or PR bodies.

## Versioning & tagging

- Version is managed by release-please — do not bump `package.json` manually or create `git tag`s.
- Tags have no `v`-prefix (`include-v-in-tag: false`).

## CI runner

```yaml
runs-on: [self-hosted, heytag-ci]
```

No pre-installed tools beyond Docker. If a step needs `jq` or similar, it must be baked into the runner image — `apt-get install` in a workflow step will fail (no sudo).

## Key constraints in default.json

- `minimumReleaseAge: "3 days"` on the automerge rule — do not remove; it guards against silent vendor regressions.
- `gitSubmodules: true` — required for `.claude/conventions` submodule tracking across heytag repos.
- All version tags without `v`-prefix.

## Secrets

Never read, write, or edit `.env`, `.env.*`, `*.pem`, `*.key`, or `credentials*.json` files.

## Language

- Code, commit messages, documentation: English.
- Responses to the user: German.
