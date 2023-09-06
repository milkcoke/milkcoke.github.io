---
layout: post
title: Loading relational data
category: Redis
tags:
  - Redis
---

There's two solution for getting loading relational data



# (1) RANGE based query + GET query
Let me show you example code.

```typescript
export async function itemsByEndingTime(
  order: 'DESC' | 'ASC' = 'DESC',
  offset: number = 0,
  count: number = 10
): Promise<Item[]> {
  // First range-based query
  const itemIdList = await redisClient.zRange(generateItemEndingAtKey(), Date.now(), '+inf', {
    BY: 'SCORE',
    LIMIT: {
      offset: offset,
      count: count
    }
  });

  // Pipeline with two separated commands
  const results = await Promise.allSettled(itemIdList.map(itemId => redisClient.hGetAll(generateItemKey(itemId))));

  if (isExistsError(results)) {
    throw new Error("Error occurs when sorting items by ending time");
  }

  return results
    .filter(result => result.status === 'fulfilled')
    .map((item: PromiseFulfilledResult<any>, index)=> {
      return deserializeItem(itemIdList[index], item.value);
    });
}
```

#### Pros
Simple solution

#### Cons
This solution requires two redis operation (two network round trip)


# (2) Sort
Use the SORT command for loading relational data.
## When to use
- Sort data types on sets, sorted sets, lists
- Apply different sort criteria.


## How to operate `SORT`?

```redis
-- [Requirement] Sort book name order by book's year
-- (1) 'SORT' Extract all the members from books:likes (sorted set)
-- (2) 'BY'     Take each different members and going to insert them into this template
-- good -> 1950, bad -> 1930, ok -> 1940
-- (3) 'GET' # (pound) means that insert original ID whatever the origiannl member was
-- (4) 'GET' repeat that same thing in (2) . Loop through each member and get the property (key)
-- (5) 'GET'  loop all year!
-- Return members with original ID and joined title

SORT books:likes BY books:*->year GET # GET books:*->title GET books:*->year
```

#### Original data
![redis-original-data](/assets/img/redis/redis-original-data.svg)

#### Sort internally with join `GET`
![redis-get-sort](/assets/img/redis/redis-get-sort.svg)

#### Result
![redis-sort-result](/assets/img/redis/redis-sort-result.svg)


## No sort
> Use `nosnort` keyword instead of arbitrary key name.

If not exist key on 'BY' clause, `SORT` operation doesn't sort. \
Just follow original data sequence.  \
In this situation, you could use `nosort` keyword

```redis
-- [No Sort] Use 'nosort' keyword
-- Apply no sort (In this example, followed by sorted set order status)
SORT books:likes BY nosort GET # GET books:*->title
```


#### Pros
Just one network round trip. \
Single operation

#### Cons
Relatively complex query and code.
