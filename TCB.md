# Trusted Computing Base (TCB) — `twitter-formal-spec`

This file enumerates everything in this repo that is **trusted but not formally verified**. Every Tier-4 PR that expands the trust surface MUST add a row here.

The spec repo's trust surface is intentionally narrow: it contains the TLA+ module and TLC configs, which ARE the source of truth (not "trusted"), plus the conformance test fixtures. Tier-4 work here is mostly tracking discipline.

| File | Item | Why trusted | Validated by |
|---|---|---|---|
| `conformance.jsonl` | the conformance test sequence itself | hand-authored from the spec; if it diverges from `twitter.tla`, every impl fails | manual review on every conformance.jsonl change; impl repos run it on every PR |
| `.github/template-tier4-bootstrap/` | repo bootstrap template | seed for downstream Tier-4 repos; copied verbatim on first commit of each new repo | template is checked into git; downstream consumers diff against it |

Note: `twitter.tla` and `twitter*.cfg` are NOT in the TCB — they are the verification target / source of truth.

## Trust surface categories

- **Spec source of truth:** `twitter.tla`, `twitter.cfg`, `twitter-pr.cfg` — checked by TLC, not trusted
- **Conformance fixtures:** `conformance.jsonl` — trusted; replayed by both impls
- **Tracking templates:** `.github/template-tier4-bootstrap/`

## How rows get removed

A row is removed when the underlying item moves into the verified core (e.g., a conformance scenario gets generated from the TLA spec rather than hand-authored). The PR removing the row updates `CHANGELOG-tier4.md` under `Trust-Boundary`.
