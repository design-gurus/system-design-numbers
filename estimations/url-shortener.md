# Estimation: URL shortener

A URL shortener stores a mapping from a short key to a long address and redirects visitors. The estimate shows a system that is small in storage and heavy in reads. The numbers point to a cache and a seven-character key, and they show that partitioning is not required for storage.

## Assumptions

| Input | Value |
|-------|-------|
| New short URLs per month | 100 million |
| Read-to-write ratio | 100 to 1 |
| Size of one record | 500 bytes |
| Retention | 5 years |
| Peak factor | 3 |
| Copies of each record | 3 |

## Arithmetic

```
Writes per second    100,000,000 / 2,600,000    = about 40
Reads per second     40 x 100                   = 4,000
Peak reads           4,000 x 3                  = 12,000 per second
Records in 5 years   100,000,000 x 60 months    = 6 billion
Storage              6,000,000,000 x 500 B      = 3 TB
With 3 copies        3 TB x 3                   = 9 TB
```

Key length, using letters and digits (base 62):

```
6 characters   = about 57 billion keys     (6 billion uses 11 percent)
7 characters   = about 3.5 trillion keys   (6 billion uses 0.2 percent)
```

Cache size, storing the most requested 20 percent of one day of reads:

```
Reads per day     4,000 x 86,400           = about 346 million
Cached entries    346,000,000 x 0.2        = about 69 million
Cache memory      69,000,000 x 500 B       = about 35 GB
```

## Result

| Quantity | Value |
|----------|-------|
| Writes | 40 per second |
| Reads at peak | 12,000 per second |
| Storage after 5 years | 3 TB, or 9 TB with copies |
| Key length | 7 characters |
| Cache | about 35 GB |

## What the result changes

- **Storage is not the hard part.** Five years of data is 3 TB, which a small database cluster holds. The design does not need partitioning for capacity.
- **Reads dominate.** A peak of 12,000 reads per second exceeds one database node, while 40 writes per second is trivial. The design needs a cache in front of the database, and 35 GB fits in the memory of one cache node.
- **Seven characters, not six.** Six characters would leave the key space 11 percent full, so randomly generated keys would collide often.
- **Write path can be simple.** At 40 writes per second, a single primary node with replicas is sufficient, and key generation does not need to be distributed for throughput.

## Go deeper

- [Grokking the System Design Interview](https://www.designgurus.io/course/grokking-the-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=estimations-url-shortener) includes the complete URL shortener design, with key generation and redirection covered in detail.
- The sibling repository has a [URL shortener walkthrough](https://github.com/design-gurus/grokking-system-design/blob/main/questions/design-tinyurl.md) that starts from these numbers.
- [Powers of two and data sizes](../numbers/powers-of-two-and-data-sizes.md) lists the key-space sizes used above.
