# Storage and object sizes

A storage estimate is a count of objects multiplied by the size of one object. The count comes from the traffic estimate, and the size comes from this page. Every value here is a rough working assumption that should be stated aloud, since real sizes vary by product and encoding.

## Small values

| Item | Working size | Notes |
|------|--------------|-------|
| One character of English text | 1 byte | Other scripts need 2 to 4 bytes in UTF-8 |
| Boolean or small enumeration | 1 byte | |
| 32-bit integer | 4 bytes | |
| 64-bit integer or identifier | 8 bytes | |
| Timestamp | 8 bytes | |
| UUID, a 128-bit identifier | 16 bytes | 36 bytes when stored as text |
| IPv4 address | 4 bytes | |
| Typical URL | 100 bytes | |
| One line of an application log | 200 bytes | |

## Records

| Item | Working size | Composition |
|------|--------------|-------------|
| Short text post with metadata | 1 KB | About 250 bytes of text plus identifiers, timestamps, and counters |
| Chat message | 100 bytes | A short text, two identifiers, and a timestamp |
| User profile row | 1 KB | Name, contact fields, settings |
| Location update | 100 bytes | Identifier, coordinates, heading, timestamp |
| Email without attachments | 50 KB | Headers and formatted body |

## Media

| Item | Working size | Notes |
|------|--------------|-------|
| Thumbnail image | 20 KB | |
| Image resized for a web page | 200 KB | |
| Photo from a phone, compressed | 2 MB | |
| One minute of audio at 128 kbps | 1 MB | |
| One minute of video at 1080p | 40 MB | Derived from a bitrate of about 5 Mbps |
| One hour of video at 1080p | 2.4 GB | |

Bitrate is the number of bits a stream uses per second. The video figure follows from the arithmetic: 5 megabits per second for 60 seconds is 300 megabits, which is 37.5 MB, rounded to 40 MB.

## Multipliers that are easy to forget

| Multiplier | Typical factor | Reason |
|------------|----------------|--------|
| Replication | 3x | Three copies are a common default for durability |
| Indexes | 1.2x to 2x | Each secondary index stores keys again |
| Several renditions of media | 2x to 3x | Video is stored at several resolutions |
| Compression of text and logs | 0.2x to 0.5x | Text compresses well, while photos and video are already compressed |
| Free space for operations | 1.3x | Disks should not run above about 75 percent full |

## Worked example

A service stores 50 million photos per day. Each upload keeps the 2 MB original and three resized versions that total 0.5 MB.

```
Per photo          2 MB + 0.5 MB        = 2.5 MB
Per day            50,000,000 x 2.5 MB  = 125 TB
Per year           125 TB x 365         = about 45.6 PB
With 3 copies      45.6 PB x 3          = about 137 PB
Metadata per day   50,000,000 x 500 B   = 25 GB
```

The image data is 5,000 times larger than the metadata. That ratio justifies storing the two separately: images in object storage, which is a service for large unstructured files, and metadata in a database. The [photo sharing estimation](../estimations/photo-sharing.md) continues from here.

## Hot and cold data

Most stored data is rarely read. A common assumption is that 20 percent of the data receives 80 percent of the reads. A cache sized at 20 percent of one day of read traffic is therefore a reasonable first figure, and old data can move to cheaper and slower storage.

## Go deeper

- [Grokking the System Design Interview](https://www.designgurus.io/course/grokking-the-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=numbers-storage-and-object-sizes) applies these sizes in designs for photo, video, and messaging systems.
- [Powers of two and data sizes](powers-of-two-and-data-sizes.md) gives the multiplication table for counts and sizes.
