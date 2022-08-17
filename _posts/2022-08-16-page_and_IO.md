---
layout: post
title: "Required terms of database"
category: [Database]
tags: [database]
---

## Pages
The logical unit, that of database stores and reads the data. \
Different according to DBMS (ex. PostgreSQL has a 8KB page, MySQL does 16KB) \
DB handle data one or more pages in a single IO.


## IO
Operation unit a request to disk. \
Considered performance, developers should minimize this since this is expensive (on disk). \
Some OS(ex. PostgreSQL) use OS cache instead of disk.

## Index
**Physical method of storing data.**
- This is for searching data fast
- Consists of B-Tree data structure.
- Stored as pages.

B-Tree search operation Time Complexity is Log(N)

## Heap
Everything is stored in here (disk). \
More IO in this section, more cost and slower the queries.
