---
layout: post
title: Batch command in redis
category: Redis
tags:
  - Redis
---


## why to use batch?
When we have to send a lot of command to redis \
Redis server have to process commands and replies about them. \
In request - response mechanism, there's also 'Round Trip Time'.


## Round Trip Time (RTT)
![RTT](/assets/img/redis/RTT.png)
### Why RTT makes process slow?

#### internet hops
If client and server communication using internet protocol \
There's many hops between them. \
According to region, routing, many differences exist.

#### context switching
Redis client - server has TCP connection. \
When sending command (client) and receiving the command (server), there's socket `read()` , `write()` blocking system call. \
So, a lot of context switching between user and kernel area in the server.

## How to use pipeline batch
Redis has **'pipeline'** command for batch commands.
**You should consider request reasonable number commands on it.**

While the client sends commands using pipelining, \
the server will be forced to queue the replies, using memory.
If massive command in a single pipeline, there's possibility of peeking memory usage on redis server.


## References
- [Redis pipelining - redis offical docs](https://redis.io/docs/manual/pipelining/)
