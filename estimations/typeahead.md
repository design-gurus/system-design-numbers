# Estimation: typeahead

Typeahead, also called autocomplete, suggests complete queries while a user is still typing. Each search produces several suggestion requests, and each must return quickly enough to feel instant. The estimate combines a very high request rate with a strict latency limit, and both shape the design.

## Assumptions

| Input | Value |
|-------|-------|
| Searches per day | 5 billion |
| Suggestion requests per search | 6 |
| Size of one response (10 suggestions) | 500 bytes |
| Distinct prefixes stored | 200 million |
| Size of one prefix entry | 100 bytes (a 20-byte key and 10 identifiers of 8 bytes) |
| Latency target | 100 ms, as seen by the user |
| Peak factor | 2 |

A prefix is the leading part of a query, such as "sys" for "system design".

## Arithmetic

Traffic:

```
Requests per day       5,000,000,000 x 6             = 30 billion
Requests per second    30,000,000,000 / 86,400       = about 350,000
Peak per second        350,000 x 2                   = 700,000
```

Index size:

```
Memory     200,000,000 x 100 B                       = 20 GB
```

Bandwidth for responses:

```
Bytes per second    350,000 x 500 B                  = 175 MB
In bits             175 MB x 8                       = 1.4 Gbps
```

Servers, assuming 100,000 in-memory lookups per second per node:

```
Nodes at peak       700,000 / 100,000                = 7 per copy of the index
```

Latency:

```
Round trip between continents        = 150 ms   (exceeds the target)
Round trip within one region         = under 70 ms
Lookup in memory                     = under 1 ms
```

## Result

| Quantity | Value |
|----------|-------|
| Requests | 350,000 per second average, 700,000 at peak |
| Index | 20 GB in memory |
| Response bandwidth | 1.4 Gbps average |
| Serving nodes | at least 7 at peak, before spare capacity |

## What the result changes

- **The index fits in memory on every node.** At 20 GB, each serving node holds a complete copy. Requests need no database call, and nodes are added for throughput alone.
- **Suggestions are precomputed.** Storing the top 10 results with each prefix turns a request into one lookup. Ranking at request time would not sustain 700,000 requests per second.
- **Serving must be regional.** A round trip between continents takes 150 ms, which already exceeds the 100 ms target. Each region needs its own copy of the index.
- **The client reduces the load.** Sending a request only after the user pauses for a moment lowers the 6 requests per search. Halving that number halves the server count.
- **Updates are a batch process.** The index is rebuilt from search logs on a schedule and distributed to the nodes, since suggestions do not need to reflect the last minute.

## Go deeper

- [Grokking the System Design Interview](https://www.designgurus.io/course/grokking-the-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=estimations-typeahead) contains the complete typeahead design, including the prefix tree and ranking.
- Lesson: [Designing Typeahead Suggestion](https://www.designgurus.io/course-play/grokking-the-system-design-interview/doc/designing-typeahead-suggestion?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=estimations-typeahead), in Grokking the System Design Interview
- Lesson: [Design Typeahead/Autocomplete](https://www.designgurus.io/course-play/system-design-interview-crash-course/doc/design-typeaheadautocomplete?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=estimations-typeahead), in System Design Interview Crash Course
- The sibling repository has a [typeahead walkthrough](https://github.com/design-gurus/grokking-system-design/blob/main/questions/design-typeahead-autocomplete.md) for the complete design.
- [Latency numbers](../numbers/latency.md) explains the round-trip times used above.
