# Medium-scope roadmap (future)

When the project graduates from minimal scope to medium, the following features are planned. Each lists the F/A invariants we expect to add or extend.

## Features

- **Likes** — `POST /tweets/:id/like`, `DELETE /tweets/:id/like`. Adds a `likes: int` field to tweet responses.
- **Replies** — `POST /tweets` with optional `in_reply_to: tweet_id`. Tweets form a parent/child tree.
- **User profile pages** — `GET /users/:handle` returns user metadata + their post history.

## New / extended properties

- **F11 (like idempotency)** — `Like(u,t); Like(u,t)` ≡ `Like(u,t)`; same for `Unlike`. Same flavor as F3.
- **F12 (no orphan likes)** — every like edge `(u, t)` references a known user `u` and an existing tweet `t`. Same flavor as F6/F9.
- **F13 (like count = sum of edges)** — `tweet.likes` returned by API equals the cardinality of the like-edge set for that tweet. A maintained invariant on every like/unlike.
- **F14 (reply tree well-formed)** — every `in_reply_to` references an existing tweet; no cycles (enforced by id monotonicity from F8).
- **F15 (profile = post history)** — `GET /users/:handle` returns exactly the set of tweets `t` with `t.author == handle`, sorted by F2 ordering.

## TLA+ extensions

The TLA+ module gains:
- a `likes` set of `(user, tweet_id)` pairs
- a `replies` partial function `tweet_id -> tweet_id`
- new actions `Like`, `Unlike`, `Reply`
- new invariants F11–F15

The TLC bound stays modest: 4 users, 6 tweets, 6 timestamps, max 8 likes, max 4 replies.

## Conformance suite

`conformance.jsonl` gains scenarios for:
- like idempotency
- like-then-unlike round trip
- reply chain ordering
- like count aggregation
- profile page returning post history

## Out of scope at medium

- DMs, search, retweets, notifications (those are full-scope)
- Like notifications to authors
- Reply notifications

## Migration

The minimal-scope spec module is forward-compatible: medium-scope adds new variables and actions without changing existing F1–F9. Impl repos migrate via:
1. Bump `SPEC_SHA` to the medium-spec commit
2. Implement the new endpoints + verifier contracts
3. Pass the extended conformance suite
