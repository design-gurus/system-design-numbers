# Estimation: rate limiter memory

A rate limiter rejects requests from a client that exceeds an allowed rate. It keeps a small amount of state for every active client, so its cost is memory. The estimate compares two algorithms and shows that the choice between them changes the memory requirement by a factor of 13.5.

## Assumptions

| Input | Value |
|-------|-------|
| Clients active within one time window | 10 million |
| Limit per client | 100 requests per minute |
| Requests checked per second | 50,000 |
| Overhead per entry in an in-memory store | 40 bytes |
| Size of an identifier, counter, or timestamp | 8 bytes each |

## Arithmetic

A token bucket stores a count of remaining tokens and the time of the last refill. Each request removes one token, and tokens return at a fixed rate.

```
Bytes per client    8 (identifier) + 8 (tokens) + 8 (time) + 40   = 64
Total memory        10,000,000 x 64 B                             = 640 MB
```

A sliding window log stores the timestamp of every request inside the window. It is exact, but its size grows with the limit.

```
Bytes per client    100 x 8 (timestamps) + 64                     = 864
Total memory        10,000,000 x 864 B                            = 8.64 GB
Ratio               864 / 64                                      = 13.5
```

Separate limits for 10 endpoints, using token buckets:

```
Total memory        640 MB x 10                                   = 6.4 GB
```

Throughput, with one store operation per check:

```
Load on one cache node    50,000 / 100,000                        = 50 percent
```

## Result

| Design | Memory |
|--------|--------|
| Token bucket, one limit per client | 640 MB |
| Sliding window log, one limit per client | 8.64 GB |
| Token bucket, ten limits per client | 6.4 GB |

## What the result changes

- **A token bucket fits on one node.** At 640 MB and 50,000 operations per second, a single cache node holds the state and handles the load. A replica is added for availability, not for capacity.
- **The exact algorithm has a price.** The sliding window log costs 13.5 times more memory. If the limit were 1,000 requests per minute, each client would need about 8 KB, and the total would reach about 80 GB.
- **Limits per endpoint multiply the state.** Ten limits per client bring the token bucket to 6.4 GB, which still fits on one node but should be stated.
- **Entries must expire.** The estimate counts only active clients. Without expiry, every client ever seen would remain in memory, and the 640 MB figure would grow without limit.
- **Latency matters more than memory.** Every request waits for the check, which costs one round trip of about 0.5 ms inside a data center. The store must be in the same region as the servers that call it.

## Go deeper

- [Grokking the System Design Interview](https://www.designgurus.io/course/grokking-the-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=estimations-rate-limiter-memory) contains the complete rate limiter design, including the distributed case.
- Lesson: [Designing an API Rate Limiter](https://www.designgurus.io/course-play/grokking-the-system-design-interview/doc/designing-an-api-rate-limiter?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=estimations-rate-limiter-memory), in Grokking the System Design Interview
- Lesson: [Design a Distributed Rate Limiter](https://www.designgurus.io/course-play/system-design-interview-crash-course/doc/design-a-distributed-rate-limiter?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=estimations-rate-limiter-memory), in System Design Interview Crash Course
- Lesson: [Rate Limiting Algorithms](https://www.designgurus.io/course-play/grokking-system-design-fundamentals/doc/rate-limiting-algorithms?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=estimations-rate-limiter-memory), in Grokking System Design Fundamentals
- The sibling repository has a [rate limiter walkthrough](https://github.com/design-gurus/grokking-system-design/blob/main/questions/design-rate-limiter.md) for the complete design.
- [Storage and object sizes](../numbers/storage-and-object-sizes.md) lists the field sizes used above.
- Free article: [How to Design a Rate Limiter: Algorithms, Architecture, and Trade-offs](https://www.designgurus.io/blog/grokking-rate-limiters?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=estimations-rate-limiter-memory), the design that these numbers size.
- Free answer: [Rate limiting algorithms, each explained with an example](https://www.designgurus.io/answers/detail/rate-limiting-algorithms?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=estimations-rate-limiter-memory)
