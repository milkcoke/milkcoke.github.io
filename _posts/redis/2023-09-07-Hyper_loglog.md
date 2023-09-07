---
layout: post
title: HyperLogLog
category: Redis
tags:
  - Redis
---

# HyperLogLog
HyperLogLog is an algorithm for the count distinct problem. \
Redis provides HyperLogLog algorithm string data \
**It only uses 12 KB.**

## When to use?
**Guarantee approximate uniqueness when handle large dataset using small memory**

## How to use?

### PFADD
#### Example
```redis
-- It's like a set, doesn't actually store true value
PFADD <string | number>
```
Duplicate -> 0 (false) \
Unique -> 1 (true)

### PFCOUNT
```redis
-- Get the approximate count of key.
PFCOUNT <key>
```

## Practical example

#### Requirements
Suppose we need to guarantee uniqueness of item views. \
So, It's not allowed to increment many view counts by same user.

#### Simple solution
We can think about simple solution using `SET` data structure keeping username of items:views
![Set for item views](/assets/img/redis/redis-without-hyperloglog.svg)

This solution guarantee exact number of view count. \
**However, requires many memory usage even though it's simple feature**

#### HyperLogLog Solution

![HyperLogLog for item views](/assets/img/redis/redis-with-hyperloglog.svg)\
This solution requires only 12KB memory usage.

![HyperLogLog error](/assets/img/redis/redis-hyperloglog-error.svg)\
However, it has error rate approximately 0.81%.

This solution is appropriate than SET solution since it's important reduce redis memory usage and it's not much important counting views exactly.


## Conclusion
Consider using HyperLogLog for ensuring uniqueness with some error. \
It depends on your feature. \
It's appropriate when handle count views, likes etc.

Don't use it when you need guaranteeing  exact uniqueness like username , userId , email etc.










