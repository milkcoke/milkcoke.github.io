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

## Solution
- Use atomic operations. \
`HSETNX`, `HINCRBY` doesn't require read. \
It removes gap between operations
- Use transaction\
Redis transaction using `MULTI` and `WATCH` keyword for guaranteeing commands are executed sequentially. \
However, it doesn't support rollback.


