---
layout: post
title: Set data type
category: Redis
tags:
  - Redis
---

## When to use
- Enforce uniqueness of elements
- Creating relationship between other entity \
![Benefits from set example](/assets/img/redis/set-usecase-example.svg)

- Common elements between other entity \
![Benefits from set example2](/assets/img/redis/set-usecase-example_2.svg)
- Guarantee operation idempotence
- Order of elements doesn't matter. \
ex) ip black list.


## Tips
Why do we need store operation?
- `SINTERSTORE`
- `SDIFFSTORE`
- `SUNIONSTORE`

Reduce the number of execution those operation. \
Those operations' cost is expensive.

