---
layout: post
title: "Don't use DB as Container"
category: [Database]
tags: [database]
---


## DB as container is not good approach

### (1) Stateless
Containers are designed for stateless processes. DB is a stateful system.\
You have to manage volume. \
This is so tricky since concurrency and performance issues.

![Volume concurrency](/assets/img/container/concurrency_error.png)

### (2) Performance
Containers are not designed for high-performance workloads such as databases. \
Containers can be slower than running databases on bare metal or virtual machines because of the additional overhead of running the container environment.

### (3) Persistence
Database data must be backed up regularly. \
Native DB Engine supports this feature, if you use container and volume, you should ensure support that.


## Separate strategy between development and production

### (1) Database name
Separate only database name between development and production.
In intial phase, you could use this approach saving cost.

#### Pros
Easy to set
Saving cost

#### Cons
State is coupled. \
If cluster is down. Both environment down simultaneously


### (2) Database engine
Separate database engine completely.

#### Pros
State is also decoupled.

#### Cons
It's more difficult to manage than first approach
expensive than former
