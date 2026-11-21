# Traffic formulas

Traffic estimation converts a count of users into requests per second. Three formulas are sufficient: one for the average rate, one for the peak rate, and one for the division between reads and writes. Requests per second is often written as RPS, and queries per second as QPS. The two are used interchangeably here.

## The three formulas

```
Average RPS = daily active users x actions per user per day / 86,400
Peak RPS    = average RPS x peak factor
Write RPS   = total RPS / (read-to-write ratio + 1)
```

Daily active users, or DAU, is the number of distinct people who use the product on a given day. It is usually a fraction of the total registered users.

## Choosing the inputs

| Input | Typical working value | Notes |
|-------|-----------------------|-------|
| DAU as a share of registered users | 10 to 50 percent | State the DAU directly when the interviewer gives it |
| Actions per user per day | 5 to 50 | Count reads and writes separately when they differ greatly |
| Peak factor for ordinary daily variation | 2 to 3 | Usage concentrates in waking hours |
| Peak factor for events or campaigns | 5 to 10 | A sale, a live match, or a mass notification |
| Read-to-write ratio, content products | 100 to 1 | Feeds, link shorteners, video |
| Read-to-write ratio, messaging | about 1 to 1 | Each message is written once and read once |

These are starting assumptions. A candidate should state the chosen value and continue, since the interviewer will correct an input that matters.

## Quick conversion table

| DAU | Actions per user | Requests per day | Average RPS | Peak RPS at 3x |
|-----|------------------|------------------|-------------|----------------|
| 1 million | 10 | 10 million | about 120 | about 350 |
| 10 million | 20 | 200 million | about 2,300 | about 7,000 |
| 100 million | 20 | 2 billion | about 23,000 | about 70,000 |
| 1 billion | 20 | 20 billion | about 230,000 | about 700,000 |

## Worked example

A product has 10 million daily active users. Each user performs 20 actions per day, and the read-to-write ratio is 10 to 1.

```
Requests per day   10,000,000 x 20     = 200,000,000
Average RPS        200,000,000 / 86,400 = 2,315, say 2,300
Peak RPS           2,300 x 3            = about 7,000
Peak writes        7,000 / 11           = about 640
Peak reads         7,000 - 640          = about 6,360
```

The result selects a design. About 640 writes per second is within the range of a single relational database node, as [component capacity](component-capacity.md) shows. About 6,400 reads per second is near the limit of that node, so the design should add a cache or read replicas.

## Concurrent users

Some systems are limited by open connections instead of requests. Concurrent users are the people connected at one moment.

```
Average concurrent users = DAU x minutes per user per day / 1,440
```

A product with 100 million daily users who each spend 60 minutes has about 4.2 million concurrent users on average. The [video streaming](../estimations/video-streaming.md) and [chat messaging](../estimations/chat-messaging.md) estimations both depend on this formula.

## Checks before moving on

- Reads and writes should sum to the total rate.
- The peak should be stated, since capacity is provisioned for the peak and storage for the average.
- A rate above 100,000 per second should be questioned once, because it usually implies partitioning.

## Go deeper

- [Grokking the System Design Interview](https://www.designgurus.io/course/grokking-the-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=numbers-traffic-formulas) places this calculation in the second step of its interview framework.
- [Time conversions](time-conversions.md) lists the per-day to per-second shortcuts.
- [Estimation method](../cheat-sheets/estimation-method.md) continues from traffic to storage and bandwidth.
- Free article: [Back-of-the-Envelope Estimation: A Step-by-Step Guide for System Design Interviews](https://www.designgurus.io/blog/back-of-the-envelope-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=numbers-traffic-formulas), which applies these formulas in a full estimate.
