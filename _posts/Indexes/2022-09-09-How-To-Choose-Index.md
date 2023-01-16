---
layout: post
title: "인덱스 컬럼을 어떻게 선택할까?"
category: [Database]
tags: [database]
math: true
---

## 인덱스란?
데이터의 논리적 분류

## 인덱스를 잘 고르려면?
### 1. 분포도가 좋은 컬럼인가?
#### 분포도란?
> **전체 레코드에서 식별 가능한 수에 대한 백분율**

ex) 남녀 성비는 분포도가 50%, 나이는 (1~100세) 1%다.
즉, 백분율이 낮을 수록 분포도가 좋다고 한다.

$$분포도 = \frac{1}{식별 가능한 수} * 100  (\%)$$

#### 선택도 (Selectivity)
```sql
-- This is definition of selectivity
SELECT distinct(col1) / col1 AS 'Selectivity'
FROM table_name
```
선택도는 1에 가까울 수록, 분포도는 0에 가까울 수록 좋다.

> 분포도를 1% 내로 하는 것이 일반적으로 좋다.

맹신하지는 말자. 한 테이블에 전체 데이터가 1,000만 건이라 가정하면 1%만 해도 10만건이다.. \
한번에 10만건을 조회해와야 하는 쿼리를 비즈니스 로직에서 필요로 할까?

#### 분포도가 높아도 인덱스로 쓰일 수 있는 경우
주문 테이블에 '배송 여부' 컬럼이 있다고 가정하자.
배송 여부 컬럼의 값은 (`true`, `false`) 이고 분포도는 50%이다.
그치만 인덱스 후보로 적절하다.
물리적으로 실제 값을 확인했을 때는 최근에 주문한 주문 건에 대해서만 `false` 이고 다른 대부분의 주문건은 `true` 일 것이기 때문이다.

> 인덱스의 후보는 물리적으로도 판단하라.

```sql
SELECT *
FROM orders
-- 'N' 주문건의 비율은 전체에서 매우 적을 것이다.
-- 주로 '아직 배달되지 않은' 주문 건에 대해 조회가 일어나는 서비스라면
-- 이와 같은 쿼리가 자주 이뤄질 것이고 따라서 `is_delivered' 컬럼을 인덱스로 등록할만하다.
WHERE is_delivered = 'N'
ORDER BY ordered_date ASC;
```

일반적으로 seq scan (full scan) 은 느리다고 생각하지만 항상 그렇지만은 않다. \
postgres 의 경우 seq scan 을 수행할 때 여러 프로세스가 parallel scan 하는 매커니즘을 갖고 있기 때문이다.

### 2. 갱신이 자주 발생하지 않는 컬럼인가?
일반적으로 데이터베이스에서 인덱스는 B-Tree 혹은 B+Tree 구조로 이뤄져있다.
여기서의 B 는 'Balance' 를 의미한다. (Black 이나 Binary 가 아니다.)

따라서 인덱스는 INSERT, DELETE, UPDATE 되면 밸런스를 맞추기 위한 오버헤드가 발생할 수 있다.
특히, 가급적 UPDATE 가 자주 일어나는 컬럼은 인덱스 후보에서 제외하는 것을 권한다.

_여기가 좀 애매하네... Leaf 노드 여부랑 상관없이 UPDATE 가 INSERT / DELETE 보다 더 많이 페이지 이동이 일어날 것 같은데? INSERT DELETE 는 이미 이미 정렬된 것 기준으로 key-value 쌍으로 움직이면 되지만 Update 는 페이지 이동 확률과 양이 더 많을 듯...?_
인덱스를 담고있는 Leaf 노드에서의 INSERT/DELETE 는 밸런싱 오버헤드 발생 확률이 적지만, UPDATE 는 리밸런싱이 일어날 확률이 높기 때문이다.
UPDATE 시 매우 적은 컬럼에서만 업데이트가 일어나도록 하자. 많은 컬럼에서 업데이트가 일어날 수록 리밸런싱 비용이 빠르게 증가한다.


### 3. 조건절에서 자주 사용되나?

### 4. 조인의 연결고리에 사용되는 컬럼인가?

### 5. 소트 발생을 제거하는 컬럼인가?

## 인덱스 생성도와 실전 인덱스

## 인덱스 종류와 사용 목적


## 🔗 참조
- [update use index - luke.com](https://use-the-index-luke.com/sql/dml/update)
- [parallel sequential scan in postgres](https://postgrespro.com/blog/pgsql/5969403)
