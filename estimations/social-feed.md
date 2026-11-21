# Estimation: social feed

A social feed shows each user recent posts from the accounts they follow. The estimate reveals that the post rate is modest, while the work of copying each post into follower feeds is 200 times larger. That multiplication, called write amplification, is the number that decides the design.

## Assumptions

| Input | Value |
|-------|-------|
| Daily active users | 200 million |
| Posts per day | 100 million (one post per two users) |
| Feed views per user per day | 5 |
| Average followers per account | 200 |
| Size of one post with metadata | 1 KB |
| Feed entries kept per user | 500 post identifiers of 8 bytes |
| Peak factor | 3 |

## Arithmetic

```
Posts per second        100,000,000 / 86,400      = about 1,200
Feed views per day      200,000,000 x 5           = 1 billion
Feed views per second   1,000,000,000 / 86,400    = about 12,000
Peak feed views         12,000 x 3                = 36,000 per second
```

Fan-out is the copying of one post into the feed of every follower:

```
Insertions per day      100,000,000 x 200         = 20 billion
Insertions per second   20,000,000,000 / 86,400   = about 230,000
```

Storage and memory:

```
Posts per day      100,000,000 x 1 KB             = 100 GB
Posts per year     100 GB x 365                   = 36.5 TB
Feed cache         200,000,000 x 500 x 8 B        = 800 GB
```

## Result

| Quantity | Value |
|----------|-------|
| Posts | 1,200 per second |
| Feed views at peak | 36,000 per second |
| Feed insertions | 230,000 per second |
| Post storage | 36.5 TB per year |
| Feed cache | 800 GB |

## What the result changes

- **Precomputing feeds is affordable for ordinary accounts.** Reading a prepared list for 36,000 views per second is far cheaper than assembling each feed from 200 accounts at read time.
- **The insertion rate needs a queue and many workers.** At 230,000 insertions per second, fan-out cannot run inside the request that creates a post. It must run asynchronously, which means after the request has returned.
- **Very large accounts need a different path.** An account with 10 million followers creates 10 million insertions from one post. Posts from such accounts are better merged into feeds at read time.
- **The feed cache must be partitioned.** At 800 GB, it exceeds the memory of one node, so it is divided among nodes by user identifier.

## Go deeper

- [Grokking the System Design Interview](https://www.designgurus.io/course/grokking-the-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=estimations-social-feed) covers feed generation and the choice between copying on write and merging on read.
- The sibling repository has a [social feed walkthrough](https://github.com/design-gurus/grokking-system-design/blob/main/questions/design-twitter.md) for the complete design.
- [Traffic formulas](../numbers/traffic-formulas.md) explains the conversion from users to requests per second.
- Free article: [How to Design a Social Media News Feed: A System Design Interview Walkthrough](https://www.designgurus.io/blog/design-social-media-news-feed?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=estimations-social-feed), the design that these numbers size.
