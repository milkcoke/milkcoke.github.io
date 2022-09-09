---
layout: post
title: "How to decide composite index"
category: [Database]
tags: [database]
---

## What column is to be included in composite index?

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
