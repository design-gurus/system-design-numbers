# Powers of two and data sizes

Storage and memory are counted in powers of two, while traffic is counted in powers of ten. The useful fact is that 2^10 is close to 1,000, so every ten powers of two add three decimal zeros. This page lists the units, the integer limits, and the sizes of common key spaces.

## Byte units

A bit is a single binary digit, and a byte is 8 bits.

| Power | Exact value | Approximation | Unit |
|-------|-------------|---------------|------|
| 2^10 | 1,024 | 10^3, one thousand | 1 KB (kilobyte) |
| 2^20 | 1,048,576 | 10^6, one million | 1 MB (megabyte) |
| 2^30 | 1,073,741,824 | 10^9, one billion | 1 GB (gigabyte) |
| 2^40 | about 1.1 x 10^12 | 10^12, one trillion | 1 TB (terabyte) |
| 2^50 | about 1.13 x 10^15 | 10^15, one quadrillion | 1 PB (petabyte) |

The approximation error grows with each step: 2.4 percent at 1 KB, 7.4 percent at 1 GB, and 12.6 percent at 1 PB. An estimate tolerates all of these, because the goal is the correct order of magnitude.

## Integer limits

| Type | Distinct values | What it can count |
|------|-----------------|-------------------|
| 8 bits (1 byte) | 256 | One text character in ASCII |
| 16 bits (2 bytes) | 65,536 | Network port numbers |
| 32 bits (4 bytes) | about 4.3 billion | IPv4 addresses, or seconds across 136 years |
| 64 bits (8 bytes) | about 1.8 x 10^19 | Any identifier a system will ever issue |

A 32-bit counter is too small for a table that may exceed 4.3 billion rows. A 64-bit identifier removes that limit at a cost of 4 additional bytes per row.

## Key-space sizes

A key space is the set of all values a key can take. Its size determines how long a generated key must be.

| Alphabet | Length | Distinct keys |
|----------|--------|---------------|
| Base 62 (letters and digits) | 6 | about 57 billion |
| Base 62 | 7 | about 3.5 trillion |
| Base 62 | 8 | about 218 trillion |
| Hexadecimal (base 16) | 8 | about 4.3 billion |
| 128-bit identifier such as a UUID | 32 hexadecimal digits | about 3.4 x 10^38 |

## Multiplication table for estimates

Most storage estimates are a count multiplied by a size. The table gives the result directly.

| Records | at 100 bytes | at 1 KB | at 1 MB |
|---------|--------------|---------|---------|
| 1 million | 100 MB | 1 GB | 1 TB |
| 1 billion | 100 GB | 1 TB | 1 PB |
| 1 trillion | 100 TB | 1 PB | 1,000 PB |

The pattern is simple to recall: a million records of 1 KB occupy 1 GB, and each factor of a thousand moves the unit one step.

## Worked example

A service must issue short keys for 6 billion records over five years.

```
Six characters of base 62    = about 57 billion keys
Records required             = 6 billion
Share of key space used      = 6 / 57 = about 11 percent
```

Six characters are sufficient in principle, but a space that is 11 percent full makes random keys collide often. Seven characters provide 3.5 trillion keys, so the space stays under 0.2 percent full. The estimate therefore selects a key length of seven. The full calculation is in the [URL shortener estimation](../estimations/url-shortener.md).

## Go deeper

- [Grokking the System Design Interview](https://www.designgurus.io/course/grokking-the-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=numbers-powers-of-two-and-data-sizes) applies key-space and storage arithmetic to complete designs.
- [Storage and object sizes](storage-and-object-sizes.md) lists the per-record sizes to multiply by.
