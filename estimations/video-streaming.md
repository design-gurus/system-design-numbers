# Estimation: video streaming

A video streaming service stores uploaded videos and delivers them to viewers. The estimate is dominated by bandwidth: peak delivery traffic reaches tens of terabits per second. Storage is also large, but it grows steadily and can be planned, while delivery must be served at every moment.

## Assumptions

| Input | Value |
|-------|-------|
| Daily active users | 100 million |
| Viewing time per user per day | 60 minutes |
| Average stream bitrate (1080p) | 5 Mbps |
| Videos uploaded per day | 1 million, averaging 5 minutes |
| Stored size per minute, all resolutions | 100 MB |
| Peak factor | 2 |

One minute of 1080p video at 5 Mbps is about 40 MB. The service stores each video at several resolutions, so the estimate uses 100 MB per minute.

## Arithmetic

Delivery:

```
Average concurrent viewers   100,000,000 x 60 / 1,440   = about 4.2 million
Average delivery traffic     4,200,000 x 5 Mbps         = about 21 Tbps
Peak delivery traffic        21 Tbps x 2                = about 42 Tbps
```

Tbps means terabits per second, which is 1,000 Gbps.

Storage:

```
Minutes uploaded per day   1,000,000 x 5              = 5 million
Storage per day            5,000,000 x 100 MB         = 500 TB
Storage per year           500 TB x 365               = about 183 PB
```

Processing:

```
Minutes uploaded per second   5,000,000 / 86,400      = about 58
```

## Result

| Quantity | Value |
|----------|-------|
| Concurrent viewers | 4.2 million average, 8.3 million at peak |
| Delivery traffic | 21 Tbps average, 42 Tbps at peak |
| New storage | 500 TB per day, about 183 PB per year |
| Upload processing | 58 minutes of video per second |

## What the result changes

- **Delivery must happen near the viewer.** A traffic level of 42 Tbps cannot leave a few central data centers. Video segments are served from a content delivery network, with servers placed inside or near the networks of internet providers.
- **Storage belongs in object storage with tiers.** At 500 TB per day, most videos are rarely watched after the first weeks. Older and unpopular videos move to cheaper storage classes.
- **Encoding is a batch workload.** Processing 58 minutes of video every second, at several resolutions, requires a large pool of workers fed by a queue. The pool scales with upload volume and is independent of viewing traffic.
- **Metadata is small.** One million videos per day at 1 KB each is 1 GB per day, so the catalog fits in an ordinary database.

## Go deeper

- [Grokking the System Design Interview](https://www.designgurus.io/course/grokking-the-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=estimations-video-streaming) contains the complete video platform design, including encoding and adaptive streaming.
- The sibling repository has a [video platform walkthrough](https://github.com/design-gurus/grokking-system-design/blob/main/questions/design-youtube.md) for the complete design.
- [Network and bandwidth](../numbers/network-and-bandwidth.md) lists the bitrates and link speeds used above.
