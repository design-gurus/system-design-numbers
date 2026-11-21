# Component capacity

An estimate ends with a count of machines, and that count needs an assumption about what one machine can handle. The values on this page are starting assumptions to state aloud during an interview. They are not benchmarks. Real capacity depends on hardware, query shape, and payload size, and it can differ from these figures by a factor of ten in either direction.

## Starting assumptions per node

Throughput is the number of operations a component completes per second.

| Component | Working throughput per node | What changes it most |
|-----------|-----------------------------|----------------------|
| Application server, simple requests | 1,000 requests per second | Work per request, and time spent waiting on other services |
| Relational database, writes | 1,000 per second | Indexes, transaction size, and disk type |
| Relational database, simple indexed reads | 10,000 per second | Whether the data fits in memory |
| In-memory cache | 100,000 operations per second | Value size and network capacity |
| Message broker, one partition | 10,000 messages per second | Message size and durability settings |
| Gateway holding idle connections | 100,000 open connections | Memory per connection and message rate |

A message broker is a service that stores messages between a sender and a receiver. A partition is one ordered sequence of messages within it.

## Where the values come from

Each value follows from the [latency table](latency.md). A cache operation is a memory lookup and one network round trip of about 0.5 ms, and a node serves many clients in parallel, so 100,000 operations per second is plausible. A database write must reach durable storage, which costs at least one disk operation, so its rate is about 100 times lower.

## The sizing formula

```
Nodes = peak load / capacity per node, divided by target utilization, plus spare nodes
```

Target utilization is the share of capacity a node should use at peak. A common target is 50 to 70 percent, which leaves room for a failed node and for unexpected growth.

## Worked example

A service has a peak of 7,000 requests per second, of which 640 are writes.

```
Application servers   7,000 / 1,000 / 0.7   = 10, plus 2 spare = 12
Database writes       640 / 1,000           = 64 percent of one node
Database reads        6,360 / 10,000        = 64 percent of one node
Cache operations      6,360 / 100,000       = 6 percent of one node
```

Reads and writes together exceed what one database node should carry. The estimate selects a primary node for writes with one or two read replicas, and a single cache node with a replica for availability. It does not justify partitioning the database, because the write rate remains under the capacity of one node.

## When to partition

Partitioning, also called sharding, divides data among several nodes so that each handles a share of the writes. It adds considerable complexity, so the estimate should show that it is required.

| Signal from the estimate | Likely response |
|--------------------------|-----------------|
| Reads exceed one node, writes do not | Add a cache and read replicas |
| Writes exceed about 1,000 per second sustained | Partition, or select a store built for high write rates |
| Data exceeds a few terabytes per node | Partition by storage, even at a low write rate |
| Open connections exceed 100,000 | Add a separate gateway tier |

## How to say it in an interview

A candidate should present these values as assumptions: "I will assume one database node handles about 1,000 writes per second. The peak is 640, so one primary is sufficient for now." The phrasing shows the method and invites the interviewer to supply a different figure.

## Go deeper

- [Grokking the System Design Interview](https://www.designgurus.io/course/grokking-the-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=numbers-component-capacity) shows when each scaling step is justified in complete designs.
- [Advanced System Design Fundamentals](https://www.designgurus.io/course/advanced-system-design-fundamentals?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=numbers-component-capacity) covers capacity and cost estimation, including the points where an estimate changes the design.
- [Traffic formulas](traffic-formulas.md) produces the peak load this page divides.
