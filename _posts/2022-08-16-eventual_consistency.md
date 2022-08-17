---
layout: post
title: "Strong vs Eventual Consistency"
category: [Database]
tags: [database]
---

## Strong Consistency
_"Always all data is to have integrity"_

![Strong Consistency](/assets/img/consistency/strong_consistency.png)



## Eventual Consistency
This ensures all read operations retrieves updated data at the end.

![Eventual Consistency](/assets/img/consistency/eventual_consistency.gif)

Master Node + Read replica

| Index            | Strong Consistency                  | Eventual Consistency              |
|------------------|-------------------------------------|-----------------------------------|
| Pros             | Data consistency and integrity      | Scalability, Performance          |
| Cons             | Scalability, Performance            | Weak consistency and integrity    |
| When To Use      | Small data </br>context is specific | Large data</br>context is general |
| Example use-case | Payment transaction                 | Likes on instagram picture        |


### What developers have to decide?
**Always consider 'user-centered' perspective, Does it affect on user experience?**

---

## 🔗 References
- [Balancing strong and eventual consistency - Google Cloud Platform](https://cloud.google.com/datastore/docs/articles/balancing-strong-and-eventual-consistency-with-google-cloud-datastore?hl=ko#h.w3kz4fze562t)
