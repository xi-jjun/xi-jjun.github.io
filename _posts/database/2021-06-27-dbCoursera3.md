---
layout: post
title: "Database(Coursera) - 1"
author: "xi-jjun"
tags: Database
---

# Database Design and Basic SQL in PostgreSQL - 3주차

Coursera 에서 강의를 듣고 공부하였다. database가 뭔지 감이 너무 안왔고, 진행되는 공모전 프로젝트에 있어서 DB를 만들 엄두도 안났기에 답을 얻고자 수업을 들었다. ~~7일동안 무료이기에 빨리 들어야 했다~~

<br>

https://www.w3schools.com/sql/sql_foreignkey.asp - foreign key 정의 설명.

















## 1주차 강의

일단 기본적으로 영어 강의라서 해석하는데에 어려움을 많이 느꼈다.

- MySQL 많이 쓰인다
- MySQL를 똑같이 복사해놓은 것을 MariahDB 라고 부른다. (동일한 소스코드 사용-[출처](https://ko.wikipedia.org/wiki/MariaDB))
- MariahDB가 살짝 바뀌어서 PostgreSQL 가 흥미로운 대안이 됐다. 강력한 Open source 이기 때문.

90년대 후반에 시작한 아마존은 Oracle을 선택했다. 우리는 아마존이 원했던 것처럼 빠른 DB를 원했고 DB회사는 되기 싫었기 때문이다. 아마존은 그래서 Oracle DB와 관련해서 많은 것들을 구축했다. 이로 인해서 많은 비용이 들었고, 아마존이 커질수록 Oracle 비용도 점점 더 커졌다. 따라서 아마존이 Oracle을 안쓰기 위해 돈을 많이 썼고, `PostgrSQL` 을 사용하기 시작했다.

<br>

## History of Relational DataBase

### DataBase가 생기기 이전

Disk는 용량이 작다. 따라서 Tape에 저장했지만 linear하게 데이터가 저장됐기에 access delay가 매우 컸다. 그래서 은행 같은 경우엔 계좌번호로 분류된 tape에 데이터를 저장했다. 그래서 낮에는 종이로 거래를 하고, 밤에는 컴퓨터를 이용해서 해당 변경 내용을 tape에 기록했다. 이 때 데이터를 계좌번호가 낮은 순서로 카드를 정렬시킨 다음 tape에 저장했었는데 이러한 정렬을 우리는 매번 했어야 했다.

데이터를 매번 정렬해야 했고, tape가 어제, 2일전, 3일전, 4일전... 처럼 점점 많아졌다.

=> DataBase가 필요한 이유다.

<br>

SQL 이전에는 file system으로 데이터를 보관했지만 DB가 생긴 이후로는 database system으로 데이터를 관리한다.

<br>

## PostgreSQL Installation in MacOS

이 강의에서는 회사들이 `PostgreSQL`로 가는 추세인 것 같다고 하면서 이걸 가르칠 것이라 했다.





```shell
brew install postgresql
```

`brew`로 설치해줬다.

```shell
brew services start postgresql
```

을 입력해서 실행시켜 줬다.

<br>

## PostgreSQL 사용법

```sql
CREATE USER "user name" WITH PASSWORD 'password';
CREATE USER pg4e WITH PASSWORD 'secret';
```

`user name`이라는 사용자의 비밀번호를 `password`으로 하겠다 라는 뜻. super user계정으로만 할 수 있다. 강의에서는 밑에 쓴 것처럼 했다.

<br>

```sql
CREATE DATABASE "database name" WITH OWNER 'owner name';
CREATE DATABASE people WITH OWNER 'pg4e';
```

`database name`이라는 이름의 DB를 만들겠다. 이 DB를 쓸 사용자는 `owner name`으로 하겠다 라는 뜻. 강의에서는 밑에 쓴 것처럼 했다.

<br>

```shell
psql "database name" "owner name"
psql people pg4e
```

`database name`이라는 DB에 접속하겠다.

<br>

```sql
CREATE TABLE "table name"(
  column1 DATATYPE,
  column2 DATATYPE
  ...
);

CREATE TABLE user(
  name VARCHAR(128),
  email VARCHAR(128)
);
```

`table name`이라는 이름의 table을 만들 것이다. attributes는 `column1, column2...`이다. 강의에서는 밑에 쓴 것처럼 했다.

<br>

```sql
SELECT column1, column2, column3... FROM "table name"
```

`table name`이라는 이름의 table에서 각 column에 해당하는 table element 보여준다.

<br>

```sql
DELETE FROM "table name"
```

`table name`의 내용 삭제.

<br>

```sql
INSERT INTO "table name" (column1, column2, column3...) VALUES (value1, value2, value3...)
```

각 column에 value를 삽입.

<br>

## 추가

- flat file DB : data model(주로 하나의 TABLE)을 plain text 파일로 인코딩하는 방법을 쓴 DB. 쉽게말하자면, .txt로 이뤄진 database이다. - [출처](https://ko.wikipedia.org/wiki/%ED%94%8C%EB%9E%AB_%ED%8C%8C%EC%9D%BC_%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4)

<br>

## 1주차 후기

영어로 된 강의여서 오역이 많을 것 같은게 걱정이다.
