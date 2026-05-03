<!-- Paste this section into the repo's CONTRIBUTING.md, or use this file as the seed if no CONTRIBUTING.md exists yet. -->

## Tier-4 PR checklist (merge-blocking)

Every PR labeled or scoped as Tier-4 work MUST:

- [ ] Touch `CHANGELOG-tier4.md` — at least one new line under the appropriate section
- [ ] Touch `TCB.md` if the PR expands, shrinks, or modifies the trust surface (new admin endpoint, new trusted shim, discharged proof obligation, etc.) — `Trust-Boundary` section in the changelog as well
- [ ] Pass the `tier4-bootstrap-check` workflow (CI gate)

Skip rule: dependency-bump-only PRs (Renovate, Dependabot) may pass with the changelog auto-appended via the bot, no TCB.md change required.
