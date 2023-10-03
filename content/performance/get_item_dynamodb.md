+++
title = 'How long does it take to read a row from DynamoDB?'
date = 2023-09-30T18:50:05+02:00
draft = false
+++

tl;dr: The answer is between 40 and 370 milliseconds. Read on to get the details!

The question, as it is posed, does not seem to hold much value. However, it encapsulates valuable data points that help developers and architects estimate what to expect when they need to read a single row from DynamoDB.

At [cloud-canary.com](https://cloud-canary.com), we consistently monitor the biggest AWS services to alert our customers when something goes wrong. This process comes with a lot of interesting data that can be mined to answer questions like the one above.

### Terminology

But first, let's clarify the terminology.

You don't "read a row" from DynamoDB; instead, you "get an item". Data inside DynamoDB is stored in "tables", and each table is a collection of "items". The API to call is indeed named "GetItem".

### Methodology

Then, we need some clarity on the methodology, a.k.a., "how the data was collected".

At [Cloud-Canary](https://cloud-canary.com), we run custom scripts to collect our data. Our data is "user representative"; we don't care what the internal AWS metrics say. What matters to us is how long the whole request takes.

Yes, such a request will incur network latency between the computation environment and DynamoDB, but so will all user workloads. All our canaries run in the same region as the service we are testing.

The specific canary for "GetItem" is rather simple: we request DynamoDB to retrieve an item from a specific table previously populated with data. Each item is indexed by a UUIDv4 and it contains 4kB of data. Timing begins just before sending the request and ends immediately after its completion. Simple.

The specific and punctual data are available to our customers, but here is a useful aggregation.

| Percentile | Average Latency (ms) |
|------------|----------------------|
| 1th        | 5.3                  |
| 10th       | 40.0                 |
| 25th       | 78.8                 |
| 50th       | 119.8                |
| 75th       | 179.9                |
| 99th       | 373.5                |
| 100th      | 539.2                |

The data is aggregated over 24 hours of today, 30th September 2023.

### Discussion

From the percentiles, we can see that half of the requests will take 120ms or less, while the other half will take 120ms or more. We can also observe that 1 in 100 requests will take more than 370ms. The longest request today took a whopping 540ms.

Whether this is acceptable or not depends on your use case. Perhaps it’s not fast enough for a cache layer, but it might be sufficient for non-critical data. As a rule of thumb, anything below 200 ms is typically perceived as instantaneous by the human brain.

### Performance analysis

You can check out all of our performances analysis on this link.

[Performances Analysis](/performance)

