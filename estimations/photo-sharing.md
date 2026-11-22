# Estimation: photo sharing

A photo sharing service stores uploaded images and shows them in feeds and profiles. The estimate separates two very different data sets: image files measured in petabytes and metadata measured in terabytes. That separation, together with the delivery traffic, shapes the architecture.

## Assumptions

| Input | Value |
|-------|-------|
| Daily active users | 100 million |
| Photos uploaded per day | 50 million |
| Stored size per photo | 2.5 MB (a 2 MB original and 0.5 MB of resized versions) |
| Metadata per photo | 500 bytes |
| Photos viewed per user per day | 100 |
| Average size of a served image | 200 KB |
| Copies of each file | 3 |

## Arithmetic

Uploads and storage:

```
Uploads per second    50,000,000 / 86,400         = about 580
Images per day        50,000,000 x 2.5 MB         = 125 TB
Images per year       125 TB x 365                = about 45.6 PB
With 3 copies         45.6 PB x 3                 = about 137 PB
Metadata per day      50,000,000 x 500 B          = 25 GB
Metadata per year     25 GB x 365                 = about 9.1 TB
```

Views and delivery:

```
Views per day         100,000,000 x 100           = 10 billion
Views per second      10,000,000,000 / 86,400     = about 116,000
Delivery traffic      116,000 x 200 KB            = about 23 GB per second
In bits               23 GB x 8                   = about 185 Gbps
```

## Result

| Quantity | Value |
|----------|-------|
| Uploads | 580 per second |
| Image storage | 125 TB per day, about 137 PB per year with copies |
| Metadata | 25 GB per day, about 9.1 TB per year |
| Views | 116,000 per second |
| Delivery traffic | about 185 Gbps |

## What the result changes

- **Files and metadata are stored separately.** Image data is 5,000 times larger than metadata. Images go to object storage, and metadata goes to a database that supports queries by user and time.
- **The metadata database needs partitioning over time.** It grows by 9.1 TB per year before indexes, which exceeds a comfortable size for one node within the first year.
- **Delivery requires a content delivery network.** At 185 Gbps and 116,000 views per second, serving images from the origin is not practical. Images never change after upload, so CDN servers can keep copies for long periods.
- **Uploads are not the constraint.** At 580 per second, the upload path is modest. Resizing can run asynchronously after the upload is acknowledged.
- **The read-to-write ratio is 200 to 1.** Views outnumber uploads so heavily that the read path deserves most of the design time.

## Go deeper

- [Grokking the System Design Interview](https://www.designgurus.io/course/grokking-the-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=estimations-photo-sharing) contains the complete photo sharing design, including feed generation.
- Lesson: [Designing Instagram](https://www.designgurus.io/course-play/grokking-the-system-design-interview/doc/designing-instagram?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=estimations-photo-sharing), in Grokking the System Design Interview
- The sibling repository has a [photo sharing walkthrough](https://github.com/design-gurus/grokking-system-design/blob/main/questions/design-instagram.md) for the complete design.
- [Storage and object sizes](../numbers/storage-and-object-sizes.md) lists the image sizes assumed here.
- Free article: [How to Design Instagram: A System Design Interview Walkthrough](https://www.designgurus.io/blog/how-to-design-instagram-in-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=estimations-photo-sharing), the design that these numbers size.
