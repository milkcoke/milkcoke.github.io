---
layout: post
title: "When you meet huge table"
category: [Database]
tags: [database]
---

## When you work with huge table

### [CASE I] Brute force
Brute forcing your way to work on the table.

If you tried to find a row in the table without indexing \
which is do multi-processing, multi-threading parallely.


### [CASE II] Indexing
Can I avoid processing entire table?
> 📝 Best approach is indexing


### [CASE III] Partitioning
Partitioning the table on disk breaking into smaller size,  multiple parts \
Separate location of data logically.


### [CASE IV] Sharding
Have multiple hosts. \
Divide disk , Separate data physically. \
You can reduce the size of table physically but complexity would increase.

(Size descending) Shard -> Partition -> Index


## Conclusion
Before applying like this solution, Avoid having a billion row table. \
That's the first thing

You can't avoid it? Use indexing, partitioning, sharding. (Like DynamoDB)


[Twitter]
single profile table,
id, name, follower integer, follower , following on the write level.

Update these things everytime someone follow / following

