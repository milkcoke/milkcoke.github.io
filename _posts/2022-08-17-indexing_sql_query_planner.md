---
layout: post
title: "Indexing with query planner"
category: [Database]
tags: [database]
---


### Configuration
Tested on DBMS: PostgreSQL 14.4

#### Table DDL
```postgresql
create table users
(
    id   integer generated always as identity
        constraint user_id_pk
            primary key,
    name varchar(15)
);
```
Suppose already inserted 1 million rows in the table.

### Seq scan
Querying with no index column `seq scan` executed.

```postgresql
EXPLAIN (analyze, buffers) SELECT id, name
FROM users
WHERE name = 'PLVCE UHIP';
```

#### Result
```text
Gather  (cost=1000.00..12129.43 rows=1 width=15) (actual time=0.437..43.940 rows=1 loops=1)
Workers Planned: 2
Workers Launched: 2
Buffers: shared hit=5668
# 💡Sqeq scan occurs!
->  Parallel Seq Scan on users  (cost=0.00..11129.33 rows=1 width=15) (actual time=23.162..36.439 rows=0 loops=3)
Filter: ((name)::text = 'PLVCE UHIP'::text)
Rows Removed by Filter: 349525
Buffers: shared hit=5668
Planning:
Buffers: shared hit=4
Planning Time: 0.081 ms
# 💡Slow query
Execution Time: 43.969 ms
```



### Use `index scan` for efficient query

```postgresql
-- create index on `name`
CREATE INDEX users_name_idx on users(name);
-- DBMS decide to use 'index scan' since it has index column (name)
EXPLAIN (analyze, buffers) SELECT id, name
FROM users
WHERE name = 'PLVCE UHIP';
```

#### Result
```text
# ✅ Index scan occurs!
Index Scan using users_name_idx on users  (cost=0.42..8.44 rows=1 width=15) (actual time=0.020..0.021 rows=1 loops=1)
  Index Cond: ((name)::text = 'PLVCE UHIP'::text)
  Buffers: shared hit=4
Planning:
  Buffers: shared hit=85
Planning Time: 0.358 ms
# ✅ Fast query!
Execution Time: 0.039 ms
```


### Don't use Like '%' query if you want fast query in general
like cause slow query since actually asking for 'expression' not a single 'value'. \
So, seq scan should be executed instead of index scan even if it has index.


```postgresql
EXPLAIN (analyze, buffers) SELECT id, name
FROM users
WHERE name LIKE '%PLVCE UHIP%';
```

#### Result
```text
Gather  (cost=1000.00..12139.83 rows=105 width=15) (actual time=0.771..42.900 rows=1 loops=1) \
Workers Planned: 2
Workers Launched: 2
Buffers: shared hit=5668
# 💡 Seq scan occurs!
->  Parallel Seq Scan on users  (cost=0.00..11129.33 rows=44 width=15) (actual time=23.480..36.384 rows=0 loops=3) \
Filter: ((name)::text ~~ '%PLVCE UHIP%'::text)
Rows Removed by Filter: 349525
Buffers: shared hit=5668
Planning:
Buffers: shared hit=6
Planning Time: 0.117 ms
# 💡 Slow query
Execution Time: 42.916 ms
```


## Terms of query planner

```text
Gather  (cost=1000.00..12129.43 rows=1 width=15) (actual time=1.664..59.150 rows=1 loops=1)
Buffers: shared hit=5668
Planning:
Buffers: shared hit=6
# ...
```

### What's buffers shared 'hit' and 'read'?

PostgreSQL internally use OS cache. \
You can check the cache size on the `shared_buffers` (default: 128MB)

#### Shared Hit
`Buffers: shared hit = 4` means that 4 pages (each 8KB) had to be read from the **main memory.**
#### Shared Read
`Buffers: shared read = 4` means that 4 pages had to be read from the **hard disk**

#### Cost
1st value `1000.00` means it took 1000 millisecond to reach at first page on heap. \
2nd value `12129.43` means total amount of which took 12129.43 millisecond to read data from heap. \
This is not actual time, just estimated value.

#### rows
Not an accurate number just statistical, approximate number
> ⚠️ Use this value instead of  COUNT() function since it would kill performance.

#### Width
Sum of all bytes for all the columns. \
Larger this value, larger TCP packet size.
