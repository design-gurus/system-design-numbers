# Estimation: chat messaging

A chat service delivers short messages between users in real time and stores the history. The estimate produces two separate limits: the number of open connections and the volume of stored messages. Each limit leads to its own tier in the design.

## Assumptions

| Input | Value |
|-------|-------|
| Daily active users | 500 million |
| Messages sent per user per day | 40 |
| Size of one message with metadata | 100 bytes |
| Share of daily users connected at peak | 20 percent |
| Open connections per gateway server | 100,000 |
| Peak factor | 2 |
| Copies of each message | 3 |

## Arithmetic

```
Messages per day      500,000,000 x 40            = 20 billion
Messages per second   20,000,000,000 / 86,400     = about 230,000
Peak messages         230,000 x 2                 = 460,000 per second
```

Storage:

```
Per day          20,000,000,000 x 100 B           = 2 TB
Per year         2 TB x 365                       = 730 TB
With 3 copies    730 TB x 3                       = about 2.2 PB
```

Connections:

```
Open connections    500,000,000 x 0.2             = 100 million
Gateway servers     100,000,000 / 100,000         = 1,000
```

## Result

| Quantity | Value |
|----------|-------|
| Messages at peak | 460,000 per second |
| New storage | 2 TB per day, 730 TB per year |
| Storage with copies | about 2.2 PB per year |
| Open connections at peak | 100 million |
| Gateway servers | about 1,000 |

## What the result changes

- **Connections need a dedicated tier.** Holding 100 million open connections takes about 1,000 gateway servers. These servers hold connections and forward messages, and they should not also run business logic.
- **A routing table is required.** With 1,000 gateways, a sender and a recipient are almost never on the same server. The system needs a lookup from user to gateway, kept in a fast shared store.
- **The message store must accept very high write rates.** A peak of 460,000 writes per second is several hundred times the working capacity of one relational node. The store should be partitioned by conversation and optimized for appends.
- **Storage grows by petabytes.** At 2.2 PB per year, retention policy and compression are design topics, not details.

Media is excluded here. If 5 percent of messages carried a 200 KB image, images would add 200 TB per day, which is 100 times the text volume.

## Go deeper

- [Grokking the System Design Interview](https://www.designgurus.io/course/grokking-the-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=system-design-numbers&utm_content=estimations-chat-messaging) contains a complete messaging design, including delivery guarantees and presence.
- The sibling repository has a [chat walkthrough](https://github.com/design-gurus/grokking-system-design/blob/main/questions/design-whatsapp.md) for the complete design.
- [Component capacity](../numbers/component-capacity.md) explains the per-server connection assumption.
