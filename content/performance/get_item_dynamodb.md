+++
title = 'How Long Does It Take to Read a Row from DynamoDB?'
date = 2023-09-30T18:50:05+02:00
draft = false
+++

tl;dr: The answer is between 53 and 241 milliseconds. Read on to get the details!

The question, as it is posed, does not seem to hold much value. However, I still think it represents a valuable data point that would help developers and architects have a rough idea of what to expect when they need to read a single row from DynamoDB.

At cloud-canary.com, we consistently monitor the biggest AWS services to alert our customers when something goes wrong. This comes with a lot of interesting data that can be mined to answer questions like the one above.

But first, let's clarify the terminology.

You don't "read a row" from DynamoDB; instead, you "get an item". Data inside DynamoDB is stored in "tables", and each table is a collection of "items". The API to call is indeed named "GetItem".

Then, we need some clarity on the methodology, a.k.a., "how the data was collected".

At Cloud-Canary, we run custom scripts to collect our data. Our data is "user representative"; we don't really care what the internal AWS metrics say. What matters to us is how long the whole request takes.

Yes, such a request will incur network latency between the computation environment and DynamoDB, but so will all user workloads. All our canaries run in the same region as the service we are testing.

The specific canary for "GetItem" is rather simple: we just ask DynamoDB to provide us with an item from a specific table that we previously inserted. Each item is indexed by a UUIDv4 and contains 4kB of data. We start a timer just before sending the request and stop it just after the request is complete. Simple.

The specific and punctual data are available to our customers, but here is a useful aggregation.

| Percentile | Average Latency (ms) |
|------------|----------------------|
| 10th       | 53.8                 |
| 25th       | 88.4                 |
| 50th       | 124                  |
| 75th       | 168.5                |
| 99th       | 241                  |

The data is aggregated over 24 hours of today, 30th September 2023.

### Discussion

From the percentiles, we can see that half of the requests will take 124ms or less, while the other half will take 124ms or more. We can also observe that 1 in 100 requests will take more than 241ms.

Whether this is acceptable or not depends on your use case. Perhaps it’s not fast enough for a cache layer, but it might be sufficient for non-critical data. As a rule of thumb, anything below 200 ms is typically perceived as instantaneous by the human brain.
