# Contributing to `twitter-formal-spec`

This repo holds the canonical TLA+ specification consumed by the Go and Rust implementation repos via git submodule.

## Spec-first rule (cross-repo, merge-blocking)

Any new F-property MUST land in this repo's `twitter.tla` first. Both implementation repos then bump their submodule pointer and discharge the new property. Don't add F-property logic in an impl repo and back-port it later.

## Tier-4 PR checklist (merge-blocking)

Every PR labeled or scoped as Tier-4 work MUST:

- [ ] Touch `CHANGELOG-tier4.md` — at least one new line under the appropriate section
- [ ] Touch `TCB.md` if the PR expands, shrinks, or modifies the trust surface (new admin endpoint, new trusted shim, discharged proof obligation, etc.) — and add a `Trust-Boundary` line in the changelog as well
- [ ] Pass the `tier4-bootstrap-check` workflow (CI gate)

Skip rule: dependency-bump-only PRs (Renovate, Dependabot) may pass with the changelog auto-appended via the bot, no TCB.md change required.

## TLA+ workflow

Run TLC locally with the rightsized PR bound: `bash scripts/run_tlc.sh specs/twitter-pr.cfg` (from the impl repos). Full main-bound runs in CI (~4m21s).
