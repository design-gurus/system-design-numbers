# Estimation: notification system

A notification system sends push messages, emails, and text messages on behalf of other services. Its average load is moderate, but campaigns and incidents create bursts several times higher. The estimate sizes the system for those bursts and shows why a queue sits at its center.

## Assumptions

| Input | Value |
|-------|-------|
| Notifications per day | 1 billion |
| Peak factor | 5 |
| Size of one notification in the queue | 1 KB |
| Size of one delivery log record | 200 bytes |
| Log retention | 30 days |
| Time for one call to a delivery provider | 100 ms |
| Concurrent provider calls per worker | 50 |
| Throughput of one queue partition | 10,000 messages per second |

A delivery provider is the external service that performs the final delivery, such as a mobile push gateway or an email relay.

## Arithmetic

Traffic:

```
Average per second    1,000,000,000 / 86,400       = about 12,000
Peak per second       12,000 x 5                   = 60,000
```

Queue:

```
Data through the queue per day    1,000,000,000 x 1 KB   = 1 TB
Partitions required at peak       60,000 / 10,000        = 6
```

Workers:

```
Sends per worker per second    50 / 0.1 s            = 500
Workers at average load        12,000 / 500          = 24
Workers at peak load           60,000 / 500          = 120
```

Delivery log:

```
Per day        1,000,000,000 x 200 B                 = 200 GB
For 30 days    200 GB x 30                           = 6 TB
```

## Result

| Quantity | Value |
|----------|-------|
| Average rate | 12,000 per second |
| Peak rate | 60,000 per second |
| Queue partitions | at least 6, with 12 or more for spare capacity |
| Workers | 24 on average, 120 at peak |
| Delivery log | 200 GB per day, 6 TB for 30 days |

## What the result changes

- **A queue separates acceptance from delivery.** Calling services should receive an immediate acknowledgment. The queue holds the burst while workers send at the rate providers permit.
- **Workers scale automatically.** The peak needs five times the workers of the average. A fixed pool of 120 would be idle most of the day, so the pool should grow with the queue length.
- **Provider limits may be lower than the peak.** If a provider accepts 20,000 sends per second, a burst of 60,000 takes three times longer to clear. The design needs separate queues per channel and a priority order, so that a login code is not delayed behind a marketing campaign.
- **The log is small enough to keep.** At 6 TB for 30 days, a full delivery record supports debugging and duplicate prevention at low cost.

## Go deeper

- [Grokking the System Design Interview](https://www.designgurus.io/course/grokking-the-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=estimations-notification-system) covers queues, retries, and delivery guarantees in its messaging designs.
- The sibling repository has a [notification system walkthrough](https://github.com/design-gurus/grokking-system-design/blob/main/questions/design-notification-system.md) for the complete design.
- [Component capacity](../numbers/component-capacity.md) gives the per-partition throughput assumed here.
