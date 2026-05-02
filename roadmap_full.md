# Full-scope roadmap (future)

After medium-scope lands, full-scope adds:

## Features

- **Retweets** — `POST /tweets/:id/retweet`. Retweets appear in followers' timelines like original tweets.
- **Search** — `GET /search?q=...&user=...`. Returns tweets matching a substring query, scoped to tweets visible to the searching user.
- **Notifications** — `GET /notifications?user=...`. Returns events: new follower, like on your tweet, reply to your tweet, retweet of your tweet.

## New / extended properties

- **F16 (retweet visibility)** — a retweet by `r` of `t` appears on `u`'s timeline iff (`u` follows `r` or `u == r`). Original tweet is *not* duplicated; the timeline entry references the retweeted tweet by id and credits the retweeter.
- **F17 (retweet idempotency)** — `Retweet(r, t); Retweet(r, t)` ≡ `Retweet(r, t)`. Same flavor as F3.
- **F18 (search soundness)** — every result returned by `/search?q=X&user=u` (a) is visible to `u` per F1, and (b) contains `X` as a substring of `text`.
- **F19 (search completeness)** — every tweet `t` visible to `u` whose `text` contains `X` as a substring is in the result set or in a subsequent page (handled per F1's pagination clause).
- **F20 (notification soundness)** — every notification returned to `u` corresponds to an event whose target was `u` (a follow event with `u` as followee, a like on a tweet authored by `u`, etc.).
- **F21 (notification completeness)** — every event whose target was `u` produces exactly one notification for `u` (F3-style at-least-once + F8-style at-most-once together).

## TLA+ extensions

- Retweets: `retweets` set of `(retweeter, tweet_id)` pairs
- Search: derived predicate, no new state
- Notifications: `notifications` partial function `user -> Seq(event)`; events as a discriminated union

The TLC bound grows: 4 users, 6 tweets, 4 retweets, 4 likes, search query domain {hello, world, second}, max 12 notifications. Bound chosen to stay TLC-tractable while exercising every action arrow at least twice.

## Out of scope even at full

- Federation / ActivityPub
- Image/media uploads
- Direct messages
- Live notifications via websocket

## Migration

Same flow as medium: bump `SPEC_SHA`, extend impls, pass the extended conformance suite. F1–F15 carry forward unchanged; F16–F21 add new.
