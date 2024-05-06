---
layout: post
title: "BigQuery - OLTP, OLAP"
author: "xi-jjun"
tags: BigQuery


---

# BigQuery

## 개요

[인프런 강의](https://www.inflearn.com/course/%EC%B4%88%EB%B3%B4%EC%9E%90%EB%A5%BC-%EC%9C%84%ED%95%9C-%EB%B9%85%EC%BF%BC%EB%A6%AC-sql-%EC%9E%85%EB%AC%B8/dashboard) 추천을 받게 됐고, 회사에서 현재 사용하고 있는 툴이어서 알고쓰자는 마음에 공부하게 됐습니다.

<br>

## BigQuery란?

### OLTP (Online Transaction Processing)

OLTP는 transaction 지향적인 어플리케이션에서 사용되는 DB system의 한 종류이다. OLTP에서 'Online'이란 사용자 요청이 실시간으로 처리되어 응답되어야함을 의미한다. 

그렇다면 transaction이란 무엇일지 잠시 확인하고 넘어가보자. DB를 공부하며 가장 익숙하게 들었을 단어지만, 간단하게 얘기해보자면,

> DBMS에서 수행되는 작업의 단위. 아래와 같은 역할을 한다
>
> 1. 시스템에 이상이 생길 경우에도, DB 데이터의 무결성과 일관성을 보장해준다. 따라서 데이터는 '성공적으로 저장'되거나 '아예 저장되지 않음' 2가지 케이스로만 처리되는 것. (애매하게 어중간한 상태로 저장되는 일은 없다는 의미)
> 2. DB에 동시에 접근할 경우에 대한 isolation level 을 지원한다. 레벨에 따라서 동일 데이터에 대한 동시접근 시 처리 또는 그 결과가 달라질 수 있다.
>
> 출처 : [DB transaction - wikipedia](https://en.wikipedia.org/wiki/Database_transaction)

사실 웬만한 서비스는 OLTP서비스라고 봐도 무방하다. 우리는 RDB를 사용하여 사용자의 요청을 저장하고, 저장한 데이터를 어떤 isolation level로 접근가능할지 정한다. 쇼핑몰 서비스를 예시로 들자면 사용자의 '주문' 요청을 '요청됨' 상태로 DB에 추가하고, '요청됨' 상태의 데이터에 접근하여 물건을 '배송중' 상태로 만드는 일련의 과정들이 모드 transaction 지향적인 어플리케이션의 특징이라 보면 될 듯 하다.

강의 내용에 나온대로 요약하자면 MySQL, PostgreSQL 등의 데이터베이스의 특징 중 하나로 볼 수 있고,

- 거래를 하기 위해 사용되는 데이터베이스
- 중간 상태가 없는, 오직 '존재X'하거나 '존재O' 상태만 존재하는 데이터
- 데이터의 추가/수정이 주로 발생
- SQL 로 조회 가능. 그러나 OLAP에 비해 느린 조회.

<br>

### OLAP (Online Analytical Processing)

> multi-dimensional analytical(MDA) 쿼리에 신속하게 답변하기 위한 접근 방식. - [출처 : OLAP - wikipedia](https://en.wikipedia.org/wiki/Online_analytical_processing)

`multi-dimensional analytical(MDA)` 를 알아보기 전에, [`multi-dimensional data model`](https://www.geeksforgeeks.org/multidimensional-data-model/)에 대해서 알아봐야 한다. `multi-dimensional data model`이란 하나의 데이터를 여러 '차원'으로 구성하여 표현하는 모델이다. 이해가 쉽도록 예시를 통해 얘기하자면, '시간'이라는 차원을 '1분기' '2분기' '3분기' '4분기'로 분류하여 표현한다는 의미다.

왜 이런 이름을 붙였고, 왜 굳이 이렇게 표현해야하는지에 대해서는 OLAP의 등장 배경에 대해서 알아봐야 한다. 일단 OLAP는 기업이 대규모 데이터를 관리 및 분석하기 위해서 등장했다. 분석할거라면 기존의 OLTP 서비스로는 안되냐? 라고 한다면, OLTP는 실시간 트랜잭션 처리(추가/수정/삭제 등)에 집중하기에 대규모 데이터를 읽어서 분석하거나 다차원적인 분석에는 맞지 않았기에 OLAP가 등장한 것이다.

따라서 OLAP는 `multi-dimensional analytical(MDA)` 으로 데이터를 다양한 시각에서 분석할 수 있도록 시각화하는 기능을 제공하게됐고, 기업은 이를 통해서 대규모 데이터를 신속하게 분석할 수 있게 됐다. 

<br>

#### 궁금증1) multi-dimensional data model은 DB 정규화는 같은 것일까?

찾아보다가 RDB의 정규화와 다른게 무엇일지 궁금해서 생각해보니, 당연하게 다를 수 밖에 없었다. 일단 개념 자체가 다르기도 하고... 목적 자체가 다르다.

- `Mult-dimensional data model` : 분석하기 용이하도록 데이터를 구조화한 것.
- RDB 정규화 : 데이터의 불필요한 중복을 최소화하고, 정규화를 통해서 데이터의 consistency(일관성)을 더 쉽게 유지하기 위해 사용되는 방법.

결국 '분석'과 '데이터 무결성' 이라는 다른 목적을 위해서 사용되는 것이라 보면 되기에 서로 다른 개념이라 보면 될 것 같다.

<br>

#### 궁금증2) 그러면 OLTP에서 multi-dimensional data model을 사용할 수는 없는가?

이 또한 목적 자체가 다르기 때문에, 불가능 보다는... 그 개념이 탄생한 목적과 배경을 부정하게 되기에 굳이 사용하지 않는다고 생각한다.

- `OLAP` : 데이터를 분석하기 위해서, 데이터를 다양한 시각에서 볼 수 있도록 다차원 모델을 사용. 따라서 read작업에 중점을 둔다.
- `OLTP` : 주로 데이터 트랜잭션을 처리하기 위해 사용되는 개념이고, 따라서 데이터의 추가/수정/삭제 작업에 중점을 둔다. 데이터의 수정이 자주 일어나는 상황에서 데이터 분석 결과 또한 매번 변경된다.

따라서 OLAP의 경우에는 실시간 데이터 추가 수정 작업이 빈번하게 일어나는 서비스가 아닌, 여러 데이터들을 한 곳에 모아서 분석할 수 있게끔 하는 처리도 포함되는 개념이라고 보면 될 듯 하다.

1. 주문DB + 결제DB + 사용자 CS문의DB 데이터를 한 곳에 모은다.
2. 모은 데이터를 가공하여 분석에 사용한다.

이정도가 되지 않을까 생각을 해본다...

<br>

### Data warehouse

>  여러개의 DB또는 데이터 보관소들로부터의 데이터들을 모아놓은 통합된 저장소. - [출처: data warehouse - wikipedia](https://en.wikipedia.org/wiki/Data_warehouse)

OLAP에서 분석을 위해 참조하는 데이터들은 대게 data warehouse에 저장된 통합된 데이터들이다.

- 데이터를 한 곳에 모아서 저장

<br>

### 결론 - BigQuery란?

`BigQuery`란 Google Cloud 서비스에서의 OLAP + Data warehouse 서비스인 것이다.

<br>

## BigQuery 비용

### 쿼리 비용

- On-demand 요금제 : 쿼리에서 처리된 용량만큼 부과. 1TB 당 $6.25
- Capacity 요금제 : Slot 단위로 요금 부과.

### 저장 비용

- Active Logical 저장소 : 1GB당 한 달에 $0.02
- Long-term Logical 저장소 : 1GB당 한 달에 $0.01 (90일 동안 수정X)

<br>

## BigQuery 환경 구성 요소

- Project : 여러개의 dateset을 가진다.
- Dateset : 여러개의 table을 가진다.
- Table : 데이터를 저장하고 있다.

<br>

## References

- [인프런(카일스쿨) - 초보자를 위한 빅쿼리 SQL 입문](https://www.inflearn.com/course/%EC%B4%88%EB%B3%B4%EC%9E%90%EB%A5%BC-%EC%9C%84%ED%95%9C-%EB%B9%85%EC%BF%BC%EB%A6%AC-sql-%EC%9E%85%EB%AC%B8/dashboard)
- [wikipedia - data warehouse](https://en.wikipedia.org/wiki/Data_warehouse)
- [wikipedia - OLAP](https://en.wikipedia.org/wiki/Online_analytical_processing)
- [wikipedia - OLTP](https://en.wikipedia.org/wiki/Online_transaction_processing)
- [geeks for geeks - multi-dimensional data model](https://www.geeksforgeeks.org/multidimensional-data-model/)
