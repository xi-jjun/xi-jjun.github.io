---
layout: post
title: "MySQL Transaction - MySQL Engine lock"
author: "xi-jjun"
tags: Database
---

# MySQL Transaction - MySQL Engine lock

## 개요

DB공부의 Flower.. 트랜잭션이다. 중요한 이유는 차차 보면서 알 수 있을 것이다. 아래와 같은 내용들을 알아볼 것이다.

- transaction
- isolation level

DB에서는 어떤 단위로 쿼리가 실행되고, 왜 '단위'로 실행되고 롤백되는지에 대해 알기위해서 transaction에 대해 완벽히 정리해볼 것이다. 이전에 isolation level의 경우에는 포스팅이 이미 존재하지만, 한번 더 정리하며 더 깊게 이해를 해보도록하는게 목적이다.

<br>

# Transaction

트랜잭션에서 가장 중요한 것은 2가지 결과만 존재한다는 것. 트랜잭션 실행 시,

1. 쿼리가 성공하면 **DB에 완벽하게 반영**이 된다.
2. 쿼리가 실패하면 **DB는 쿼리 실행 이전상태로 완벽하게 복구(롤백)**된다.

위 2가지를 보장하는 방법이 쿼리를 '트랜잭션 단위'로 실행하는 것이고, 어떻게 보장하는지를 차차 알아볼 것이다.

<br>

책에서는 트랜잭션에 대해 아래와 같이 말하고 있다.

> *트랜잭션은 하나의 논리적인 작업 set 자체가 **100% 적용되거나(`COMMIT` 실행) 아무것도 적용되지 않아야(`ROLLBACK`) 함을 보장**해주는 것*

`MyISAM` 또는 `MEMORY` 스토리지 엔진을 사용하면, 트랜잭션을 지원하지 않기 때문에 `Partial Update` 라는 현상이 발생하여 데이터 정합성을 맞추기 더욱 어렵게 한다.

그러나 이러한 트랜잭션을 마구잡이로 사용하면 안된다. application 수준에서도 꼭 필요한 부분에서만 트랜잭션을 활용해야 하는데, 그 이유는 아래와 같다.

- DB connection 개수는 제한적인데, 트랜잭션 시간이 길어져서 소유하는 시간이 길어질수록 시스템에 영향을 줄 수 있다.
- 필요 없는 부분에 대해서도 트랜잭션을 걸어버린다면, DBMS에도 영향을 줄 수 있다.
  - 책의 예시로는, 하나의 트랜잭션 내 메일 발송 프로세스가 있었는데 만약 메일 서버 응답이 지연되거나 통신이 안되는 상황에서는 DB까지 위험해질 수 있다는 것.
    → 위험해질 수 있는 이유에 대해서 생각해봤을 때에는, 통신을 못해서 커넥션을 더 오래 들고있게되고 이는 커넥션 부족을 야기할 수 있기 때문?
  - (내 생각) 트랜잭션을 건다는 것 자체는 `InnoDB` 기준으로는 해당 record에 lock을 건다는 의미이기에(insert or update일 경우) 다른 트랜잭션도 작업이 밀리게 되어 deadlock같은 문제가 발생할 가능성도 커지기 때문?

다른 이유들도 있겠지만, 기본적으로 '필요한 곳에서만 트랜잭션을 사용하자' 에는 이견이 없다.

<br>

## MySQL Engine lock

- storage engine level : storage engine 간 상호영향 없음.
- MySQL engine level : 모든 storage engine에 영향을 줌.

MySQL engine level에서의 lock에 대해 알아볼 것이다.

<br>

### Global Lock

- MySQL에서 제공하는 lock 중 가장 범위가 큼.
  - 해당 lock이 걸려있을 때 다른 세션에서 `SELECT` 를 제외한 **대부분의 DDL, DML 실행이 대기**
  - 범위 : MySQL 서버 전체
    → **대상 테이블 또는 Database가 달라도 영향**을 받는다.
- 사용처 : `mysqldump` 를 사용하여 `MyISAM` 또는 `MEMORY` storage engine에서 **'일관된 내용'을 가져와야할 때**에 쓰이는 듯 하다.
- `FLUSH WITH READ LOCK` 으로 얻을 수 있다.
  - 실행과 동시에 MySQL에 존재하는 모든 테이블을 듣고 lock 건다. (모든 변경자겁을 멈춘다.)

MySQL 8.0부터 `InnoDB` 가 기본 storage engine으로 채택됐고, 트랜잭션을 지원하기 때문에 '일관된 내용'을 가져오기 위해서 굳이 모든 변경작업을 멈출 필요가 사라졌다. → 좀 더 가벼운 `BACKUP` lock 도입

<br>

### Table Lock

개별 테이블 단위로 실행되는 잠금.

- explicitly lock acquire 
  ```mysql
  LOCK TABLES MY_TABLE_NAME [ READ | WRITE ]; -- lock 획득
  UNLOCK TABLES; -- lock 반납
  ```

- implicitly lock acquire : `MyISAM`, `MEMORY` 테이블에 데이터를 변경하는 쿼리 실행 시 얻을 수 있다. (변경된 후 자동으로 반납된다). 
  `InnoDB` 의 경우에는 record 기반 lock이 걸리지만, DDL(schema 변경 쿼리)의 경우만을 방지하는 table lock이 설정된다.

  → 해당 `InnoDB` 의 table lock이 바로 `intention lock` 인 듯 하다. [MySQL 8.0 Docs - Intention Locks](https://dev.mysql.com/doc/refman/8.0/en/innodb-locking.html#innodb-intention-locks)

### Named Lock

임의의 문자열에 대해서 lock 설정 가능. (단순히 사용자가 지정한 문자열에 대해 획득/반납)

Deadlock이 자주 발생할 것 같은 작업에서 사용한다면, 확실히 좋을 것 같으나... 해당 문자열을 어떻게 관리해야할지에 대해서는 팀과 상의를 해봐야하지 않을까 싶다...

<br>

### Metadata Lock

데이터베이스 객체의 이름이나 구조를 변경하는 경우에 획득하는 lock. [MySQL 8.0 Docs - 8.11.4 Metadata Locking](https://dev.mysql.com/doc/refman/8.0/en/metadata-locking.html)

나중에 시간되면 해당 lock에 대해서만 정리해보고 싶다..

<br>



## References

[soyeon207.log - Intention Lock](https://velog.io/@soyeon207/DB-Lock-%EC%B4%9D%EC%A0%95%EB%A6%AC-1-InnoDB-%EC%9D%98-Lock)
