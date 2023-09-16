---
layout: post
title: "MySQL Index - Function based index"
author: "xi-jjun"
tags: Database
---

# MySQL Index - Function Based Index

칼럼의 값을 변형해서 인덱스를 구축해야할 경우에 사용. (MySQL 8.0부터 지원) 아래 2가지 방법으로 지원이 가능하다.

- Virtual Column(가상 컬럼)을 이용한 인덱스
- 함수를 이용한 인덱스

기본적으로 인덱스의 내부적인 구조 및 유지관리 방법은 B-Tree 인덱스와 동일.

<br>

## Virtual Column 을 이용한 index

```sql
CREATE TABLE users (
  id BIGINT,
  first_name VARCHAR(100),
  last_name VARCHAR(100),
  PRIMARY KEY (id)
);
```

위 `Users` 테이블이 존재한다고 가정해보자. 여기서 아래와 같은 요구사항이 등장.

> ***"`first_name` 과 `last_name` 을 합쳐서 검색할 수 있어야 함."***

보통같으면

1. full_name 컬럼 추가 (인덱스도 걸고..)
2. full_name에 first_name + last_name 값을 마이그레이션

순서로 진행했을 것이다. (적어도 나라면 이렇게 할 듯)

<br>

하지만 MySQL 8.0 부터는 아래처럼 가상 컬럼 추가하고 거기에 인덱스를 걸 수 있다고 한다. (가상 컬럼 처음 들어봄;;)

```sql
ALTER TABLE users
	ADD full_name VARCHAR(255) AS (CONCAT(first_name, ' ', last_name)) VIRTUAL,
	ADD INDEX ix_fullname (full_name);
```

→ 테이블에 새로운 컬럼을 추가하는 것과 같은 효과 (실제 테이블 구조 변경됨)

<br>

## 함수를 이용한 index

MySQL 5.7에서는 함수를 직접 인덱스 생성 시 사용할 수 없었지만, 8.0부터는 가능해졌기에 아래와 같이 인덱스 생성이 가능하다.

```sql
CREATE TABLE users (
  id BIGINT,
  first_name VARCHAR(100),
  last_name VARCHAR(100),
  PRIMARY KEY (id),
  INDEX ix_fullname ((CONCAT(first_name, ' ', last_name)))
);
```

→ 테이블 구조를 변경하지 않는다.

활용을 하기 위해서는 **`WHERE` 조건절에 사용되는 표현식이 그대로 사용되어야 한다.** (다르면 결과값이 같더라도, optimizer에서 다른 표현식으로 간주하여 인덱스를 사용하지 않기 때문)

```sql
SELECT *
FROM users
WHERE CONCAT(first_name, ' ', last_name) = 'Jaejun Kim';
```

<br>

## 성능?

2개의 방법 모두 내부적으로 동일한 구현 방법을 사용하기에, 성능차이는 존재하지 않는다.

<br>

