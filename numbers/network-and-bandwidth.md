# Network and bandwidth

Bandwidth is the amount of data a network link carries per second. It is the limiting resource for systems that move large objects, such as video delivery, backups, and crawlers. The first rule is a unit conversion: network speeds are quoted in bits, storage sizes in bytes, and one byte is 8 bits.

## Bits and bytes

| Link speed | Bytes per second | Data per day at full use |
|------------|------------------|--------------------------|
| 1 Mbps | 125 KB | about 10.8 GB |
| 100 Mbps | 12.5 MB | about 1.08 TB |
| 1 Gbps | 125 MB | about 10.8 TB |
| 10 Gbps | 1.25 GB | about 108 TB |
| 100 Gbps | 12.5 GB | about 1.08 PB |

Mbps means megabits per second, and Gbps means gigabits per second. To convert a speed in bits to bytes, divide by 8.

## The bandwidth formula

```
Bandwidth = requests per second x average response size
```

| Requests per second | Response size | Bandwidth |
|---------------------|---------------|-----------|
| 1,000 | 10 KB | 10 MB per second, or 80 Mbps |
| 10,000 | 100 KB | 1 GB per second, or 8 Gbps |
| 100,000 | 200 KB | 20 GB per second, or 160 Gbps |

For streams, the formula uses concurrent viewers and bitrate instead: bandwidth equals the number of concurrent streams multiplied by the bitrate of one stream.

## Typical stream bitrates

| Stream | Working bitrate |
|--------|-----------------|
| Voice call | 50 kbps |
| Music | 128 to 256 kbps |
| Video at 480p | 1 Mbps |
| Video at 720p | 2.5 Mbps |
| Video at 1080p | 5 Mbps |
| Video at 4K | 15 to 20 Mbps |

These values are working assumptions. Actual bitrates depend on the codec, which is the algorithm that compresses the stream, and on the content.

## Transfer times

| Data | Link | Time |
|------|------|------|
| 1 GB | 100 Mbps | 80 seconds |
| 1 TB | 1 Gbps | about 2.2 hours |
| 1 PB | 10 Gbps | about 9.3 days |

Large migrations are constrained by these times. Moving 1 PB between regions takes more than a week on a dedicated 10 Gbps link, so the migration plan must run the old and new systems together during the copy.

## Round trips cost more than bytes

For small requests, latency is dominated by round trips, not by bandwidth. The [latency table](latency.md) gives 0.5 ms inside a data center and 150 ms between continents. A 2 KB response takes the same time on a 100 Mbps link and a 10 Gbps link, because the transfer completes within one round trip in both cases. Bandwidth becomes the constraint only when the object is large or the request rate is high.

## Worked example

A photo service serves 10 billion image views per day at an average of 200 KB per image.

```
Views per second   10,000,000,000 / 86,400   = about 116,000
Bytes per second   116,000 x 200 KB          = about 23 GB
Bits per second    23 GB x 8                 = about 185 Gbps
```

No single data center link should carry 185 Gbps of image traffic. The result selects a content delivery network, or CDN, which is a set of servers near users that store copies of static files. The origin then serves only the requests that the CDN has not stored.

## Go deeper

- [Grokking the System Design Interview](https://www.designgurus.io/course/grokking-the-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=numbers-network-and-bandwidth) covers CDN placement and media delivery in its video and photo designs.
- [Video streaming](../estimations/video-streaming.md) and [web crawler](../estimations/web-crawler.md) are the two estimations where bandwidth decides the design.
- Free article: [CDN Architecture in System Design: How Content Delivery Networks Work](https://www.designgurus.io/blog/content-delivery-network-cdn-system-design-basics?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=numbers-network-and-bandwidth), the component that carries most of the bandwidth for large objects.
