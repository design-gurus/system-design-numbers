# Rounding rules

Estimation by hand is practical only with rounded numbers. A rounding rule is safe when its error is smaller than the uncertainty already present in the inputs. This page lists the safe shortcuts, the size of each error, and the two places where rounding must not be applied.

## Safe shortcuts

| Exact value | Rounded value | Error | Direction |
|-------------|---------------|-------|-----------|
| 86,400 seconds per day | 100,000 | 13.6 percent | Rates come out too low |
| 2,592,000 seconds per month | 2.5 million | 3.7 percent | Rates come out slightly too high |
| 31,536,000 seconds per year | 30 million | 4.9 percent | Totals come out slightly too low |
| 365 days per year | 400 | 9.6 percent | Totals come out too high |
| 1,024 | 1,000 | 2.4 percent | Sizes come out slightly too low |
| 8 bits per byte | 10 | 25 percent | Bandwidth comes out too high |

The last row is acceptable only for a first figure. Dividing by 8 exactly is usually no harder.

## Work in powers of ten

Write every quantity as a small number multiplied by a power of ten. Multiplication then becomes addition of exponents.

```
Question    200 million users x 5 actions x 2 KB.
Rewrite     (2 x 10^8) x 5 x (2 x 10^3) bytes.
Multiply    20 x 10^11 bytes, which is 2 x 10^12 bytes.
Result      2 TB.
```

| Power of ten | Count | Bytes |
|--------------|-------|-------|
| 10^3 | thousand | KB |
| 10^6 | million | MB |
| 10^9 | billion | GB |
| 10^12 | trillion | TB |
| 10^15 | quadrillion | PB |

## One significant digit

Keep one significant digit during the calculation, and two at most in the final answer. A result of 36.5 TB should be spoken as "about 40 TB" or "about 36 TB". Stating "36.5 TB" suggests a precision that the inputs do not have.

## Round in the conservative direction

Capacity planning should err toward more capacity. When the direction is a choice, round traffic and storage upward and capacity per node downward.

| Quantity | Round |
|----------|-------|
| Users, requests, record sizes | Up |
| Peak factor | Up |
| Capacity of one node | Down |
| Compression ratio | Down |

## Where not to round

- **Do not round a ratio near a decision point.** If the estimate is 900 writes per second and the assumed capacity is 1,000, rounding either figure decides the outcome. State that the result is close to the limit instead.
- **Do not round across units by habit.** Megabits and megabytes differ by a factor of 8, and milliseconds and microseconds by 1,000. Confirm the unit before rounding the number.

## Compensating errors

Two rounding errors in opposite directions partly cancel. Dividing a yearly total by 400 days and then by 100,000 seconds understates the rate by about 21 percent. Using 365 with 100,000 understates it by 13.6 percent. Neither changes an order of magnitude, but three or four errors in the same direction can reach a factor of two.

## A quick check for any result

Compare the final figure with a known reference point. One million requests per day is about 12 per second. One billion records of 1 KB occupy 1 TB. A result that disagrees with these by a factor of 1,000 contains a unit error.

## Go deeper

- [Grokking the System Design Interview](https://www.designgurus.io/course/grokking-the-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=cheat-sheets-rounding-rules) shows estimation performed aloud inside complete designs.
- Lesson: [What Is Back-of-the-Envelope Estimation](https://www.designgurus.io/course-play/grokking-system-design-fundamentals/doc/what-is-backoftheenvelope-estimation?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=cheat-sheets-rounding-rules), in Grokking System Design Fundamentals
- [Time conversions](../numbers/time-conversions.md) and [powers of two](../numbers/powers-of-two-and-data-sizes.md) list the exact values behind these shortcuts.
