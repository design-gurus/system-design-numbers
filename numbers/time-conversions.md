# Time conversions

Requirements are stated per day or per month, while capacity is planned per second. Every estimate therefore contains a division by the number of seconds in a period. This page lists those divisors and the shortcuts that make the division possible without a calculator.

## Seconds in a period

| Period | Exact seconds | Rounded value |
|--------|---------------|---------------|
| 1 minute | 60 | 60 |
| 1 hour | 3,600 | 4,000 |
| 1 day | 86,400 | 100,000 |
| 1 week | 604,800 | 600,000 |
| 1 month (30 days) | 2,592,000 | 2.6 million |
| 1 year (365 days) | 31,536,000 | 31.5 million |

Three further values appear often: a day has 1,440 minutes, a month has about 730 hours, and a year has 8,760 hours.

Some references round the month to 2.5 million seconds. Either value is acceptable, since the two differ by less than 4 percent.

## Per day to per second

The most common conversion divides a daily volume by 86,400.

| Volume per day | Exact average per second | Value to say aloud |
|----------------|--------------------------|--------------------|
| 1 million | 11.6 | about 12 |
| 10 million | 116 | about 120 |
| 100 million | 1,157 | about 1,200 |
| 1 billion | 11,574 | about 12,000 |
| 10 billion | 115,741 | about 120,000 |

The single fact to memorize is that 1 million per day is about 12 per second. Every other row is that fact multiplied by a power of ten.

## Per month to per second

| Volume per month | Average per second |
|------------------|--------------------|
| 1 million | about 0.4 |
| 100 million | about 40 |
| 1 billion | about 400 |
| 10 billion | about 4,000 |

## How large the rounding error is

Dividing by 100,000 instead of 86,400 produces a rate that is 13.6 percent too low. The error is acceptable for two reasons. First, the input assumptions are rarely known within 13.6 percent. Second, the peak multiplier of 2 or 3 that follows is much larger than the error.

A candidate who wants the more careful figure can add one seventh to the rounded result. For 100 million per day, the rounded rate is 1,000, and adding one seventh gives about 1,140, against an exact 1,157.

## Per second to per year

Storage estimates run in the opposite direction, from a rate to a yearly total.

| Rate | Per day | Per year |
|------|---------|----------|
| 1 per second | 86,400 | about 31.5 million |
| 1,000 per second | 86.4 million | about 31.5 billion |
| 1 MB per second | 86.4 GB | about 31.5 TB |
| 1 GB per day | 1 GB | 365 GB |

## Worked example

A service receives 250 million requests per day.

```
Rounded     250,000,000 / 100,000   = 2,500 per second
Exact       250,000,000 / 86,400    = 2,894 per second
Peak        2,894 x 3               = about 8,700 per second
```

The rounded and exact averages lead to the same conclusion. A peak near 9,000 requests per second requires several application servers behind a load balancer, which is a component that distributes requests among servers.

## Go deeper

- [Grokking the System Design Interview](https://www.designgurus.io/course/grokking-the-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=numbers-time-conversions) uses these conversions in the estimation step of every design.
- [Traffic formulas](traffic-formulas.md) continues from the average rate to the peak rate.
- [Rounding rules](../cheat-sheets/rounding-rules.md) lists which shortcuts are safe.
