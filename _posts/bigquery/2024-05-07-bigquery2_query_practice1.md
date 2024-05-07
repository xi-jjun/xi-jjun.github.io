---
layout: post
title: "BigQuery - 연습문제 풀이 1"
author: "xi-jjun"
tags: BigQuery


---

# BigQuery

## 개요

[인프런 강의](https://www.inflearn.com/course/%EC%B4%88%EB%B3%B4%EC%9E%90%EB%A5%BC-%EC%9C%84%ED%95%9C-%EB%B9%85%EC%BF%BC%EB%A6%AC-sql-%EC%9E%85%EB%AC%B8/dashboard) 추천을 받게 됐고, 회사에서 현재 사용하고 있는 툴이어서 알고쓰자는 마음에 공부하게 됐습니다.

<br>

# 연습문제 풀이

## 문제1) `trainer` 테이블에 존재하는 모든 데이터를 보여주는 SQL을 작성해라

- '모든 데이터'의 정의란? => 모든 컬럼? 모든 rows? 애매하면 물어봐야하고, 추정을 해본다면 여기서는 아마도 '모든 컬럼'일 가능성이 높다. 실제 업무에서는 반드시 물어보고 진행하자. (문제 상황을 정확이 이해한 뒤에 해결을 위해서 쿼리를 작성하는게 바람직함)

```sql
SELECT *
FROM `basic.trainer`; -- project name은 생략 가능 (프로젝트가 명시되어 있다면)
```

<br>

## 문제2) `trainer` 테이블에 있는 트레이너의 `name`을 출력하는 쿼리를 작성해라

```sql
SELECT
	name
FROM `basic.trainer`;
```

<br>

## 문제3) `trainer` 테이블에 있는 트레이너의 `name`, `age` 을 출력하는 쿼리를 작성해라

```sql
SELECT
	name,
	age
FROM `basic.trainer`;
```

<br>

## 문제4) `trainer` 테이블에 있는 `id=3`인 트레이너의 `name`, `age`, `hometown` 을 출력하는 쿼리를 작성해라

- 실제 업무에서는 '어떤 데이터를 추출하길 원하는지'에 대해서 정확한 파악이 필수다. 
  - 가령 '트레이너의 인적사항을 알려주세요' 라고 한다면, 정확히 여기에 존재하는 컬럼 중 어떤 컬럼들을 원하는지 물어봐서 요구사항을 구체화하는 작업이 필요하다.

```sql
SELECT
	name,
	age,
	hometown
FROM `basic.trainer`
WHERE 
	id = 3; -- 강의에서는 이와 같이 들여쓰기를 진행하여 따라함.
```

<br>

## 문제5) `pokemon` 테이블에서 "피카츄"의 공격력과 체력을 확인할 수 있는 쿼리를 작성해라

`pokemon`이라는 테이블을 오늘 처음 본다고 가정하면, 아래와 같은 확인과정을 진행해야 쿼리를 작성할 수 있다.

1. `pokemon` 테이블에 어떤 컬럼들이 존재하는지 확인
2. 해당 컬럼들 중에서 '공격력'과 '체력'에 관련된 컬럼 확인 => 컬럼 이름과 매칭되지 않는다면, 질문이 필요
3. "피카츄"라는 한글 이름이 저장된 컬럼 확인

```sql
SELECT
	attack,
	hp
FROM `basic.pokemon`
WHERE 
  kor_name = '피카츄';
```

<br>

## 문제6) `pokemon` 테이블에 존재하는 포켓몬 총 수를 구하는 쿼리를 작성해라

- 먼저 `pokemon` 테이블의 데이터에 '중복'이 존재하는지 확인하는 쿼리를 작성하여 중복체크
  - 여기서는 `kor_name` 기준으로 중복을 판별할 수 있다고 판단. => 상식적으로 같은 이름의 포켓몬은 없을 것이기에

```sql
SELECT
	name,
	COUNT(*) AS cnt
FROM `basic.pokemon`
GROUP BY kor_name
HAVING COUNT(*) > 1;
-- 결과 : 0건
```

<br>

- 중복이 존재하지 않음을 확인한 상태에서, 전체 포켓몬 수 구하는 쿼리 작성

```sql
SELECT
	COUNT(*) AS cnt -- 확인하기 용이하도록, alias를 명시하자!
FROM `basic.pokemon`;
-- 결과 : 251건
```

<br>

## 문제7) `pokemon` 테이블에 세대별로 몇마리의 포켓몬이 존재하는지 확인하는 쿼리를 작성해라

```sql
SELECT
  generation,
	COUNT(*) AS cnt
FROM `basic.pokemon`
GROUP BY 
	generation; -- 강의에서 이런식으로 포맷을 작성하길래 따라해봄
```

<br>

## 문제8) 포켓몬의 수를 타입별로 집계하고, 포켓몬의 수가 10마리 이상인 타입만 남기는 쿼리를 작성해라 (포켓몬 수가 많은 순서로 정렬)

```sql
SELECT
  type1 AS pokemon_type,
  COUNT(*) AS cnt -- => null이 아님이 명확하다면, `id`와 같은 컬럼을 기입해주는게 명시적이어서 좋다고 함. 
FROM `basic.pokemon`
GROUP BY 
  type1
HAVING 
  COUNT(*) >= 10 -- cnt로 써도 됨
ORDER BY 
  COUNT(*) DESC; -- cnt로 써도 됨
```



<br>

## References

- [인프런(카일스쿨) - 초보자를 위한 빅쿼리 SQL 입문](https://www.inflearn.com/course/%EC%B4%88%EB%B3%B4%EC%9E%90%EB%A5%BC-%EC%9C%84%ED%95%9C-%EB%B9%85%EC%BF%BC%EB%A6%AC-sql-%EC%9E%85%EB%AC%B8/dashboard)

  
