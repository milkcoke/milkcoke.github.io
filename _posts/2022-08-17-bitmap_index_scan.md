---
layout: post
title: "Bitmap Scan"
category: [Database]
tags: [database]
---

### Configuration
DBMS : PostgreSQL 14.4

#### Table Schema
```postgresql
create table users
(
    id      integer generated always as identity
        constraint user_id_pk
            primary key,
    name    varchar(15),
    score   integer
);
-- score range [0, 100]
CREATE INDEX users_score_idx
  ON users USING btree(score)
  INCLUDE (id);
```
Suppose already inserted 1 million rows in the table.

## Bitmap scan
### When to use
Expecting the result size is large, but need using index. \
DBMS would decides use bitmap scan.

```postgresql
EXPLAIN ANALYZE SELECT name
FROM users
WHERE score > 90;
```

#### Result
✅ Bitmap Scan!
```text
Bitmap Heap Scan on users  (cost=1962.80..9948.87 rows=104565 width=10) (actual time=28.306..99.511 rows=104072 loops=1)
  Recheck Cond: (score > 90)
  Heap Blocks: exact=6679
  ->  Bitmap Index Scan on users_score_idx  (cost=0.00..1936.66 rows=104565 width=0) (actual time=27.578..27.578 rows=104072 loops=1)
        Index Cond: (score > 90)
Planning Time: 0.076 ms
Execution Time: 103.958 ms
```

### How it operates?
Postgres makes a bitmap . \
When index condition is true in the specific page, marking true bit on bitmap (page number)\
Not going to jump directly to the heap as finding index.

Finally, after all marking the bitmap, \
can jump once to the heap table and pull all these pages.

**👉🏻 Lower page jump in heap, more efficient**

