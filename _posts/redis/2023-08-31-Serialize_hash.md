---
layout: post
title: "Serialize and Deserialize"
category: Redis
tags:
  - Redis
---

## Requirements
We have to provide serialize and deserialize in redis hash.
Because, serialize doesn't require `id` in values. \
And it's not appropriate storing specific format and data in redis because of time zone, data storage cost. \
However, deserialized data requires `id` and specific format.

## How to resolve it?

Define serialize and deserialize helper function.
For about `Javascript Date` , `id` (PK).

### Serialize requirements
- Remove id for saving storage cost \
Client already knows what the key is for searching.
- Transform from specific date object to string type which is searched easily in string.

#### Example code
```typescript
function serializeItem(attrs: CreateItemAttrs) {
  return {
    // epoch milliseconds
    // insert as 'string' instead of Date object type.
    createdAt: attrs.createdAt.toMillis(),
    endingAt: attrs.endingAt.toMillis()
  };
}
```

### Deserialize requirements
- Transform data type
  - from empty string(\'\') to `undefined` or `null`
  - from string '5' to number 5
  - from string 'March-5-2023' to `Date` object
- Insert `id` in object

#### Example
```typescript
// Include id and convert string to date object, number etc..
function deserializeItem(id: string, item: { [key: string]: string }): Item {
  return {
    // item is not object type
    // so it's not possible to use spread operator.
    id: id,
    name: item.name,
    ownerId: item.ownerId,
    imageUrl: item.imageurl,
    description: item.description,
    createdAt: DateTime.fromMillis(Number(item.createdAt)),
    endingAt: DateTime.fromMillis(Number(item.endingAt)),
    views: Number(item.views),
    likes: Number(item.likes),
    price: Number(item.price),
    bids: Number(item.bids),
    highestBidUserId: item.highestBidUserId
  };
}

```
