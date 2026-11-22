# Estimation method

A back-of-the-envelope estimation follows the same five steps for every system. The steps take three to five minutes in an interview. The purpose is to find the one or two numbers that decide the design, so each step ends with a short conclusion.

## The five steps

| Step | Calculation | Typical inputs |
|------|-------------|----------------|
| 1. State assumptions | None | Daily active users, actions per user, record size, retention |
| 2. Traffic | Users x actions / 86,400, then x peak factor | Peak factor of 2 to 3 |
| 3. Storage | Records per day x size x retention x copies | 3 copies, 1 to 5 years |
| 4. Bandwidth and memory | Rate x response size, and 20 percent of daily reads | Response size, cache share |
| 5. Design consequence | Compare each result with the capacity of one node | See [component capacity](../numbers/component-capacity.md) |

## Step 1: state assumptions

Ask for the scale, and propose a figure when the interviewer gives none. Write every assumption where both people can see it. Round each input to one significant digit: 200 million users, not 187 million.

A sentence that works: "I will assume 100 million daily active users and 10 actions per user per day. Please correct me if the scale is different."

## Step 2: traffic

Compute requests per day, then the average rate, then the peak. Divide reads from writes when the ratio is far from 1 to 1.

```
Requests per day    100,000,000 x 10        = 1 billion
Average per second  1,000,000,000 / 86,400  = about 12,000
Peak per second     12,000 x 3              = 36,000
Writes at 10 to 1   36,000 / 11             = about 3,300
```

## Step 3: storage

Multiply the daily write count by the record size, then by retention and copies. Compute media separately from records, because media usually dominates.

```
Writes per day      1,000,000,000 / 11      = about 91 million
Per day             91,000,000 x 1 KB       = 91 GB
Per year            91 GB x 365             = about 33 TB
With 3 copies       33 TB x 3               = about 100 TB
```

## Step 4: bandwidth and memory

```
Response traffic    36,000 x 10 KB          = 360 MB per second, or 2.9 Gbps
Reads per day       1,000,000,000 x 10 / 11 = about 909 million
Cache               909,000,000 x 0.2 x 1 KB = about 180 GB
```

## Step 5: design consequence

Compare each result with what one node can handle, and say what follows.

| Result | Comparison | Consequence |
|--------|------------|-------------|
| 3,300 writes per second at peak | Above one relational node | Partition the data, or use a store built for writes |
| 33,000 reads per second at peak | Above one node | Cache, with read replicas behind it |
| 100 TB after one year | Above one node | Partitioning is also required for size |
| 180 GB of cache | Above one cache node | Divide the cache among several nodes |

## Time allocation

| Activity | Time |
|----------|------|
| Assumptions | 1 minute |
| Traffic | 1 minute |
| Storage | 1 minute |
| Bandwidth and memory | 1 minute |
| Consequences | 1 minute |

An estimate that takes more than five minutes reduces the time left for the design itself. If the interviewer shows no interest in the numbers, the candidate should state the two that matter and continue.

## When to skip a step

- Skip bandwidth when responses are small and the rate is low.
- Skip the cache estimate when the system is dominated by writes.
- Never skip the peak write rate, because it decides whether partitioning is required.

## Go deeper

- [Grokking the System Design Interview](https://www.designgurus.io/course/grokking-the-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=cheat-sheets-estimation-method) teaches this step as part of a complete interview framework, with worked designs.
- Lesson: [What are Back-of-the-Envelope Estimations?](https://www.designgurus.io/course-play/grokking-the-system-design-interview/doc/what-are-backoftheenvelope-estimations?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=cheat-sheets-estimation-method), in Grokking the System Design Interview
- Lesson: [Estimating QPS, Storage, and Bandwidth](https://www.designgurus.io/course-play/grokking-system-design-fundamentals/doc/estimating-qps-storage-and-bandwidth?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=cheat-sheets-estimation-method), in Grokking System Design Fundamentals
- Lesson: [A Worked Estimation Example](https://www.designgurus.io/course-play/grokking-system-design-fundamentals/doc/a-worked-estimation-example?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=cheat-sheets-estimation-method), in Grokking System Design Fundamentals
- [System Design Interview Crash Course](https://www.designgurus.io/course/system-design-interview-crash-course?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=cheat-sheets-estimation-method) is the compressed version for a short preparation period.
- The [worked estimations](../estimations/README.md) apply the method to ten systems.
- Free article: [Back-of-the-Envelope Estimation: A Step-by-Step Guide for System Design Interviews](https://www.designgurus.io/blog/back-of-the-envelope-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=cheat-sheets-estimation-method), the same method explained at full length.
