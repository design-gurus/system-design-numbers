# Worked estimations

Ten complete estimations. Each page states its assumptions, shows the arithmetic line by line, and ends with the design decision that the numbers support. The assumptions are illustrative and are not measurements of any real product.

| System | Dominant resource | The number that decides the design |
|--------|-------------------|-------------------------------------|
| [URL shortener](url-shortener.md) | Read throughput | 4,000 reads per second against 40 writes |
| [Social feed](social-feed.md) | Write amplification | 230,000 feed insertions per second |
| [Chat messaging](chat-messaging.md) | Connections and storage | 100 million open connections |
| [Video streaming](video-streaming.md) | Bandwidth | About 42 Tbps at peak |
| [Photo sharing](photo-sharing.md) | Storage | 125 TB of new photos per day |
| [Ride-hailing location updates](ride-hailing-location-updates.md) | Write throughput | 1.25 million updates per second |
| [Web crawler](web-crawler.md) | Bandwidth and politeness | 4,000 pages per second |
| [Notification system](notification-system.md) | Burst handling | 60,000 per second at peak |
| [Rate limiter memory](rate-limiter-memory.md) | Memory | 640 MB or 8.6 GB, depending on the algorithm |
| [Typeahead](typeahead.md) | Latency | 350,000 requests per second within 100 ms |

## How to practice with these pages

1. Read only the assumptions, then cover the rest of the page.
2. Compute traffic, storage, and bandwidth independently, speaking each step aloud.
3. Compare the results. A difference within a factor of two is a success.
4. State the design consequence before reading the final section.

The method is described in the [estimation method](../cheat-sheets/estimation-method.md), and the input values come from the [numbers](../numbers/README.md) pages.

## Go deeper

- [Grokking the System Design Interview](https://www.designgurus.io/course/grokking-the-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=estimations-readme) contains the full designs for these systems, with the estimation step in context.
