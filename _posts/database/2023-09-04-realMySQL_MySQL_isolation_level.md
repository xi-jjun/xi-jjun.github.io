---
layout: post
title: "MySQL Isolation Level"
author: "xi-jjun"
tags: Database
---

# MySQL Isolation Level

> 여러 트랜잭션이 동시에 처리될 때 특정 트랜잭션이 다른 트랜잭션에서 변경하거나 조회하는 데이터를 볼 수 있게 허용할지 여부를 결정하는 것.

<br>

## Read Uncommitted

각 트랜잭션에서의 변경내용이 `COMMIT` 또는 `ROLLBACK` 에 관계없이 다른 트랜잭션에서 보인다. (`Dirty Read`)

![mysql_isolation_level_read_uncommitted](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/database/img/mysql_isolation_level_read_uncommitted.png?raw=True){: width="65%"}

<br>

## Read Committed

`COMMIT` 이 완료된 데이터만 다른 트랜잭션에서 조회가 가능.

![mysql_isolation_level_read_committed](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/database/img/mysql_isolation_level_read_committed.png?raw=True){: width="65%"}

사용자 B가 `SELECT` 할 때에 A의 트랜잭션이 완료되지 않았기에 Undo log를 읽어오는 것을 볼 수 있다. 이로써 `Read Uncommitted` 의 `Dirty read` 문제를 해결할 수 있었다. 하지만 다음과 같은 `NON-REPEATABLE READ` 라는 부정합 문제가 아래와 같이 발생할 수 있다.

![mysql_isolation_level_non_repeatable_read](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/database/img/mysql_isolation_level_non_repeatable_read.png?raw=True){: width="65%"}

사용자 B의 트랜잭션에서 **같은 `SELECT` 쿼리를 실행**했음에도 불구하고, 다른 결과를 보여주고 있는 상황이다. 이로써 

> 하나의 트랜잭션에서 동일 `SELECT`는 같은 결과를 보장한다.

라는 `REPEATABLE READ` 정합성에 어긋나는 문제가 생기는 것이다.

*'하나의 트랜잭션에서 동일 데이터를 여러번 읽고 변경하는 작업'* 일 경우에 심각한 문제가 될 수 있다. 그래서 트랜잭션을 길게하지 않는 것이 중요한 것 같기도 하면서, 로직을 변경할 수 없다면 isolation level을 `REPEATABLE READ` 로 올리는 방법밖에 없을 것 같다.

<br>

## Repeatable Read

MySQL `InnoDB`에서 기본적으로 사용하는 isolation level. `NON-REPEATABLE READ` 문제를 해결된다.

![mysql_isolation_level_repeatable_read_1](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/database/img/mysql_isolation_level_repeatable_read_1.png?raw=True){: width="65%"}

사용자 B의 10번 trx 내에서 실행되는 모든 `SELECT` 에 대해서 10번보다 작은 trx 번호에서 변경한 것만 보이게 된다. 이로써 `NON-REPEATABLE READ` 문제가 해결되는 것이다. (동일 트랜잭션 내 `SELECT` 의 결과가 같음을 보장)

마찬가지로 해당 수준에서도 아래와 같은 부정합이 발생할 수 있다.

![mysql_isolation_level_repeatable_read_2](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/database/img/mysql_isolation_level_repeatable_read_2.png?raw=True){: width="65%"}

사용자 B가 `SELECT ... FOR UPDATE` 쿼리로 테이블을 조회했을 때 해당 쿼리는 record lock을 걸어야하는데, `Undo record`에는 lock을 걸 수 없기 때문에 이와 같은 결과가 나오게 된다.

이처럼 다른 트랜잭션에서 수행한 변경작업에 의해 record가 보였다가 안보였다 하는 현상을 `PHANTOM READ` 라고 한다.

`SELECT ... FOR UPDATE`, `SELECT ... LOCK IN SHARE MODE` 로 조회되는 record는 `Undo` 영역의 변경전 데이터가 아니라, 현재 record의 값을 가져오게돼서 발생하는 것이다.

<br>

## Serializable

모든 트랜잭션에 대해서 Lock을 얻어야지만 실행된다. `PHANTOM READ` 문제는 해결이 됐으나, `InnoDB`는 `Next Key Lock`, `Gap Lock` 덕분에 `REPEATABLE READ` 수준에서도 `PHANTOM READ` 가 발생하지 않기에 굳이 해당 수준으로 설정할 필요는 없다.

기본적으로 모든 트랜잭션에 대해서 Lock을 얻고 진행하기에, 동시성 처리가 매우 떨어진다.

<br>

