---
layout: post
title: "Which one to choose between Postgres and MySQL"
category: [Database]
tags: [database]
---

This depends on your application.

## Choose Postgres
**Frequent Write operation**
#### why?
Postgres has secondary index architecture. So re-balancing operation cost of insert & delete is cheaper than Clustered index. \
However, postgres create a process with large memory (almost 10 MB) per connection. This is the cause of lower speed in read operation comparing to MySQL using thread.
Also, Massive read operation is inefficient by random IO because postgres only supports non-clustered index.

Refer to [Clustered index vs non-clustered index](https://milkcoke.github.io/posts/Clustered_vs_Non-Clustered/)
## Choose MySQL
High volumes of reads. \
MySQL supports clustered index, so massive read is more efficient than Postgres. \
Random I/O is can be avoided.

MySQL has two types threads, foreground and background

### MySQL's Threads
#### Foreground thread
Read operation from buffer or cache. \
Borrow a thread from thread pool. \
A thread request connection to Connection pool, if there's not available (idle) connection it should wait for other thread to return the connection.
> ⚠️ MySQL community edition doesn't support connection pool.

#### Background thread
Usually, Do write operation especially between buffer <-> disk.

- Merge inserting buffers.
- Logging to disk
- Monitoring Lock & Deadlock

---

## 🔗 References
- [Postgres architecture - postgres.org](https://www.postgresql.org/docs/current/tutorial-arch.html)
- [Postgres vs MySQL](https://hevodata.com/learn/postgresql-vs-mysql/#f2)
- [DB Connection Pool - tistory](https://hyuntaeknote.tistory.com/12)
