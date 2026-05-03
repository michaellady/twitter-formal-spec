# Tier-4 Changelog — `twitter-formal-spec`

Tracks Tier-4 changes (UI, deploy, shadow/diff-test, proof-discharge progress) for this repo. Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

Every Tier-4 PR MUST append at least one line under the appropriate section. The `tier4-bootstrap-check` CI gate enforces this.

## [Unreleased]

### Added

- Phase 0 bootstrap: `TCB.md`, `CHANGELOG-tier4.md`, `CONTRIBUTING.md`, `.github/template-tier4-bootstrap/` template directory, and `.github/workflows/tier4-bootstrap-check.yml` CI gate.
- Repo template at `.github/template-tier4-bootstrap/` for downstream Tier-4 repos (UI, diffsplitter, etc.) to copy on first commit.

### Changed

### Deprecated

### Removed

### Fixed

### Trust-Boundary

- Initial trust surface inventoried in `TCB.md`: `conformance.jsonl` (hand-authored fixture) and the bootstrap template directory.

---

_For Tier 1–3 history (the verified spec), see git log._
