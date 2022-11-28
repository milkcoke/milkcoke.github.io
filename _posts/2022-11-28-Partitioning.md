---
layout: post
title: "Database Partitioning"
category: [cache]
tags: [cache]
---


## What's Partitioning
Split the big table into multiple logical tables in the same table.

## When to use Partitioning?
If table size is bigger and bigger , Indexes should being large, **the queries could slower than before** \

**Partition the table with index!** \
=> Query to small size indexes so targeting specific partitioned table! \
=> **Faster than whole index scanning!**


### Horizontal vs Vertical Partitioning
#### Horizontal
Split rows range, list, hash based.

#### Vertical
Splits columns partitions (Not recommended)

## Partitioning Types
#### Range
Dates, ids\
(e.g. `year` of logDateTime , `userId`)

ex) If you don't use log date before `2015 years`, You can archive old partitions to cheap storages.

#### List
Discrete values \
ex) zip codes


#### Hash
Hash function decides where to store the data.

Cassandra, DynamoDB uses this strategy as default.


### ⚠️Partitioning is not Sharding
> Sharding splits big table into multiple physical tables across multiple DB Servers.
> On the other hand, Partitioning have multiple logical tables in same table in same server.
