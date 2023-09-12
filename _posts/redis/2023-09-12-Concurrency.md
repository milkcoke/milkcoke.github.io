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




