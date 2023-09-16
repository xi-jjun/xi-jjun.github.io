---
layout: post
title: "MySQL Index - Full text search"
author: "xi-jjun"
tags: Database
---

# MySQL Index - Full Text Search

MySQL의 `LIKE` 를 쓸 때에 `'%HELLO%'` 로 검색하게 된다면, 인덱스를 사용하지 않게 된다. B-Tree 인덱스는 실제 칼럼의 앞부분만 사용하기 때문이다.

그렇다면 문자열 검색은 영원히 Full Scan만을 해야하는 것일까? 라는 물음에, Full Text Search를 답할 수 있다. 오늘 Real MySQL 책의 내용은 `Full Text Search` 알고리즘 및 사용법이다.

<br>

## 인덱스 알고리즘

키워드를 인덱싱하는 기법에 따라 아래 2개로 나뉨

- 단어의 어근 분석 알고리즘
- `n-gram` 분석 알고리즘 

<br>

## 어근 분석 알고리즘

### Stop Word 처리

> stop word : 자주 등장하지만 분석을 하는 것에 있어서는 큰 도움이 되지 않는 단어 [참고](https://wikidocs.net/22530)

MySQL 코드에 stop word 가 정의되어 있고, 사용자가 정의해서 처리할 수도 있다.

#### 확인방법

`information_schema.innodb_ft_default_stopword` 테이블에서 확인 가능.

<br>

### Stemming (어근 분석)

검색어로 선정된 단어의 뿌리인 원형을 찾는 작업. MySQL에서는 `MeCab` 이라는 플러그인 형태로 지원한다.

- `MeCab` : 일본어를 위한 형태소 분석 프로그램
- 필요 요소
  - 단어 사전
  - 실제 언어를 이용해 학습하는 과정 필요.

한글을 분석할 수 있도록 하기에는 많은 비용이 든다.

<br>

## `n-gram` 알고리즘

`MeCab` 는 만족할만한 결과를 내기 위해서 많은 비용을 써야하기에 `n-gram` 은 그 단점을 보완하기 위해 도입됐다.

> `n-gram` : 본문을 무조건 N글자씩 잘라서 인덱싱하는 방법

- 단순하다.
- 그러나 만들어진 인덱스의 크기가 상당히 크다.

N글자로 글자를 잘라서 토큰을 만들고, 해당 토큰들 중에서 stop word를 필터링하는 방식. 그 후 토큰들을 B-Tree 인덱스에 저장한다.

<br>

## Full Text Search Query

### 예시 테이블 `tb_test`

```sql
CREATE TABLE tb_test (
  doc_id INT,
  doc_body TEXT,
  PRIMARY KEY (doc_id),
  FULLTEXT KEY fx_docbody (doc_body) WITH PARSER ngram
) ENGINE=InnoDB;
```

<br>

### Full Scan Query - Bad Case

```sql
SELECT *
FROM tb_test
WHERE doc_body LIKE '%문서%';
```

→ table full scan

<br>

### Full Text Search Query - Good Case

```sql
SELECT *
FROM tb_test
WHERE MATCH(doc_body) AGAINST('문서' IN BOOLEAN MODE);
```

→ 위 예제처럼 `MATCH (...) AGAINST (...)` 로 작성을 해야하고, `MATCH` 안에 full text search 컬럼들을 모두 명시해야한다.

<br>

## 느낀점

이전에 해커톤에서 관련 인덱스를 쓴적이 있는데.. 그 때는 급해서 일단 좋아보이니깐 썼으나 이런 특징이 있다는 것을 알게되어 다행인듯..
