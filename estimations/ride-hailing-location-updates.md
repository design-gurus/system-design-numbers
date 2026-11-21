# Estimation: ride-hailing location updates

A ride-hailing service must know where every active driver is, so that it can match riders with nearby cars. Each driver's phone reports its position every few seconds. The estimate shows an unusual combination: an extremely high write rate over a data set small enough to hold in memory.

## Assumptions

| Input | Value |
|-------|-------|
| Drivers online at peak | 5 million |
| Drivers online on average | 2 million |
| Interval between updates | 4 seconds |
| Size of one update | 100 bytes |
| History retention | 1 year |

## Arithmetic

Write rate:

```
Peak updates per second      5,000,000 / 4         = 1.25 million
Average updates per second   2,000,000 / 4         = 500,000
```

Network traffic arriving at peak:

```
Bytes per second     1,250,000 x 100 B             = 125 MB
Bits per second      125 MB x 8                    = 1 Gbps
```

Current positions, keeping only the latest update per driver:

```
Memory     5,000,000 x 100 B                       = 500 MB
```

Location history, at the average rate:

```
Updates per day     500,000 x 86,400               = 43.2 billion
Storage per day     43,200,000,000 x 100 B         = 4.32 TB
Storage per year    4.32 TB x 365                  = about 1.6 PB
```

## Result

| Quantity | Value |
|----------|-------|
| Updates at peak | 1.25 million per second |
| Inbound traffic at peak | 1 Gbps |
| Memory for current positions | 500 MB |
| History | 4.32 TB per day, about 1.6 PB per year |

## What the result changes

- **Current positions belong in memory.** The whole data set is 500 MB, and each update overwrites the previous one. Writing it to a disk-based database 1.25 million times per second would waste capacity on data that is obsolete within 4 seconds.
- **The in-memory store is partitioned for throughput, not size.** One cache node handles about 100,000 operations per second under the working assumption, so the peak requires at least 13 nodes. Partitioning by geographic cell keeps nearby drivers together for matching.
- **History takes a separate path.** Updates are appended to a log and written in batches to storage built for analysis. The matching service never reads this path.
- **The update interval is a design parameter.** Changing it from 4 seconds to 8 halves every number on this page. An adaptive interval, with slower updates for drivers who are not moving, is a legitimate optimization to propose.
- **Retention deserves a question.** At 1.6 PB per year, the interviewer should be asked how long precise history must be kept.

## Go deeper

- [Grokking the System Design Interview](https://www.designgurus.io/course/grokking-the-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=estimations-ride-hailing-location-updates) contains the complete ride-hailing design, including geographic indexing and matching.
- The sibling repository has a [ride-hailing walkthrough](https://github.com/design-gurus/grokking-system-design/blob/main/questions/design-uber.md) for the complete design.
- [Component capacity](../numbers/component-capacity.md) gives the per-node throughput assumed here.
- Free article: [How to Design a Ride Sharing Service](https://www.designgurus.io/blog/ride-sharing-service?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=estimations-ride-hailing-location-updates), the design that these numbers size.
