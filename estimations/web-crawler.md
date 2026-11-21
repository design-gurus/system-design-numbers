# Estimation: web crawler

A web crawler downloads pages, extracts their links, and repeats the process to keep a copy of the web current. The estimate shows that processing power is not the limit. The limits are bandwidth, storage, and politeness, which is the rule that a crawler must not send requests to one site too quickly.

## Assumptions

| Input | Value |
|-------|-------|
| Pages fetched per month | 10 billion |
| Average page size (HTML only) | 100 KB |
| Compression ratio for stored pages | 4 to 1 |
| Size of one URL | 100 bytes |
| Average time for one fetch | 500 ms |
| Concurrent connections per fetch server | 200 |
| Politeness limit | 1 request per second per site |

## Arithmetic

Throughput and bandwidth:

```
Pages per second     10,000,000,000 / 2,600,000    = about 4,000
Download rate        4,000 x 100 KB                = 400 MB per second
In bits              400 MB x 8                    = 3.2 Gbps
```

Storage for one month of pages:

```
Uncompressed     10,000,000,000 x 100 KB           = 1 PB
Compressed       1 PB / 4                          = 250 TB
```

Fetch servers:

```
Pages per server per second   200 / 0.5 s          = 400
Servers required              4,000 / 400          = 10
```

URL bookkeeping:

```
URL list              10,000,000,000 x 100 B        = 1 TB
Seen-URL filter       10,000,000,000 x 10 bits      = 12.5 GB
```

The filter is a Bloom filter, a compact structure that answers whether an item has probably been seen. At 10 bits per item, about 1 percent of answers are false positives.

## Result

| Quantity | Value |
|----------|-------|
| Fetch rate | 4,000 pages per second |
| Bandwidth | 3.2 Gbps sustained |
| Page storage | 250 TB per month, compressed |
| Fetch servers | about 10, before spare capacity |
| URL list | 1 TB |
| Seen-URL filter | 12.5 GB |

## What the result changes

- **Ten servers can fetch the whole workload.** The fetch tier is small. Most engineering effort goes into scheduling, not into raw downloading.
- **Politeness sets the shape of the queue.** With one request per second per site, a rate of 4,000 pages per second needs at least 4,000 distinct sites in progress at every moment. The list of pending URLs is therefore organized as one queue per site.
- **The URL list does not fit in memory.** At 1 TB, it lives on disk and is partitioned by site, with only the front of each queue held in memory.
- **Duplicate detection does fit in memory.** A 12.5 GB filter replaces a 1 TB lookup, at the cost of skipping about 1 percent of new URLs.
- **Storage dominates over a year.** Keeping twelve monthly copies would need 3 PB, so the design should store only pages that changed.

## Go deeper

- [Grokking the System Design Interview](https://www.designgurus.io/course/grokking-the-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=estimations-web-crawler) contains the complete crawler design, including scheduling and duplicate detection.
- The sibling repository has a [web crawler walkthrough](https://github.com/design-gurus/grokking-system-design/blob/main/questions/design-web-crawler.md) for the complete design.
- [Network and bandwidth](../numbers/network-and-bandwidth.md) covers the conversion between bytes and bits.
- Free article: [How to Design a Web Crawler from Scratch](https://www.designgurus.io/blog/design-web-crawler?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=estimations-web-crawler), the design that these numbers size.
