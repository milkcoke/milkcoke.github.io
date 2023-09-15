---
layout: post
title: Concurrency in redis
category: Redis
tags:
  - Redis
---



In many cases, concurrency could be ignored.
It depends on your business requirement.

## Problem
Race condition depends on sequence of commands. \
Result differ according to it.

Why this happened? There's gap between read and update.

![General Concurrency Issue](/assets/img/redis/concurrency_issue.svg)

## Solution
### (1) Use atomic command
ex)`HSETNX`, `HINCRBY` \
It removes gap between read and command \
![Atomic operation](/assets/img/redis/concurrency_atomic_operation.svg)

### (2) Use transaction
Redis transaction using `MULTI` and `WATCH` keyword for guaranteeing commands are executed sequentially. \
However, it doesn't support rollback.

#### Transaction example
`MULTI` command enqueue all commands until `EXEC` command to be executed. \
So, commands are to be executed sequentially one by one.

```redis
>> MULTI
-- "OK"

>> SET color red
>> SET color blue
-- "QUEUED"

>> EXEC
-- ["OK", "OK"]
```

`WATCH` command provides optimistic lock. \
It allow only single command operation. \
And make other transactions fail.

```redis
-- optimistic lock
>> WATCH color

>> SET color red

>> MULTI
>> SET color blue
>> SET color green

>> EXEC
-- nil (failed to execute transaction)

>> SET color yellow
-- success color set as `yellow` since `EXEC` call `UNWATCHED` implicitly
```

## Concurrency issue is not always problem
- To Do app status update
- Calender app
- Only one user can access (not shared resource)

**Like those app, concurrency issue is not critical.**


## Cons of `WATCH` solution

Suppose bid system.
bid condition is "bid amount must be greater than item price". \
![WATCH-Scenario](/assets/img/redis/item-scenario.png)


There's error case when applying `WATCH` solution. \
Let me show you example,
![WATCH-error-case](/assets/img/redis/watch-case.png)

when original Item price is 5$, If first request amount 10$ and second request amount 15$ \
Both request occur almost simultaneously, first request succeed but second request is going to fail. \
This is error case since second request amount 15$ is greater than 10$ of first request.

For Guaranteeing second request success, we should implement retry logic. \
From this , A lot of load occurs on redis server since there's many fail cases for short time.


### Summary cons of `WATCH` solution
We call it `WATCH` solution as optimistic lock.

1. Error case
2. Not scalable \
Especially mass requests occurs, results in a lot of fail transactions and load on redis server.
3. Redis cluster doesn't support `WATCH`

> For resolving this problem, we can use **distributed lock**.
{: .prompt-tip }

You can see lock in redis [this post]({% post_url redis/2023-09-15-Lock %})




