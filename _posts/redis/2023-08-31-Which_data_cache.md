---
layout: post
title: "Which data is to be cached"
category: Redis
tags:
  - Redis
---

## Consideration design on redis
1. What data should be fetched
2. Data structure
3. Often accessed and common data among users \
This is common strategy when choosing cache data.
4. Not often changed data \
If you chosen cache data which is often changed data (especially for personalized data), in-memory space should be reduced very quickly. \

### Example
Suppose you have to select which web page should be cached using redis. \
You are administrator and owner of web site.

![Redis web page cache](/assets/img/redis/web-page-cache-redis.svg)

**Static web page is better choice.** \
These are the reason.

- Often accessed data
- Smaller size data
- Data not changed easily
- Not related business logic
