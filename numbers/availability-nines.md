# Availability nines

Availability is the share of time a system answers requests correctly, written as a percentage. It is usually described by its count of nines: 99.9 percent is three nines. Each additional nine divides the permitted downtime by ten, and typically costs far more than the previous one.

## Downtime per level

| Availability | Per year | Per month (30 days) | Per day |
|--------------|----------|---------------------|---------|
| 99% | 3.65 days | 7.2 hours | 14.4 minutes |
| 99.9% | 8.76 hours | 43.2 minutes | 86.4 seconds |
| 99.95% | 4.38 hours | 21.6 minutes | 43.2 seconds |
| 99.99% | 52.6 minutes | 4.32 minutes | 8.64 seconds |
| 99.999% | 5.26 minutes | 25.9 seconds | 0.86 seconds |

The formula is: downtime equals the period multiplied by one minus the availability. For three nines over a year, that is 8,760 hours multiplied by 0.001, which gives 8.76 hours.

Two values are worth memorizing. Three nines allow about 9 hours per year, and five nines allow about 5 minutes per year.

## What each level implies

| Target | What it requires in practice |
|--------|------------------------------|
| 99% | A single server with manual recovery can meet it |
| 99.9% | Redundant servers and automated restarts |
| 99.99% | No single point of failure, automatic failover, and deployments without downtime |
| 99.999% | Several regions serving traffic, since 5 minutes per year leaves no time for a person to react |

A single point of failure is a component whose failure stops the whole system. Failover is the automatic transfer of work from a failed component to a healthy one.

## Components in series

Components are in series when a request needs every one of them. Their availabilities multiply.

```
Availability in series = A1 x A2 x A3
```

| Chain | Combined availability |
|-------|-----------------------|
| Three services at 99.9% | 99.7% |
| Five services at 99.9% | 99.5% |
| One at 99.9% and one at 99.99% | 99.89% |

A chain is always less available than its weakest member. Five services at three nines allow about 44 hours of downtime per year, compared with 8.76 hours for one of them.

## Components in parallel

Components are in parallel when any one of them can serve the request. The system fails only if all of them fail together.

```
Availability in parallel = 1 - (1 - A1) x (1 - A2)
```

| Redundant set | Combined availability |
|---------------|-----------------------|
| Two replicas at 99% | 99.99% |
| Two replicas at 99.9% | 99.9999% |

The parallel formula assumes that failures are independent. Replicas that share a power supply, a network, or a faulty deployment fail together, so real results are lower than the formula predicts.

## Worked example

A request passes through a load balancer at 99.99 percent, an application tier, and a database. The application tier has two servers at 99 percent each, and the database has two replicas at 99.9 percent each.

```
Application tier   1 - 0.01 x 0.01      = 99.99%
Database tier      1 - 0.001 x 0.001    = 99.9999%
Whole path         0.9999 x 0.9999 x 0.999999 = 99.98%
```

The path allows about 1.8 hours of downtime per year. The database is no longer the limiting component. A further improvement must come from the load balancer or the application tier, which each contribute 0.01 percent of unavailability.

## Go deeper

- [Grokking the System Design Interview](https://www.designgurus.io/course/grokking-the-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=numbers-availability-nines) shows how to turn an availability target into replication and failover choices.
- [Advanced System Design Fundamentals](https://www.designgurus.io/course/advanced-system-design-fundamentals?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=numbers-availability-nines) covers failure modes and the size of the area a failure affects.
- [Component capacity](component-capacity.md) covers the other reason to add nodes, which is throughput.
- Free article: [High Availability in System Design: 15 Strategies for Always-On Systems](https://www.designgurus.io/blog/high-availability-system-design-basics?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=numbers-availability-nines), the techniques that earn each extra nine.
- Free answer: [What is the difference between Availability and Reliability?](https://www.designgurus.io/answers/detail/what-is-the-difference-between-availability-and-reliability?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=numbers-availability-nines)
