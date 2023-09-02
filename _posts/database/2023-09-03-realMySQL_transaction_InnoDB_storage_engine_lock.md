---
layout: post
title: "MySQL Transaction - InnoDB Storage Engine Lock"
author: "xi-jjun"
tags: Database
---

# MySQL Transaction - InnoDB Storage Engine Lock

# InnoDB Storage Engine Lock

MySQL에서 제공하는 잠금과는 별개로 record기반의 잠금방식 탑재. → 뛰어난 동시성 처리가 가능한 이유. 그러나 lock에 대한 정보를 알기 힘듦.

최신 버전의 `InnoDB` 에서의 트랜잭션 잠금과 대기중인 트랜잭션 목록 조회 기능이 도입되어 아래와 같은 방법으로 조회가 가능.

- `information_schema` 데이터베이스에 존재하는 `INNODB_TRX`, `INNODB_LOCKS`, `INNODB_LOCK_WAITS` 라는 테이블을 join해서 조회 가능.
- `Performance Schema` 를 이용하여 `InnoDB` 내부 잠금(`semaphore`)에 대해서도 모니터링이 가능.

<br>

## Record Lock

- row level lock
- `InnoDB`에서는 **인덱스의 record에 대해 lock**을 거는 것이다.
  - 인덱스가 없는 테이블이어도 자동 생성된 클러스터 인덱스를 이용해 lock을 설정.
  - PK, Unique index에 의한 변경작업에서는 Gap이 아닌, record 자체에 대해서만 lock을 설정한다고 함.

index record? 어떤 차이가 있는지 예제를 보면서 이해해보자.

### Index record

- `Employees` 라는 테이블 존재.
  - `first_name`, `last_name`, `age` 컬럼이 존재.
  - `first_name` 으로 인덱스가 걸려서 index table이 존재. (Index table 이름은 `idx_firstname`)

위와 같은 상태에서 다음과 같은 조건으로 쿼리를 실행한다고 해보자.

```text
가정
- first_name이 '재준'인 경우는 총 243건이라고 가정
- first_name이 '재준' 이면서 last_name이 '김' 인 경우는 총 1건이라고 가정

쿼리 조건 : first_name이 '재준' 이면서 last_name이 '김' 인 employee의 age를 27으로 변경
```

```mysql
-- 위 쿼리 조건의 내용을 쿼리로 변환
UPDATE employees 
SET age = 27
WHERE first_name = '재준' AND last_name = '김';
```

자, 위와 같은 쿼리를 실행하게될 때, ***1건의 업데이트를 위해서 몇 개의 record에 lock을 걸어야 할까?***

다시 말하자면, MySQL은 record 기반의 lock을 사용하게될 때, index record lock을 설정하게 된다. 당연하게도 그 뜻은 index에 lock을 건다는 것이고 따라서 <span style="color: red; font-weight: bold;">243개의 index record에 lock을 걸게될 것</span>이다. (`first_name` 이 `'재준'` 인 row는 총 243건이라고 가정했기 때문)

처음에 약간 이해하기 어려웠던 부분이, 

> 1건만 업데이트하면 되는 것을 왜 굳이 불필요하게 243건 모두에 대해서 lock을 거는 것일까..?

라는 생각이 들어서 인지부조화가 왔었다...

위를 통해 알 수 있는 것은, MySQL에서의 index는 단순 조회성능을 위해서뿐만이 아니라 write 시에도 큰 영향을 미칠 수 있다는 것이다. 위 경우처럼 쓸데없이 200여개의 index lock을 걸게된다면, 다른 세션에서는 lock을 얻기 위해서 대기를 해야하는 상황만이 오기 때문에 `UPDATE` 시에도 적잘한 index를 설정해야한다는 것이다. (책에서 많이 강조한 이유를 알게됐다...)

결국 index가 없는 경우라면 **table full scan으로 `UPDATE` 를 실행하게 되고, 이는 해당 테이블의 모든 record에 대해 lock**을 걸게 되니깐, 우리는 항상 index를 무조건 잘 설정해야한다. (사실 index가 없는 경우는 없고, clustered index에 대해 lock을 건다고 한다. [MySQL 8.0 Docs 참고](https://dev.mysql.com/doc/refman/8.0/en/innodb-index-types.html))

> If a table has no `PRIMARY KEY` or suitable `UNIQUE` index, `InnoDB` generates a hidden clustered index named `GEN_CLUST_INDEX` on a synthetic column that contains row ID values. ...

PK도 적절한 Unique index column도 없다면, `GEN_CLUST_INDEX` 를 생성한다고 한다...

Index lock같은 경우는 매우 중요해보여서 최대한 자세히 살펴봤다.

<br>

## Gap Lock

Record 자체가 아니라 record와 인접한 record 사이의 간격만을 lock하는 것. (MySQL에서만 존재)
→ 그 사이의 간격에 새로운 record가 생성되는 것을 제어하는 것이라고 한다.

index 가 아니고 nonunique index일 경우 변경작업 쿼리를 실행할 때, `Gap Lock` 이 걸리게 된다고 한다. (따라서 index 또는 unique index가 걸려있다면, record lock이 걸리게 될 것이다.)

[여기서](https://medium.com/daangn/mysql-gap-lock-%EB%8B%A4%EC%8B%9C%EB%B3%B4%EA%B8%B0-7f47ea3f68bc) Gap Lock에 대해 더 자세히 알 수 있으니 참고하면 좋을 것 같다.

> Gap Lock은 MySQL 서버 내부적으로 Shared Lock 형태만 존재(Exclusive Gap Lock은 없음)하며, 순수하게 다른 트랜잭션이 대상 간격(Gap)에 새로운 레코드가 INSERT되는 것을 막는 것이 주 목적이다.

### Tip 중요한 정보

> 아래 Tip은 [당근 테크 블로그 - MySQL Gap Lock 다시보기](https://medium.com/daangn/mysql-gap-lock-%EB%8B%A4%EC%8B%9C%EB%B3%B4%EA%B8%B0-7f47ea3f68bc)에서 가져온 내용입니다.

- Primary Key와 Unique Index
  - 쿼리의 조건이 1건의 결과를 보장하는 경우, Gap Lock은 사용되지 않고 **Record Lock만 사용됨**
  - 쿼리의 조건이 1건의 결과를 보장하지 못하는 경우, Record Lock + Gap Lock이 동시에 사용됨 (레코드가 없거나, 여러 컬럼으로 구성된 복합 인덱스를 일부 컬럼만으로 WHERE 조건이 사용된 경우 포함)
- Non-Unique Secondary Index
  - 쿼리의 결과 대상 레코드 건수에 관계없이 **항상 Record Lock + Gap Lock이 사용됨**

~~*정리 감사합니다 당근이시어...*~~

<br>

## Next Key Lock

> ..., **a next-key lock is an index-record lock plus a gap lock on the gap preceding the index record**. 
>
> If one session has a shared or exclusive lock on record `R` in an index, another session cannot insert a new index record in the gap immediately before `R` in the index order.
>
> [MySQL 8.0 Docs - Next-Key Locks](https://dev.mysql.com/doc/refman/8.0/en/innodb-locking.html#innodb-next-key-locks)

`Record lock` + `Gap Lock` == `Next Key Lock` 

### 참고) Insert Intention Locks

> An insert intention lock is a type of gap lock set by [`INSERT`](https://dev.mysql.com/doc/refman/8.0/en/insert.html) operations prior to row insertion. This lock signals the intent to insert in such a way that multiple transactions **inserting into the same index gap need not wait for each other if they are not inserting at the same position within the gap**. ...

같은 index gap에 대해 여러개의 트랜잭션이 `INSERT` 작업을 하기 위한 Lock. 변경작업할 row가 같지 않다면 해당 Gap에서의 변경작업이 동시에 가능하다.

- `Gap Lock` 은 항상 Shared mode 이기 때문에 서로 충돌없이 lock 획득이 가능

- 그 다음 `INSERT` 작업이 필요할 때, `Insert Intention Lock` 이 필요하다. 

- 그러나 `Insert Intention Lock` 와 `Gap Lock` 은 서로 호환이 되지 않는다. (`Insert Intention Lock` 을 획득하기 위해서 다른 트랜잭션의 `Gap Lock` 의 resolve를 기다리게 되기 때문)

  → 하지만 사실 `InnoDB` 에서는 `insert Intention Lock` 을 사용하게 되면, row가 겹치지 않을 경우에 대해서만 **대기없이 변경작업을 할 수 있게 해준다.**

<br>

## AUTO-INC Locks

> An `AUTO-INC` lock is a special table-level lock taken by transactions inserting into tables with `AUTO_INCREMENT` columns.

`AUTO_INCREMENT` 컬럼이 사용된 테이블에 동시에 여러 record가 `INSERT` 되는 경우, 중복되지 않는 일련의 값을 가지게 해준다. MySQL에서는 이를 위해서 테이블 수준의 Lock을 사용한다.

기본적으로 `INSERT` 시 `AUTO_INCREMENT` 를 가져오는 순간에만 lock이 걸리고, `UPDATE`, `DELETE` 에는 걸리지 않는다.

<br>

## 느낀점

Lock... 너무 많다... 날 잡고 다시 정리해야할 듯...합니다..

<br>

## References

[MySQL 8.0 Docs - Record Locks](https://dev.mysql.com/doc/refman/8.0/en/innodb-locking.html#innodb-record-locks)

[MySQL 8.0 Docs - 15.6.2.1 Clustered and Secondary Indexes](https://dev.mysql.com/doc/refman/8.0/en/innodb-index-types.html)

[당근 테크 블로그 - MySQL Gap Lock 다시보기](https://medium.com/daangn/mysql-gap-lock-%EB%8B%A4%EC%8B%9C%EB%B3%B4%EA%B8%B0-7f47ea3f68bc)

[MySQL 8.0 Docs - 15.7.1 InnoDB Locking](https://dev.mysql.com/doc/refman/8.0/en/innodb-locking.html)
