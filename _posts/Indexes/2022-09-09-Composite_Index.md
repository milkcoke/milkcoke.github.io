---
layout: post
title: "How to decide composite index"
category: [Database]
tags: [database]
---

## What kind of columns are to be included in composite index?

Let me introduce my principal about how to choose column of composite index.

#### 1. WHERE clause
#### 2. Always used by '='
**If leading colum condition is not 'equal(=)' next columns would not use index scan.**

```sql
SELECT col1, col2, col3, col4
FROM table_name
WHERE col1 = ? AND col2 = ?
    -- col1 ~ col3 are to use index scan but col4 be not. (just filtering)
    -- since not equal ('=') operation is executed before.
    BETWEEN col3 ? AND ?
    AND col4 = ?
```

#### 3. Lower selectivity column is leading
Because real world's queries are executed like below. \
> Main category -> middle category -> subcategory

#### 4. Location column (condition, ex. ID) is followed by order column (ex. DATE)

#### 5. Use `IN` instead of between
You can take benefit of index when to use `IN` instead of BETWEEN \
`IN` means internally operates combination ('=' + OR)
```sql
SELECT id, name
FROM member WITH(users_id_index) -- index 사용 강제 가능.
WHERE IN id IN (1, 3, 10, 15) -- id == 1 || id == 3 || id == 10 || id == 15
```

#### 6. Use minimum the number of columns in index

#### 7. Sync Composite key ordering with `ORDER BY` sequence.
Sync all same sequence or on other way sequence. \
If not, more data access and sorting occurs.
