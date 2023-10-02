+++
title = 'Unveiling the Latency: How Long Does AWS S3’s GetObject Operation Take?'
date = 2023-09-30T18:50:05+02:00
draft = false
+++

tl;dr: The latency of AWS S3’s `GetObject` operation varies, ranging from 40 milliseconds up to around 1000 milliseconds. Let’s delve deeper into the details!

Understanding the time it takes to retrieve an object from an S3 bucket is not just a trivial matter; it’s crucial information for developers and architects aiming to build high-performance applications.

At [cloud-canary.com](https://cloud-canary.com), we are consistently monitoring major AWS services, ready to alert our customers at the first sign of any issues. This provides us with a wealth of data, perfect for answering questions like the latency of retrieving an object from S3.

### Our Methodology

Data collection at [Cloud-Canary](https://cloud-canary.com) is done through custom scripts designed to reflect user experience accurately. We’re not concerned with AWS’s internal metrics; our focus is on the actual time a request takes from initiation to completion.

Network latency between the computation environment and S3 is a factor, but it's one that affects all user workloads. For accuracy, our canaries are deployed in the same region as the tested service.

For the `GetObject` operation, the process is straightforward. We time the retrieval of an object from an S3 bucket, each object is 4kB in size. The timer starts just before the request is sent and stops upon completion.

Below is an aggregated snapshot of our findings:

| Percentile | Average Latency (ms) |
|------------|----------------------|
| 1st        | 40.4                 |
| 10th       | 79.3                 |
| 25th       | 99.5                 |
| 50th       | 120.8                |
| 75th       | 159.5                |
| 99th       | 339.2                |
| 100th      | 1001.9               |

This data is compiled over 24 hours on September 30th, 2023.

### Discussion

The percentile data reveals that half of the requests complete in 120.8 milliseconds or less, while the others half take more than 120ms. 

In our sample 1% of the requests took more than 340ms and the slowest request took a bit longer than 1 second.


