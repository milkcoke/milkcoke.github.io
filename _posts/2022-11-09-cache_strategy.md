---
layout: post
title: "Cache Strategy"
category: [cache]
tags: [cache]
---

## 1. Cache Aside
![Cache Aside](/assets/img/cache_strategy/cache_aside.png)
Application update cache data only when cache miss occurs reading from database

#### When to use
Massive read workload

### Pros
- Have elasticity in error since cache memory is separated even though cache cluster is down
- Can have different data schema

### Cons
- Synchronization issue
  Not always sync cache data with database

- Slow when cache miss occurs

## 2. Read Through
Database update cache when cache miss

![Read Through](/assets/img/cache_strategy/read_through.png)

#### When to use
Massive read workload

### Pros
- Always data is synchronized if using with Write-Through

### Cons
- Data schema should follow database entity schema
- First request data always meets cache miss

## 3. Write Through
Application write all data in cache first and synchronized with db

![Write Through](/assets/img/cache_strategy/write_through.png)

#### When to use
All cache data must be synchronized.

### Pros
- Always data is synchronized between cache and DB

### Cons
- All data is in cache even though which is not used.
- Write operation performance degradation since all write operations must be hit DB


## 4. Write Behind

![Write Behind](/assets/img/cache_strategy/write_behind.png)

#### When to use
Massive write workload \
R/W work load \
Read through + Write Behind strategy => Fast R/W and can get synchronized data

### Pros
- Fast write operation
- Using batch write from cache to DB, can reduce db write operations

### Cons
- Not strong consistency between cache and DB
- Exists data loss probability when cache error occurs


## ⚙️ Related tools
#### Redis Gears
You can use [Redis Gears](https://github.com/RedisGears/rgsync)  when to use Write-Through or Write-Behind strategy.

#### DAX
You can use [DAX](https://aws.amazon.com/ko/dynamodb/dax/) when to use Read-Through + Write-Through strategy.




