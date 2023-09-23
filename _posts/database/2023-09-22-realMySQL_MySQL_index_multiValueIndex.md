---
layout: post
title: "MySQL Index - Multi value index"
author: "xi-jjun"
tags: Database
---

# Multi value index

이걸 처음에 보고, 2개 이상의 컬럼으로 이뤄진 인덱스를 뜻하는 것인가...? 라는 생각이 들었으나 책 내용을 보면 그런 것 같지가 않았다. 책의 내용을 유심히 보면,

> 하나의 레코드에 여러개의 값들이 존재하고, 그런 값들로 인덱스 추가가 가능

라고 되어있다. 이는 말 그대로 'multi value' 에 대한 인덱싱이 가능하다는 뜻 같다. 구체적으로 말하자면 JSON type의 컬럼의 각 값에 대한 인덱스를 거는 것이 가능하다 정도. (그래서 1개의 컬럼이 여러개의 인덱스를 가진다는 뜻 → multi value index...)

MySQL 8.0으로 오면서 가능해졌다고 하는데... `PostgreSQL` 에서는 Array type 컬럼의 각각의 값에 인덱스를 걸 수 있다고 들었던 것 같은데, MySQL에서는 가능하다는 것을 이번에 처음 알게됐다. 책에서 제공한 예시를 따라해보자.

```sql
CREATE TABLE Users (
  id BIGINT AUTO_INCREMENT PRIMARY KEY,
  first_name VARCHAR(100),
  last_name VARCHAR(100),
  extra_data JSON,
  INDEX mx_extra_data ( (CAST(extra_data->'$.data' AS UNSIGNED ARRAY)) )
);

INSERT INTO Users VALUES (1, 'Jaejun', 'Kim', '{"data":[100, 200, 3042, 49, 756, 1, 604, 98, 411]}');

mysql> SELECT * FROM Users WHERE id=1;
+----+------------+-----------+------------------------------------------------------+
| id | first_name | last_name | extra_data                                           |
+----+------------+-----------+------------------------------------------------------+
|  1 | Jaejun     | Kim       | {"data": [100, 200, 3042, 49, 756, 1, 604, 98, 411]} |
+----+------------+-----------+------------------------------------------------------+
```

```sql
-- 조회 쿼리
mysql> SELECT *
    -> FROM Users
    -> WHERE 49 MEMBER OF(extra_data->'$.data');
+----+------------+-----------+------------------------------------------------------+
| id | first_name | last_name | extra_data                                           |
+----+------------+-----------+------------------------------------------------------+
|  1 | Jaejun     | Kim       | {"data": [100, 200, 3042, 49, 756, 1, 604, 98, 411]} |
+----+------------+-----------+------------------------------------------------------+
1 row in set (0.04 sec)

-- 실행계획 확인
mysql> EXPLAIN SELECT * FROM Users WHERE 49 MEMBER OF(extra_data->'$.data');
+----+-------------+-------+------------+------+---------------+---------------+---------+-------+------+----------+-------------+
| id | select_type | table | partitions | type | possible_keys | key           | key_len | ref   | rows | filtered | Extra       |
+----+-------------+-------+------------+------+---------------+---------------+---------+-------+------+----------+-------------+
|  1 | SIMPLE      | Users | NULL       | ref  | mx_extra_data | mx_extra_data | 9       | const |    1 |   100.00 | Using where |
+----+-------------+-------+------------+------+---------------+---------------+---------+-------+------+----------+-------------+
1 row in set, 1 warning (0.01 sec)
```

`possible_keys` 로 `mx_extra_data` 가 사용된 것을 확인할 수 있다.

Multi value index를 활용하기 위해서는 아래와 같은 함수를 사용해야 한다고 나와있다.

- `MEMBER OF()`
- `JSON_CONTAINS()`
- `JSON_OVERLAPS()`

근데... 이걸 찾다보니 인터넷에서 잘 나오지가 않는다. ~~대부분 여러개의 컬럼으로 이뤄진 인덱스에 관한 내용만 나오더라..~~
사람들이 잘 쓰는 것 같지가 않은느낌...

## 결론

하나의 컬럼에 여러개의 인덱스가 존재 → multi value index
