---
layout: post
title: "What's non-clustered Index"
category: [Database]
tags: [database]
---

## Primary key
Organized, clustered table. we call it **Index Organized Table (IOT)** \
Clustered index that is sequential is not that bad. So, it's ordered data and can use efficient caching mechanism. \
If not, memory in heap would jump left and right, That will not benefit of memory caching DB is doing.

## Secondary Key
This is used in non-clustered index. \
Have an additional outside index as B-Tree. It's not in table.

> ⚠️ Postgres doesn't have primary index. All indexes are secondary index. (Non-clustered index)


## Non-Clustered Index
- Located in separated memory section. \
  So, should jump to HEAP
- One or more exists in a table.
- Store value and a pointer to actual row that holds data.
- The leaf node has only included columns.
- Just logical order, not organized physically.
- Composite key when used with unique constraints of the table act as non-clustered index.

### How to create non-clustered index in DBMS
![Syntax](/assets/img/index/non_clustered_index_syntax.png)


### Secondary index in Postgres and MySQL

> ⚠️ Don't use big primary key in MySQL using secondary index because it keeps primary key in secondary index

![Secondary index result](/assets/img/index/secondary_index_create_example.png)
![Secondary index create](/assets/img/index/secondray_index_create_result.png)


---

## 🔗 Reference
- [Difference between clustered and non clustered index - GeeksForGeeks](https://www.geeksforgeeks.org/difference-between-clustered-and-non-clustered-index/?ref=rp)
