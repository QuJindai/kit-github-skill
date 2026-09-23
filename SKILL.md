---
name: "github"
description: "Use Github when the user asks for Github or this provider's API."
---

# Github

## Purpose
Use Github with the user-connected `custom.github` credential.

## Tooling
`~/workspace/skills/github/bin/gh` — thin CLI over the GitHub REST API:

```
gh GET /user
gh GET /user/repos --params '{"per_page":50,"sort":"updated"}'
gh GET /repos/OWNER/REPO/issues --params '{"state":"open"}'
gh POST /repos/OWNER/REPO/issues --json '{"title":"...","body":"..."}'
gh PATCH /repos/OWNER/REPO/issues/1 --json '{"state":"closed"}'
```

It attaches the stored credential as a Bearer token and sets the required
`Accept` / `X-GitHub-Api-Version` headers. Query strings go in `--params`
(JSON object); request bodies go in `--json` (JSON string). Only
api.github.com is reachable.

Python CLIs must import `/opt/hatch/skills/skill-creator/bin/dynamic_credentials.py` and call `add_surrogate_to_request(...)`, `url_with_surrogate_query_param(...)`, or `url_with_surrogate_path_segment(...)` before authenticated requests, matching where the provider reads the key. If they use `urllib`, read JSON responses with `read_json_response(resp)` from the same helper instead of calling `resp.read()` directly. They must send only `hsurr:*` values, and only to the hosts below.

## Auth
The credential is already stored; nothing here collects one. Never ask the user to paste a raw key in chat, set a secret environment variable, pass a secret flag, or write an auth file.

A 401 or 403 is a question about the request before it is a question about the key. Check that the credential was attached at all: a request built without the helpers named under Tooling carries nothing, and that looks exactly like a wrong or under-scoped token. Only once a request that did carry the credential is still rejected, call `credentials.request_api_access` with `reconnect` to replace it. The connector is stored as `custom.github`.

## Operating Rules
1. Use this skill when the user asks for Github or this provider's API.
2. Restrict authenticated requests to: api.github.com.
3. Do not print, log, or persist raw credentials.
4. If auth is missing or rejected, follow the Auth section rather than asking for a key.
