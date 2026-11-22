# System Design Numbers

> The free, open companion to **Grokking the System Design Interview** by [DesignGurus.io](https://www.designgurus.io/course/grokking-the-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=readme), created by Arslan Ahmad.

[![GitHub stars](https://img.shields.io/github/stars/design-gurus/system-design-numbers?style=social)](https://github.com/design-gurus/system-design-numbers/stargazers)
[![Last commit](https://img.shields.io/github/last-commit/design-gurus/system-design-numbers)](https://github.com/design-gurus/system-design-numbers/commits/main)
[![License: CC BY 4.0](https://img.shields.io/badge/License-CC%20BY%204.0-lightgrey.svg)](LICENSE)
[![PRs welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)

Every system design answer rests on a few numbers: how long an operation takes, how many requests arrive each second, and how many bytes must be stored. This repository collects those numbers on one page, explains how to combine them, and works through ten complete estimations.

A back-of-the-envelope estimation is a quick calculation with rounded inputs that produces the right order of magnitude. An order of magnitude is a factor of ten. The purpose is to decide between designs, not to produce an exact figure.

## Latency numbers

These values are approximate and rounded for recall. Each is correct to an order of magnitude on current hardware. The [latency page](numbers/latency.md) explains the gaps between the rows.

| Operation | Time | Relative to a memory reference |
|-----------|------|--------------------------------|
| L1 cache reference | 1 ns | 0.01x |
| Mutex lock and unlock | 20 ns | 0.2x |
| Main memory reference | 100 ns | 1x |
| Compress 1 KB | 2 µs | 20x |
| Read 1 MB sequentially from memory | 10 µs | 100x |
| SSD random read | 100 µs | 1,000x |
| Round trip inside a data center | 500 µs | 5,000x |
| Read 1 MB sequentially from SSD | 1 ms | 10,000x |
| Disk seek on a spinning disk | 10 ms | 100,000x |
| Read 1 MB sequentially from a spinning disk | 20 ms | 200,000x |
| Round trip between two coasts of one continent | 70 ms | 700,000x |
| Round trip between continents | 150 ms | 1,500,000x |

Two human thresholds complete the table. A response within about 100 ms feels instant, and a response after about 1 second feels slow.

## Powers of two and data sizes

| Power | Exact value | Approximation | Unit |
|-------|-------------|---------------|------|
| 2^10 | 1,024 | 1 thousand | 1 KB |
| 2^20 | 1,048,576 | 1 million | 1 MB |
| 2^30 | 1,073,741,824 | 1 billion | 1 GB |
| 2^40 | about 1.1 x 10^12 | 1 trillion | 1 TB |
| 2^50 | about 1.13 x 10^15 | 1 quadrillion | 1 PB |

| Useful limit | Value |
|--------------|-------|
| 2^16 | 65,536 |
| 2^32 | about 4.3 billion |
| 2^64 | about 1.8 x 10^19 |
| 62^7 (seven characters of letters and digits) | about 3.5 trillion |

More sizes, including typical object sizes, are on the [data sizes page](numbers/powers-of-two-and-data-sizes.md) and the [object sizes page](numbers/storage-and-object-sizes.md).

## Time conversions

| Period | Seconds | Value for mental arithmetic |
|--------|---------|-----------------------------|
| 1 day | 86,400 | 100,000 |
| 1 month (30 days) | 2,592,000 | 2.6 million |
| 1 year | 31,536,000 | 31.5 million |

| Volume per day | Average per second |
|----------------|--------------------|
| 1 million | about 12 |
| 100 million | about 1,200 |
| 1 billion | about 12,000 |

Dividing by 100,000 instead of 86,400 gives a result about 14 percent too low. The peak multiplier applied afterward is far larger than that error. See [time conversions](numbers/time-conversions.md).

## Availability

Availability is the share of time a system answers requests correctly. Each additional nine divides the permitted downtime by ten.

| Availability | Downtime per year | Downtime per month | Downtime per day |
|--------------|-------------------|--------------------|------------------|
| 99% (two nines) | 3.65 days | 7.2 hours | 14.4 minutes |
| 99.9% (three nines) | 8.76 hours | 43.2 minutes | 86.4 seconds |
| 99.95% | 4.38 hours | 21.6 minutes | 43.2 seconds |
| 99.99% (four nines) | 52.6 minutes | 4.32 minutes | 8.64 seconds |
| 99.999% (five nines) | 5.26 minutes | 25.9 seconds | 0.86 seconds |

Components in series multiply their availabilities, so three services at 99.9% give 99.7%. The formulas are on the [availability page](numbers/availability-nines.md).

## The five-step estimation method

1. **State the assumptions.** Name the daily active users, the actions per user per day, and the size of one record.
2. **Compute the traffic.** Multiply users by actions, divide by 86,400 for the average rate, and multiply by 2 or 3 for the peak.
3. **Compute the storage.** Multiply records per day by bytes per record, then by the retention period and the number of copies.
4. **Compute bandwidth and memory.** Multiply the request rate by the response size, and size the cache at about 20 percent of daily reads.
5. **Say what the number changes.** Each result should select or reject a design choice. A number that changes nothing was not worth computing.

Worked example with 100 million writes per day:

```
Writes per second   100,000,000 / 100,000    = about 1,000
Reads per second    1,000 x 100 (read ratio) = about 100,000
Storage per day     100,000,000 x 1 KB       = 100 GB
Storage per year    100 GB x 365             = about 36 TB
```

The conclusion matters more than the arithmetic. At 100,000 reads per second a single database node is not sufficient, so the design needs a cache and read replicas. The full method is on the [estimation method page](cheat-sheets/estimation-method.md).

## All pages

### Numbers

| Page | What it gives you |
|------|-------------------|
| [Latency](numbers/latency.md) | The full latency table and the five gaps that drive design decisions |
| [Powers of two and data sizes](numbers/powers-of-two-and-data-sizes.md) | Byte units, integer limits, and key-space sizes |
| [Time conversions](numbers/time-conversions.md) | Seconds per period and per-day to per-second shortcuts |
| [Availability nines](numbers/availability-nines.md) | Downtime per nine, plus series and parallel formulas |
| [Traffic formulas](numbers/traffic-formulas.md) | From daily active users to average and peak requests per second |
| [Storage and object sizes](numbers/storage-and-object-sizes.md) | Working sizes for text, identifiers, photos, and video |
| [Network and bandwidth](numbers/network-and-bandwidth.md) | Bits against bytes, link speeds, and transfer times |
| [Component capacity](numbers/component-capacity.md) | Starting assumptions for one server, database, cache, or partition |

### Worked estimations

| System | The number that decides the design |
|--------|-------------------------------------|
| [URL shortener](estimations/url-shortener.md) | 3 TB over five years, so storage is not the hard part |
| [Social feed](estimations/social-feed.md) | 230,000 feed insertions per second from 1,200 posts per second |
| [Chat messaging](estimations/chat-messaging.md) | 100 million open connections and 2 TB of messages per day |
| [Video streaming](estimations/video-streaming.md) | About 42 Tbps of peak delivery traffic |
| [Photo sharing](estimations/photo-sharing.md) | 125 TB of new photos per day against 25 GB of metadata |
| [Ride-hailing location updates](estimations/ride-hailing-location-updates.md) | 1.25 million writes per second that fit in 500 MB of memory |
| [Web crawler](estimations/web-crawler.md) | 4,000 pages per second and a 12.5 GB filter for 10 billion URLs |
| [Notification system](estimations/notification-system.md) | A peak five times the average, absorbed by a queue |
| [Rate limiter memory](estimations/rate-limiter-memory.md) | 640 MB for one algorithm and 8.6 GB for another |
| [Typeahead](estimations/typeahead.md) | 350,000 requests per second inside a 100 ms budget |

### Cheat sheets

| Page | What it gives you |
|------|-------------------|
| [Estimation method](cheat-sheets/estimation-method.md) | The five steps with a script for saying them aloud |
| [Rounding rules](cheat-sheets/rounding-rules.md) | Which shortcuts are safe and how large each error is |
| [Common mistakes](cheat-sheets/common-mistakes.md) | Ten errors that produce a wrong order of magnitude |
| [One page](cheat-sheets/one-page.md) | Every table condensed for a final review |

## FAQ

### What numbers should I know for a system design interview?

Four groups cover almost every question: the latency table, the powers of two, the seconds in a day, and the availability table. All four are printed above. Typical object sizes and per-component capacity are useful additions, and both have their own page.

### What is back-of-the-envelope estimation in system design?

It is a short calculation that converts user counts into requests per second, storage, and bandwidth. The inputs are rounded assumptions that the candidate states aloud. The result is expected to be correct within a factor of ten.

### How do you calculate QPS from daily active users?

QPS means queries per second. Multiply daily active users by the actions each user performs per day, then divide by 86,400. Multiply the average by 2 or 3 to obtain the peak. For example, 10 million users with 20 actions each produce about 2,300 queries per second on average. See [traffic formulas](numbers/traffic-formulas.md).

### Are the latency numbers every programmer should know still accurate?

The list was popularized by Jeff Dean and Peter Norvig, and hardware has improved since then. Individual values have moved, especially for SSDs and networks. The ratios between memory, disk, and network remain valid, and the ratios are what a design decision depends on.

### How accurate does a capacity estimate need to be?

The correct order of magnitude is sufficient. An interviewer evaluates the assumptions and the method. An answer of 36 TB when the careful figure is 42 TB leads to the same design, so the difference has no consequence.

## Course lessons to start with

- [What are Back-of-the-Envelope Estimations?](https://www.designgurus.io/course-play/grokking-the-system-design-interview/doc/what-are-backoftheenvelope-estimations?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=readme): what the estimation step is for and how to perform it, in Grokking the System Design Interview
- [Numbers You Should Know](https://www.designgurus.io/course-play/grokking-system-design-fundamentals/doc/numbers-you-should-know?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=readme): the reference values every estimate starts from, in Grokking System Design Fundamentals
- [Estimating QPS, Storage, and Bandwidth](https://www.designgurus.io/course-play/grokking-system-design-fundamentals/doc/estimating-qps-storage-and-bandwidth?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=readme): the three calculations in order, in Grokking System Design Fundamentals
- [A Worked Estimation Example](https://www.designgurus.io/course-play/grokking-system-design-fundamentals/doc/a-worked-estimation-example?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=readme): one estimate carried from assumptions to a result, in Grokking System Design Fundamentals
- [Latency vs Throughput](https://www.designgurus.io/course-play/grokking-the-system-design-interview/doc/latency-vs-throughput?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=readme): what each one measures and how they trade against each other, in Grokking the System Design Interview
- [Availability](https://www.designgurus.io/course-play/grokking-system-design-fundamentals/doc/availability?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=readme): how availability is measured and improved, in Grokking System Design Fundamentals
- [Capacity Planning](https://www.designgurus.io/course-play/advanced-system-design-fundamentals/doc/capacity-planning?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=readme): how to turn a load estimate into a number of machines, in Advanced System Design Fundamentals
- [The Other Envelope: Cost Estimation](https://www.designgurus.io/course-play/advanced-system-design-fundamentals/doc/the-other-envelope-cost-estimation?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=readme): how to estimate what a design costs to run, in Advanced System Design Fundamentals
- [Designing a URL Shortening Service like TinyURL](https://www.designgurus.io/course-play/grokking-the-system-design-interview/doc/designing-a-url-shortening-service-like-tinyurl?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=readme): a complete design that begins with an estimate, in Grokking the System Design Interview

## Free reading on DesignGurus.io

- Free article: [Back-of-the-Envelope Estimation: A Step-by-Step Guide for System Design Interviews](https://www.designgurus.io/blog/back-of-the-envelope-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=readme): the estimation method at full length.
- Free article: [System Design Cheat Sheet: All Key Concepts on One Page](https://www.designgurus.io/blog/system-design-cheat-sheet?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=readme): the concepts behind the numbers.
- Free article: [Throughput vs Latency: What Each Measures and the Trade-Off](https://www.designgurus.io/blog/throughput-vs-latency?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=readme): the two measures every estimate uses.
- Free article: [High Availability in System Design: 15 Strategies for Always-On Systems](https://www.designgurus.io/blog/high-availability-system-design-basics?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=readme): how a system reaches each availability target.
- Free article: [How to Design a URL Shortener](https://www.designgurus.io/blog/url-shortening?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=readme): a full design that starts from an estimate.
- Free article: [How to Design a Social Media News Feed](https://www.designgurus.io/blog/design-social-media-news-feed?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=readme): a read-heavy design with its numbers.
- Free article: [Designing a Scalable Video Streaming Platform Like YouTube and Netflix](https://www.designgurus.io/blog/design-video-streaming-platform?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=readme): a design where storage and bandwidth dominate.

## Go deeper

- [Grokking the System Design Interview](https://www.designgurus.io/course/grokking-the-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=readme) applies these numbers to complete designs, with a framework for each step of the interview.
- [Grokking System Design Fundamentals](https://www.designgurus.io/course/grokking-system-design-fundamentals?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=readme) teaches the building blocks the numbers refer to: caches, replicas, queues, and partitions.
- [Advanced System Design Fundamentals](https://www.designgurus.io/course/advanced-system-design-fundamentals?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=readme) covers capacity and cost estimation in depth, including when an estimate should change the design.
- [System Design Interview Crash Course](https://www.designgurus.io/course/system-design-interview-crash-course?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=readme) is the shorter path for an interview that is a few days away.
- [Grokking System Design](https://github.com/design-gurus/grokking-system-design) is the sibling repository with patterns, question walkthroughs, and its own [estimation](https://github.com/design-gurus/grokking-system-design/blob/main/cheat-sheets/estimation.md) and [latency](https://github.com/design-gurus/grokking-system-design/blob/main/cheat-sheets/latency-numbers.md) sheets.

For the system design round, see [Grokking System Design](https://github.com/design-gurus/grokking-system-design), the free companion guide with 30 patterns, 59 question walkthroughs, and 35 cheat sheets.

## Contributing

Corrections and new worked estimations are welcome. Please read [CONTRIBUTING.md](CONTRIBUTING.md) first, and show the arithmetic for every number you add.

## License

The content is licensed under [CC BY 4.0](LICENSE). See [NOTICE.md](NOTICE.md) for what the license covers and how to attribute.
