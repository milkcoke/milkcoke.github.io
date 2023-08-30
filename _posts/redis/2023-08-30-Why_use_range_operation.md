---
layout: post
title: "Redis multi key and range operation"
category: Redis
tags:
  - Redis
---

## Multi key operation

### MSET
Set multi key - value pairs
```bash
redis-cli>  MSET key1 value1 key2 value2
```

### MGET
Get multi values from multi keys order by key

```bash
redis-cli> MGET key1 key2
redis-cli> [value1, value2]
```

## GETRANGE, SETRANGE

> Two indexes means between range [start, end].

```bash
redis-cli> SET name falcon
# from 0 index to 2 index (including 2)
redis-cli> GETRANGE 0 2 # "fal"
```

### Why to use them?
It doesn't look useful. \
However, suppose we use traditional database table including multiple columns. \
There's some operations when we use update partial properties some rows.

- Fetch some column of a single row
- Update some column of a single row
- Create some rows with multiple columns

Especially, when using hashtable with encode value (ex. alphabet)

### Example
![Range operations](/assets/img/redis/redis-range-operation.svg)

## INCR / DECR
These operations are supported for number type. \
It seems that not necessary because we can implement using `GET + SET`

### Why to use them?
1. **Reduce network round trip** \
It costs two round trip if GET + SET operations are used. \
INCR / DECR require single operation
2. **Prevent from race condition** \
Race condition would occur.  when trying `SET` operation simultaneously by multiple servers. \
Using`INCR / DECR` operation resolve this problem since the Redis is based on single thread.
