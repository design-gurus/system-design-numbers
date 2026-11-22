# Common estimation mistakes

Most wrong estimates come from a small set of errors. Each one produces a characteristic factor, such as 8 or 1,000, so the size of a discrepancy often identifies its cause. This page lists ten errors with the factor each one introduces and the correction.

## Unit errors

| Mistake | Factor | Correction |
|---------|--------|------------|
| Treating megabits as megabytes | 8 | Network speeds are in bits. Divide by 8 to obtain bytes. |
| Confusing milliseconds with microseconds | 1,000 | A data center round trip is 0.5 ms. A memory read is 0.1 µs. |
| Mixing million and billion | 1,000 | Write the power of ten beside each figure. |
| Using total users instead of daily active users | 2 to 10 | State which of the two the figure represents. |

## Structural errors

| Mistake | Factor | Correction |
|---------|--------|------------|
| Sizing for the average instead of the peak | 2 to 10 | Multiply the average rate by a peak factor before sizing servers. |
| Omitting replication | 3 | Multiply stored data by the number of copies. |
| Omitting write amplification | 10 to 1,000 | Count the writes each action triggers, such as copies into follower feeds. |
| Combining media and metadata in one figure | Hides the design | Estimate each separately, since they use different storage. |
| Forgetting retention | 365 or more | Storage is a daily amount multiplied by the days kept. |
| Estimating without a conclusion | None | End each number with the design choice it supports. |

## Example: one estimate, three errors

A candidate is asked to size a service with 300 million registered users, of whom 100 million are active each day. Each active user uploads one 2 MB file per day.

Flawed version:

```
Uploads per day     300,000,000 x 2 MB    = 600 TB     (used registered users)
Per year            600 TB x 365          = 219 PB     (no replication)
Upload bandwidth    600 TB / 86,400       = 6.9 GB/s   (average, reported as Gbps)
```

Corrected version:

```
Uploads per day     100,000,000 x 2 MB    = 200 TB
Per year            200 TB x 365          = 73 PB
With 3 copies       73 PB x 3             = 219 PB
Average bandwidth   200 TB / 86,400       = about 2.3 GB per second
In bits             2.3 GB x 8            = about 18.5 Gbps
Peak bandwidth      18.5 Gbps x 3         = about 56 Gbps
```

The two storage totals agree by coincidence, because one factor of 3 was added and another was omitted. The flawed bandwidth figure, read as 6.9 Gbps, is 8 times lower than the correct peak of 56 Gbps. Agreement in the final number does not prove that the method was correct, which is the reason to show every line.

## Process errors

- **Calculating in silence.** The interviewer evaluates the reasoning, and reasoning that is not spoken cannot be evaluated.
- **False precision.** A result of 11,574 requests per second should be stated as about 12,000.
- **Spending too long.** An estimate longer than five minutes takes time from the design.
- **Never returning to the numbers.** Later decisions should refer to them: "At 36,000 reads per second, a cache is required here."
- **Defending a wrong assumption.** When the interviewer supplies a different figure, the candidate should accept it and recompute.

## A final check

Before leaving the estimate, confirm four items:

1. Every figure has a unit.
2. The rate is the peak rate.
3. Storage includes retention and copies.
4. At least one number has led to a design decision.

## Go deeper

- [Grokking the System Design Interview](https://www.designgurus.io/course/grokking-the-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=cheat-sheets-common-mistakes) demonstrates the estimation step in every design it covers.
- Lesson: [Back-of-the-Envelope, Revisited](https://www.designgurus.io/course-play/advanced-system-design-fundamentals/doc/backoftheenvelope-revisited?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=cheat-sheets-common-mistakes), in Advanced System Design Fundamentals
- Lesson: [When Estimates Change the Design](https://www.designgurus.io/course-play/advanced-system-design-fundamentals/doc/when-estimates-change-the-design?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=cheat-sheets-common-mistakes), in Advanced System Design Fundamentals
- [Advanced System Design Fundamentals](https://www.designgurus.io/course/advanced-system-design-fundamentals?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=cheat-sheets-common-mistakes) covers the cases where an estimate should change the design.
- [Rounding rules](rounding-rules.md) explains which shortcuts are safe.
