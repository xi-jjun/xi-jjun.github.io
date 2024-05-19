---
layout: post
title: "BigQuery - 연습문제 풀이 2"
author: "xi-jjun"
tags: BigQuery


---

# BigQuery

## 개요

[인프런 강의](https://www.inflearn.com/course/%EC%B4%88%EB%B3%B4%EC%9E%90%EB%A5%BC-%EC%9C%84%ED%95%9C-%EB%B9%85%EC%BF%BC%EB%A6%AC-sql-%EC%9E%85%EB%AC%B8/dashboard) 추천을 받게 됐고, 회사에서 현재 사용하고 있는 툴이어서 알고쓰자는 마음에 공부하게 됐습니다.

<br>

# 연습문제 풀이

## 문제1) 포캣몬 중에서 `type2` 값이 없는 포캣몬의 수를 구하는 쿼리를 작성해라

- `NULL`은 다른 값과의 직접 비교가 불가능 ex) `NULL = NULL` 은 틀린 쿼리
- 따라서 `IS` 연산자를 사용해야 한다.

```sql
-- 총 135건
SELECT
  COUNT(*) AS cnt
FROM `basic.pokemon`
WHERE 
  type2 IS NULL;
```

<br>

## 문제2) `type2`가 없는 포캣몬의 `type1` 과 `type1` 의 포캣몬의 수를 구하는 쿼리를 작성해라 (`type1`의 포캣몬의 수가 큰 순서로 정렬)

```sql
SELECT
  type1,
  COUNT(*) AS type1_cnt
FROM `basic.pokemon`
WHERE 
  type2 IS NULL
GROUP BY
  type1
ORDER BY
  type1_cnt DESC;
```

<br>

## 문제3) `type2`에 상관없이 `type1` 의 포캣몬의 수를 구하는 쿼리를 작성해라

```sql
SELECT
  type1,
  COUNT(*) AS cnt
FROM `basic.pokemon`
GROUP BY
  type1;
```

<br>

## 문제4) 전설 여부에 따른 포켓몬 수를 구하는 쿼리를 작성해라

```sql
SELECT
  is_legendary,
  COUNT(*) AS cnt
FROM `basic.pokemon`
GROUP BY
  is_legendary; -- GROUP BY 1 로 써도 된다. select 대상 1번째 컬럼으로 GROUP BY하겠다는 의미. 빠른 쿼리 작성이 가능해진다.
```

<br>

## 문제5) 동명이인이 존재하는 이름을 구하는 쿼리를 작성해라

- `WHERE` : 원본 테이블에 조건을 걸 수 있음
- `HAVING` : `GROUP BY` 결과로 나온 정보에 조건을 걸 수 있음

```sql
SELECT
  name
FROM `basic.trainer`
GROUP BY
  name
HAVING
  COUNT(*) > 1;
```

<br>

## 문제6) `trainer` 테이블에서 `Iris`트레이너 정보를 알 수 있는 쿼리를 작성해라

- 실제였다면 아마도 요구사항에 대한 구체화가 필요할 것으로 보임
  - 어떤 정보가 필요할지?
  - (쿼리 결과) `Iris` 트레이너는 2명으로 보이는데, 이 중에서 어떤 트레이너의 정보를 알고 싶은 것일지? 혹은 단순 이름이 같은 트레이너들의 정보를 알고 싶은 것일지?

```sql
SELECT
  *
FROM `basic.trainer`
WHERE 
  name = 'Iris';
```

<br>

## 문제7) `trainer` 테이블에서 `Iris`, `Whitney`, `Cynthia` 트레이너 정보를 구하는 쿼리를 작성해라

```sql
SELECT
  *
FROM `basic.trainer`
WHERE 
  name IN ('Iris', 'Whitney', 'Cynthia');
```

<br>

문제 8~10은 스킵 (이전에 풀었던 문제와 비슷)

<br>

## 문제11) `type2`값이 존재하는 포켓몬 중 제일 많은 `type1`을 구하는 쿼리를 작성해라

```sql
SELECT
  type1,
  COUNT(*) AS cnt
FROM `basic.pokemon`
WHERE
  type2 IS NOT NULL
GROUP BY
  type1
ORDER BY
  cnt DESC
LIMIT 1;
```

<br>

## 문제12) 하나의 타입만 존재하는 포켓몬 중 가장 많은 `type1`을 구하는 쿼리를 작성해라

- `type1` 이 반드시 존재한다는 보장이 없다고 생각하여 아래와 같은 `WHERE` 조건의 쿼리를 작성. 강의에서는 단순하게 `type2 IS NULL`로 '하나의 타입만 존재'함을 나타냄.

```sql
SELECT
  type1,
  COUNT(*) AS cnt
FROM `basic.pokemon`
WHERE
  (type1 IS NULL AND type2 IS NOT NULL)
  OR (type1 IS NOT NULL AND type2 IS NULL)
GROUP BY
  type1
ORDER BY
  COUNT(*) DESC
LIMIT 1;
```

<br>

## 문제15) 트레이너가 보유한 포켓몬이 가장 많은 트레이너를 구하는 쿼리를 작성해라

- `trainer_pokemon` 테이블에서 trainer id, pokemon id 로 GROUP BY하여 검색할 생각을 했었음.
  - 그러나 해당 방식은 잘못된 접근. 1명의 트레이너는 여러마리의 같은종류 포켓몬을 가질 수 있어서 `trainer_id` 로만 GROUP BY를 했어야 함.

```sql
SELECT
  name
FROM `basic.trainer`
WHERE id = (
  SELECT 
    trainer_id
  FROM (
    SELECT
      DISTINCT
      trainer_id,
      pokemon_id
    FROM `basic.trainer_pokemon`
    GROUP BY
      trainer_id, pokemon_id
  ) AS tmp
  GROUP BY
    trainer_id
  ORDER BY
    COUNT(*) DESC
  LIMIT 1
);
```

```sql
-- 정답
-- 간과했던 사실 : 트레이너는 같은 포켓몬을 여러마리 가질 수 있다는 사실
--   - 해당 사실을 알기 위해서는 trainer_pokemon 테이블을 잘 봤어야 했음
--   - 해당 테이블을 보면 pokemon_id가 같은 경우가 있지만, 그 이외의 데이터가 다름. 따라서 같은 종류의 포켓몬을 여러마리 가질 수 있었음을 알 수 있음.
SELECT
  trainer_id,
  COUNT(pokemon_id) AS pokemon_count
FROM `basic.trainer_pokemon`
GROUP BY
  pokemon_id
```

```sql
-- 수정.
-- 개인적으로 실제로 업무를 진행할 때에는 단순 trainer_id 가 아니라, 실제로 '이름'이 누군지 궁금할 것임.
-- 따라서 서브쿼리로 가장 많은 포켓몬을 보유한 트레이너(id=17)의 이름을 구하는 쿼리를 작성
SELECT
  name
FROM `basic.trainer`
WHERE id = (
  SELECT
    trainer_id
  FROM `basic.trainer_pokemon`
  GROUP BY
    trainer_id
  ORDER BY
    COUNT(*) DESC
  LIMIT 1
)
```

<br>

## 문제16) 포켓몬을 가장 많이 풀어준 트레이너를 구하는 쿼리를 작성해라

- 15번과 마찬가지로 구함.
- '풀어줬다'라는 의미를 파악하기 위해서 `trainer_pokemon` 테이블의 `status` 컬럼을 참고할 필요가 존재. (해당 컬럼의 의미파악이 중요)

```sql
SELECT
  name
FROM `basic.trainer`
WHERE id = (
  SELECT
    trainer_id
  FROM `basic.trainer_pokemon`
  WHERE status = 'Released'
  GROUP BY
    trainer_id
  ORDER BY
    COUNT(*) DESC
  LIMIT 1
)
```

<br>

## 문제17) 포켓몬을 풀어준 비율이 20%가 넘는 트레이너를 구하는 쿼리를 작성해라

- 트레이너 별 잡은 전체 포켓몬 수 구하는 쿼리로 임시 테이블 생성
- 트레이너 별 풀어준 포켓몬 수 구하는 쿼리로 임시 테이블 생성
- 2개의 테이블 `JOIN` 및 트레이너의 이름을 구하기 위해서 `trainer` 테이블도 `JOIN`
- `(풀어준 포켓몬 수 / 잡은 포켓몬 수) > 0.2` 조건을 `WHERE` 에 추가

```sql
SELECT
  trainer.id AS trainer_id,
  trainer.name AS trainer_name
FROM (
  SELECT
    trainer_id,
    COUNT(pokemon_id) AS total_pokemon_cnt
  FROM `basic.trainer_pokemon`
  GROUP BY
    trainer_id
) AS total_cnt
JOIN
  (
    SELECT
      trainer_id,
      COUNT(pokemon_id) AS total_released_pokemon_cnt
    FROM `basic.trainer_pokemon`
    WHERE status = 'Released'
    GROUP BY
      trainer_id
  ) AS released_cnt ON released_cnt.trainer_id = total_cnt.trainer_id
JOIN
  `basic.trainer` AS trainer ON trainer.id = total_cnt.trainer_id
WHERE
  (total_released_pokemon_cnt / total_pokemon_cnt) > 0.2
```

```sql
-- COUNTIF 함수에 대해서 알았으면 더 간단하게 작성이 가능했을 듯 하다
SELECT
  trainer_id,
  COUNTIF(status = 'Released') AS released_cnt, -- 풀어준 포켓몬 수
  COUNT(pokemon_id) AS total_cnt -- 전체 잡은 포켓몬 수
FROM `basic.trainer_pokemon`
GROUP BY
  trainer_id
HAVING
  (released_cnt / total_cnt) > 0.2
```

<br>

## References

- [인프런(카일스쿨) - 초보자를 위한 빅쿼리 SQL 입문](https://www.inflearn.com/course/%EC%B4%88%EB%B3%B4%EC%9E%90%EB%A5%BC-%EC%9C%84%ED%95%9C-%EB%B9%85%EC%BF%BC%EB%A6%AC-sql-%EC%9E%85%EB%AC%B8/dashboard)

  
