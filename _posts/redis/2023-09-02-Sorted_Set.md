---
layout: post
title: "Redis sorted set"
category: Redis
tags:
  - Redis
---

## When to use
- Enforce uniqueness of elements
- Enforce sort \
![Enforce sort](/assets/img/redis/enforce-sort-sortedset.svg)
- Needs range based query
- Tabulating a collection in order
- Creating relationships between entities sorted by some criteria \
![Relationship between entities](/assets/img/redis/sortedset-join-entity.svg)

## Range based query
Z* operations range has inclusive semantic. \
And those has zero-based index.

### ZCOUNT
#### inclusive semantic (default)
```redis
ZADD products 5 mouse
ZADD products 10 keyboard
ZADD products 25 monitor
ZADD products 40 operating_system
ZADD products 100 cpu

ZCOUNT products 10 25
-- 2 (keyboard: 10, monitor: 25)
```

#### exclusive semantic
```redis
ZCOUNT products (10 25
-- 1 (monitor: 25)
```

### ZRANGE
`ZRANGE` is very useful and often used operation.
```redis
ZRANGE products 1 1 WITHSCORES
-- [keyboard, 10]
```

#### score-based search
```redis
ZRANGE products 10 30 BYSCORE WITHSCORES
-- [keyboard, 10, monitor, 25]
```

#### index-based search
```redis
-- Reverse order before query
ZRANGE products 1 1 REV WITHSCORES
-- [operating_system, 40]
```

## Tips
Redis is key value based NoSQL. \
It's allowed to use duplication for reducing query and enhancing performance.

Suppose we need provide most views on items.\
We don't need only use Hash data which has `${item:id}` as key. \
We can also use Sorted Set for reducing query and get the highest views items just one query `items:views` like as shown below.

![duplication is fine](/assets/img/redis/redis-duplication-is-fine.svg)
