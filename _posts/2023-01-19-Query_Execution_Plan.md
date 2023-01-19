---
layout: post
title: "Query execution plan"
category: [Database]
tags: [database]
---

## PostgreSQL

| Type            | Description                                                                              |
|-----------------|------------------------------------------------------------------------------------------|
| Seq scan        | Full scan <br> When index doesn't exist, or expected rows are large (greater than 5~10%) |
| Index scan      | Search by key and retrieves data located in heap (Always RID Lookup)                     |
| Index only scan | Search only keys (covering index)                                                        |

Postgres uses only non-clustered index (secondary index). \
RID lookup always occurs when executing index scan.

## SQL Server

| Type                     | Description                                                                                               |
|--------------------------|-----------------------------------------------------------------------------------------------------------|
| Table scan               | Full scan <br> when table without a clustered index is accessed.                                          |
| Clustered Index scan     | Clustered index exists but query can't use non-clustered index                                            |
| Clustered Index seek     | Scan limited set of rows using B tree structure clustered index.                                          |
| Non-Clustered Index scan | Table has non-clustered index but query requires accessing a large amount of data across particular index |
| Non-Clustered Index seek | Query accesses data using B+ tree index (No Lookup, Covering index)                                       |
| Lookup                   | Query requires accessing not exists in B+ tree <br>=> RID/Key lookup <br>=> Random I/O occurs.            |


> 📝 Clustered Index scan is not good because even though the table has clustered index, it's not used for searching really. \
> This would lead to a performance degradation. \
> In SQL Server, Index scan and table scan are not so different in terms of performance.

---

## 🔗 References
- [Youtube](https://youtu.be/TRfVeco4wZM)
- [SQL Server clustered index operators](https://www.sqlshack.com/sql-server-query-execution-plan-beginners-clustered-index-operators/)
- [SQL Server non-clustered index operators](https://www.sqlshack.com/sql-server-query-execution-plans-beginners-non-clustered-index-operators/)
- [Key lookup and RID lookup in SQL Server](https://www.pragimtech.com/blog/sql-optimization/key-lookup-and-rid-lookup-in-sql%20server/)
