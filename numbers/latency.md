# Latency numbers

Latency is the time between starting an operation and receiving its result. The table below lists the latencies that system design decisions depend on, from one nanosecond to 150 milliseconds. The values are orders of magnitude, not measurements of any particular machine.

The list is commonly credited to Jeff Dean and Peter Norvig, who popularized it as the numbers every programmer should know. Hardware has changed since the list first circulated, so the values here are rounded to figures that remain reasonable.

## The table

| Operation | Time | In nanoseconds |
|-----------|------|----------------|
| L1 cache reference | 1 ns | 1 |
| Mutex lock and unlock | 20 ns | 20 |
| Main memory reference | 100 ns | 100 |
| Compress 1 KB | 2 µs | 2,000 |
| Read 1 MB sequentially from memory | 10 µs | 10,000 |
| SSD random read | 100 µs | 100,000 |
| Round trip inside a data center | 500 µs | 500,000 |
| Read 1 MB sequentially from SSD | 1 ms | 1,000,000 |
| Disk seek on a spinning disk | 10 ms | 10,000,000 |
| Read 1 MB sequentially from a spinning disk | 20 ms | 20,000,000 |
| Round trip between two coasts of one continent | 70 ms | 70,000,000 |
| Round trip between continents | 150 ms | 150,000,000 |

One microsecond (µs) is 1,000 nanoseconds, and one millisecond (ms) is 1,000 microseconds.

## The five gaps that matter

A design decision rarely depends on one value. It depends on the ratio between two of them.

| Gap | Ratio | Design consequence |
|-----|-------|--------------------|
| Memory reference against SSD random read | 1,000x | Keep frequently read data in memory |
| SSD random read against disk seek | 100x | Avoid random reads on spinning disks |
| Sequential against random access | 10x to 100x | Append to a log instead of updating in place |
| Data center round trip against memory reference | 5,000x | Each avoided network call pays for much local work |
| Cross-continent against data center round trip | 300x | Serve users from a region near them |

## Worked example: a latency budget

A latency budget is the total time a request may take, divided among its steps. Suppose a page must respond within 200 ms.

```
Sequential calls to 6 services in one data center   6 x 0.5 ms  = 3 ms
One SSD read per service                            6 x 0.1 ms  = 0.6 ms
One call to a service on another continent          1 x 150 ms  = 150 ms
```

The six local calls consume 3.6 ms, which is under 2 percent of the budget. The single remote call consumes 75 percent of it. The conclusion is that the remote data must be replicated or cached in the local region, and that tuning the local calls would gain almost nothing.

## Rules that follow from the table

- A 200 ms budget holds one cross-continent round trip, or about 400 round trips inside a data center.
- Ten sequential calls inside a data center cost 5 ms of network time. The same ten calls in parallel cost about 0.5 ms.
- A new encrypted connection costs two or three round trips before any data moves, so connections should be reused.
- The slowest requests define the user experience. Budgets should be set for the 99th percentile, which is the time that 99 percent of requests stay under.

## Where these numbers appear

- [Typeahead](../estimations/typeahead.md) uses the 100 ms threshold to justify regional serving.
- [Component capacity](component-capacity.md) derives throughput from the time one operation takes.
- The sibling repository shows the same ladder on a [logarithmic scale](https://github.com/design-gurus/grokking-system-design/blob/main/cheat-sheets/latency-numbers.md).

## Go deeper

- [Grokking the System Design Interview](https://www.designgurus.io/course/grokking-the-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=numbers-latency) shows how to state a latency budget and defend it during a design.
- Lesson: [Latency vs Throughput](https://www.designgurus.io/course-play/grokking-the-system-design-interview/doc/latency-vs-throughput?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=numbers-latency), in Grokking the System Design Interview
- Lesson: [Latency and Performance](https://www.designgurus.io/course-play/grokking-system-design-fundamentals/doc/latency-and-performance?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=numbers-latency), in Grokking System Design Fundamentals
- Lesson: [Latency Budgets and Data Residency](https://www.designgurus.io/course-play/advanced-system-design-fundamentals/doc/latency-budgets-and-data-residency?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=numbers-latency), in Advanced System Design Fundamentals
- [Grokking System Design Fundamentals](https://www.designgurus.io/course/grokking-system-design-fundamentals?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=numbers-latency) explains caching, replication, and content delivery networks, the three techniques these gaps justify.
- Free article: [Throughput vs Latency: What Each Measures and the Trade-Off](https://www.designgurus.io/blog/throughput-vs-latency?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=numbers-latency)
- Free article: [Demystifying Long-Tail Latency](https://www.designgurus.io/blog/long-tail-latency?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=numbers-latency), which explains why the slowest requests matter more than the average.
