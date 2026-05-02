# twitter-formal-spec

Canonical formal specification for the **twitter-clone formal-verification** project. This repo is the source of truth at the **abstract / design level**: a TLA+ module describing the system's state machine and invariants, plus a JSONL conformance suite that both implementations must replay byte-identically.

Two implementation repos consume this module as a **git submodule pinned by SHA**:

- [twitter-golang-formal-verification](https://github.com/michaellady/twitter-golang-formal-verification) — verified with **Gobra**
- [twitter-rust-formal-verification](https://github.com/michaellady/twitter-rust-formal-verification) — verified with **Verus**

## What this is — and what it isn't

It **is**: an abstract TLA+ state machine + invariants, model-checked by TLC at small bounds; plus a deterministic conformance suite (request/response pairs) that both impls must replay identically.

It **isn't**: a refinement spec. The two implementations are not formally proved to refine this module. They each write their own per-language verifier contracts (Gobra `// @ requires/ensures`, Verus `requires/ensures`) that target the **same numbered F-properties** below, and they pass the same TLC bounded model check. This is "two sources of evidence pointing at the same property numbers," not a refinement proof.

See the parent plan for the full honest enumeration of what's verified vs trusted.

## Numbered properties

| | Property | Discharged by |
|---|---|---|
| **F1** | Visibility — every tweet returned to user `u`'s timeline satisfies `u` follows author or is the author; nothing visible is omitted across pages | TLA+ + per-impl service contract |
| **F2** | Ordering — every page is sorted by `(created_at desc, tweet_id desc)`; tie-breaker on tweet_id removes ambiguity | per-impl service contract (sorted-on-merge) |
| **F3** | Idempotency — `Follow(a,b);Follow(a,b)` ≡ `Follow(a,b)`; same for `Unfollow` | TLA+ (set ops) + per-impl store contract |
| **F4** | No self-follow — `Follow(a,a)` rejected | TLA+ + per-impl service contract |
| **F5** | Data-race freedom (scoped) — Go: under partial correctness via Gobra mutex specs; Rust: synchronous core via ownership + Verus exclusivity. Out of scope: deadlock-freedom, starvation-freedom, async runtime | per-impl store contract + race tests |
| **F6** | No orphan tweet authors | TLA+ + per-impl store contract |
| **F7** | Logical clock non-strict — `next() >= prev()`; ties allowed; total ordering for F2 via tweet_id tie-breaker | TLA+ (temporal property) + per-impl clock contract |
| **F8** | Tweet IDs globally unique + per-author monotonic | TLA+ + per-impl ids contract |
| **F9** | No orphan follow edges | TLA+ + per-impl store contract |

## Assumptions and non-goals (NOT verified)

- **A1** Unauthenticated — anyone can post or follow as anyone. No authorization properties claimed.
- **A2** No rate limiting / abuse defense.
- **A3** TCB enumeration — verifier, SMT solver, compiler, runtime, all unverified library code (net/http, axum, tokio, tower, vstd trusted wrappers), OS, CPU. "Verified" claims are relative to this TCB.

## Files

| File | Purpose |
|---|---|
| `twitter.tla` | TLA+ module — state machine + invariants |
| `twitter.cfg` | TLC configuration — bounded model (3 users, 5 tweets, 5 timestamps with ties allowed) |
| `twitter-pr.cfg` | Smaller TLC bound for PR-time runs (3 users, 4 tweets, 4 timestamps) |
| `conformance.jsonl` | Deterministic request/response pairs both impls must replay byte-identically |
| `roadmap_medium.md` | Future scope: likes, replies, profiles |
| `roadmap_full.md` | Future scope: search, retweets, notifications |

## Running TLC locally

Requires Java 11+ and the `tla2tools.jar` (from [Lamport's TLA+ tools](https://lamport.azurewebsites.net/tla/tools.html)).

```bash
# Full bound (main-branch and nightly enforcement)
java -cp tla2tools.jar tlc2.TLC -config twitter.cfg twitter.tla

# PR bound (faster feedback)
java -cp tla2tools.jar tlc2.TLC -config twitter-pr.cfg twitter.tla
```

Both configs assert identical F-property invariants; the smaller one is for PR feedback (≤30 sec on a 4-core runner), the larger for main-branch and nightly enforcement.

In CI, both impl repos run TLC against this module via the submodule path.

## Consuming as a submodule

```bash
git submodule add https://github.com/michaellady/twitter-formal-spec.git specs
git -C specs rev-parse HEAD > SPEC_SHA
git add SPEC_SHA .gitmodules specs
git commit -m "Pin twitter-formal-spec submodule"
```

CI in each impl repo runs:

```bash
test "$(cat SPEC_SHA)" = "$(git -C specs rev-parse HEAD)" || exit 1
```

Bumping the spec is a deliberate action: update `SPEC_SHA`, re-run the conformance suite, re-run TLC, open a PR.

## License

MIT — see `LICENSE`.
