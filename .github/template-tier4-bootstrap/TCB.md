# Trusted Computing Base (TCB) — `<repo-name>`

This file enumerates everything in this repo that is **trusted but not formally verified**. Every Tier-4 PR that expands the trust surface MUST add a row here. The narrower this list, the stronger the project's verification claim.

| File | Item | Why trusted | Validated by |
|---|---|---|---|
| _example: src/foo.rs:42_ | _function `foo`_ | _Calls into `std::sync::Mutex::lock` which is not lifted to a vstd shim yet_ | _runtime behavior matches spec; covered by stream 2 diff-test_ |

## Trust surface categories

- **Verifier escape hatches:** `// @ trusted` (Gobra), `#[verifier::external_body]` (Verus), `assume` clauses
- **Stdlib stubs:** `stubs/{errors,sort,sync}/` (Gobra) or vstd Mutex/HashMap shims (Verus)
- **IO boundary:** HTTP shim, JSON marshaling, network calls
- **Deploy stack:** Dockerfile, deploy workflow, registry credentials
- **Admin endpoints:** `/_admin/*`, `/version`, snapshot endpoints
- **UI:** all of the UI code (the verified core has no UI)
- **Observability:** /metrics, structured logger

## How rows get removed

A row is removed when the underlying item moves into the verified core (e.g., a `// @ trusted` annotation is dropped because the proof obligation is now discharged). The PR that removes the row updates `CHANGELOG-tier4.md` under `Trust-Boundary` with a brief note.
