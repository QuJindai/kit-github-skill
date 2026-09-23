# kit-github-skill

A [Muse](https://muse.ai) skill that talks to the GitHub REST API, plus a thin
`gh` CLI wrapper. Auth comes from a stored `custom.github` connector
(Personal Access Token) — the skill never handles the raw secret.

## CLI

```
gh GET /user
gh GET /user/repos --params '{"per_page":50,"sort":"updated"}'
gh GET /repos/OWNER/REPO/issues --params '{"state":"open"}'
gh POST /repos/OWNER/REPO/issues --json '{"title":"...","body":"..."}'
gh PATCH /repos/OWNER/REPO/issues/1 --json '{"state":"closed"}'
```

- Query strings go in `--params` (JSON object), request bodies in `--json`.
- Sends `Accept: application/vnd.github+json` and
  `X-GitHub-Api-Version: 2022-11-28` on every call.
- Only `api.github.com` is reachable.

## Layout

- `SKILL.md` — skill definition (purpose, auth, operating rules).
- `bin/gh` — the CLI. Imports `dynamic_credentials` from the skill-creator
  bundle to attach the credential surrogate as a `Bearer` token.
