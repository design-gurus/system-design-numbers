# System design numbers on one page

Every table from this repository in condensed form, for a final review. Each section links to the page that explains it.

## Latency ([details](../numbers/latency.md))

| Operation | Time |
|-----------|------|
| Main memory reference | 100 ns |
| Read 1 MB from memory | 10 µs |
| SSD random read | 100 µs |
| Round trip inside a data center | 500 µs |
| Read 1 MB from SSD | 1 ms |
| Disk seek on a spinning disk | 10 ms |
| Round trip between continents | 150 ms |
| Feels instant to a person | 100 ms |

Memory is 1,000 times faster than an SSD read. A cross-continent round trip is 300 times slower than one inside a data center.

## Sizes ([details](../numbers/powers-of-two-and-data-sizes.md))

| Unit | Bytes | Count |
|------|-------|-------|
| KB | 2^10 | thousand |
| MB | 2^20 | million |
| GB | 2^30 | billion |
| TB | 2^40 | trillion |
| PB | 2^50 | quadrillion |

A 32-bit value holds 4.3 billion numbers. Seven characters of letters and digits give 3.5 trillion keys.

## Object sizes ([details](../numbers/storage-and-object-sizes.md))

| Object | Size |
|--------|------|
| Identifier or timestamp | 8 bytes |
| Chat message | 100 bytes |
| Text post or profile | 1 KB |
| Web image | 200 KB |
| Phone photo | 2 MB |
| One minute of 1080p video | 40 MB |

## Time ([details](../numbers/time-conversions.md))

| Period | Seconds |
|--------|---------|
| Day | 86,400, rounded to 100,000 |
| Month | 2.6 million |
| Year | 31.5 million |

One million per day is about 12 per second. One billion per day is about 12,000 per second.

## Availability ([details](../numbers/availability-nines.md))

| Level | Downtime per year |
|-------|-------------------|
| 99% | 3.65 days |
| 99.9% | 8.76 hours |
| 99.99% | 52.6 minutes |
| 99.999% | 5.26 minutes |

Components in series multiply. Two replicas in parallel at 99 percent give 99.99 percent.

## Network ([details](../numbers/network-and-bandwidth.md))

| Link | Bytes per second |
|------|------------------|
| 1 Gbps | 125 MB |
| 10 Gbps | 1.25 GB |

Divide bits by 8 to obtain bytes. A 1080p stream uses about 5 Mbps.

## Capacity of one node ([details](../numbers/component-capacity.md))

| Component | Starting assumption |
|-----------|---------------------|
| Application server | 1,000 requests per second |
| Relational database | 1,000 writes or 10,000 simple reads per second |
| In-memory cache | 100,000 operations per second |
| Message broker partition | 10,000 messages per second |
| Connection gateway | 100,000 open connections |

These are assumptions to state aloud, not benchmarks.

## Formulas ([details](../numbers/traffic-formulas.md))

```
Average RPS   = DAU x actions per user / 86,400
Peak RPS      = average RPS x 2 or 3
Storage       = records per day x record size x days kept x copies
Bandwidth     = RPS x response size
Cache         = 20 percent of daily reads x record size
Concurrent    = DAU x minutes per user / 1,440
Nodes         = peak load / capacity per node / 0.7
```

## Method ([details](estimation-method.md))

1. State the assumptions.
2. Compute average and peak traffic.
3. Compute storage with retention and copies.
4. Compute bandwidth and cache memory.
5. Say which design choice each number supports.

## Three checks

- Every figure has a unit, and bits are not confused with bytes.
- Servers are sized for the peak, and storage includes all copies.
- The estimate took under five minutes and led to at least one decision.

## Go deeper

- [Grokking the System Design Interview](https://www.designgurus.io/course/grokking-the-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=cheat-sheets-one-page) is the full course these numbers support, with complete designs and an interview framework.
- [Advanced System Design Interview, Volume II](https://www.designgurus.io/course/grokking-system-design-interview-ii?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=cheat-sheets-one-page) continues with larger case studies once the fundamentals are secure.
- [Grokking System Design](https://github.com/design-gurus/grokking-system-design) is the sibling repository with patterns and question walkthroughs.
