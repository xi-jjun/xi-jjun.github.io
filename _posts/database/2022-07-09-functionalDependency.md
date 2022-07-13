---
layout: post
title: "Functional Dependency"
author: "xi-jjun"
tags: Database
---

# Functional Dependency

DB normalization에 대해서 공부하다가 '함수적 종속' 이라는 생소한 개념이 나와서 정리했다.

<br>

## Functional Dependency

X, Y가 Relation R의 attributes일 때, attribute X의 값 각각에 대하여 시간에 관계없이 항상 attribute Y의 값이 오직 하나만 연관되어 있을 때,

> 'Y는 X에 함수적 종속' 이라고 한다.

- X : determinant set(결정자), set으로 표현된 이유는 X는 2개 이상의 attributes일수도 있기 때문이다.
- Y : dependent attribute(종속자)

X는 결정자, Y는 종속자라고 부르며 `X→Y`로 표현한다. 함수적 종속관계에는 3가지가 있다.

<br>

## 정규화하는데 이걸 왜 알아야 하는가?

RDB를 설계할 때 중복된 데이터를 최소화하기 위한 설계를 목적으로 정규화를 한다. 이러한 DB의 정규화 과정에서 `functional dependency` 라는 개념이 사용되는 것이다. 예를 들어서 '생일'과 '나이' attribute가 한 테이블에 존재한다고 하면, '나이'는 '생일'에 의해 하나의 값으로 결정될 수 있다. 따라서 2개는 같은 의미를 갖게 될 것이고 이는 중복이 됐음을 뜻한다. 따라서 정규화는 data끼리의 `functional dependency`관계를 보며 중복된 데이터를 줄일 수 있기에 알아야 하는 것이다.

<br>

## Full functional dependency(완전 함수적 종속)

모든 non-key 속성들이 PK(Primary Key)에만 종속된 경우를 말한다. composite key의 경우에는 PK를 구성하는 모든 attribute가 포함된 집합에 종속되어야 한다. 좀 더 쉽게 말하자면, PK를 구성하는 전체에 대하여 종속된 관계여야 한다는 것이다. 정리하자면, 

> X, Y가 Relation R의 attributes고, Y가 X의 subset이 아닌 X에 대해 functionally dependent하다면, Y가 X에게 fully functional dependent하다고 할 수 있다. - [GeeksForGeeks](https://www.geeksforgeeks.org/differentiate-between-partial-dependency-and-fully-functional-dependency/)

아래 예시를 보면서 확인해보도록 하자.

![normalization1_5](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/database/img/normalization1_5.png?raw=True){: width="85%"}

`supplier_id`, `item_id` 는 composite key로 구성된 PK이다. `price` 라는 attribute는 `supplier_id` 와 `item_id` 2개의 속성에 의해 결정되고(의존하고), 이는 PK의 subset이 아닌 PK자체에 의해 결정되는 것이기에 `price` 는 PK에 `fully functionally dependent` 하다고 할 수 있다. 따라서,

> `{ supplier_id, item_id }` → `price`

라고 표현할 수 있다.

### item_id만으로도 price가 결정되는게 아니냐?

라고 할 수도 있다. 필자도 처음엔 그렇게 생각했으나 table의 2번째 row를 보면 그 이유를 알 수 있다. `item_id` 가 똑같음에도 불구하고 `price` 의 값이 다른 것을 볼 수 있다. 이를 토대로 해당 table의 row를 구별할 수 있는 방법은 `supplier_id`, `item_id` 2개임을 알 수 있는 것이다. 따라서 `price` 는 PK에 의해 결정되는 속성인 것이다.

<br>

다음 예시를 보도록 하자.

![normalization1_4](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/database/img/normalization1_4.png?raw=True){: width="80%"}

해당 table에서 PK는 `회원번호` 이다. `이름, 나이, 거주지역`은 모두 PK인 `회원번호`에 종속된다. 따라서 `이름, 나이, 거주지역`은 모두 `full functional dependency` 라고 할 수 있다. 

<br>

## Partial functional dependency(부분 함수적 종속)

> X, Y가 Relation R의 attributes이고, Y가 X에 functional dependent하면서 X의 가능한 subset에 대해서도 functional dependent하다면, `partial functional dependent` 하다고 한다. - [GeeksForGeeks](https://www.geeksforgeeks.org/differentiate-between-partial-dependency-and-fully-functional-dependency/)

![normalization1_6](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/database/img/normalization1_6.png?raw=True){: width="85%"}

PK는 `name` 과 `roll_no` 2개로 이뤄졌다고 하자. 위 table을 보면, `course` 라는 속성은 `name` 또는 `roll_no` 둘 중 하나만 있어도 결정될 수 있다. 따라서 PK의 subset에도 `course` 가 결정될 수 있기에 `course` 는 PK에 대해 `partial functional dependent` 하다고 볼 수 있다.

<br>

다음 예시를 보도록 하자.

![normalization1_3](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/database/img/normalization1_3.png?raw=True){: width="80%"}

위 그림을 보면, PK가 `고객ID`와 `제품코드`로 이뤄진 composite PK임을 알 수 있다. `주문상품` 속성은 `제품코드`에 대해서만 영향을 받는다. 왜냐면 제품코드에 따라 주문상품이 정해지기 때문이다. 따라서 `제품코드→주문상품`의 관계가 이뤄지는 것이고, 이는 PK전체가 아닌, `제품코드`만으로도 식별이 가능하기에 `Partial functional dependency` 이다. 

그에 비해 '수량'이라는 속성의 정보는 '어떤 고객'이 '어떤 상품을' 얼만큼 시켰는지에 대한 정보이다. 따라서 해당속성은 고객ID, 제품코드 2개의 속성으로 결정되는 것이고 이는 PK를 구성하는 모든 attribute가 포함된 집합에 종속되기 때문에 `Full functional dependency` 라고 할 수 있다.

<br>

## Transitive functional dependency(이행적 함수 종속)

> `transitive functional dependency`에서 종속자(dependent)는 결정자에 **간접적으로** 의존하게 된다. 만약 `a → b, b → c` 의 의존관계를 갖고 있다면, `a → c` 라는게 transitive functional dependent하다는 것이다. - [GeeksForGeeks](https://www.geeksforgeeks.org/types-of-functional-dependencies-in-dbms/)

![normalization1_7](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/database/img/normalization1_7.png?raw=True){: width="85%"}

위 table에서 PK는 `enrol_no` 이다. PK는 `name` 과 `dept`(부서정보) 를 결정하게 된다. 따라서 

>`{ enrol_no }` → `name`
>
>`{ enrol_no }` → `dept`

라고 표현할 수 있다. 근데 `building_no` 를 보게되면, `dept` 에 의해 결정됨을 알 수 있다. 건물의 번호는 '부서'에 의해 결정되기 때문이다. 따라서 

>`{ enrol_no }` → `dept`
>
>`dept` → `building_no`
>
>=> `{ enrol_no }` → `building_no`

가 된다. 따라서 `building_no` 는 `enrol_no` 에 대해 `transitive functional dependent` 하다고 할 수 있다.

<br>

## Reference

[KOCW Database 9강-용환승 교수님](http://www.kocw.net/home/cview.do?cid=d549f8570583094b)

[why functional dependency?](https://untitledtblog.tistory.com/125)

[what is functional dependency?](https://dodo000.tistory.com/20)

[fully, partial functional dependency](https://www.geeksforgeeks.org/differentiate-between-partial-dependency-and-fully-functional-dependency/)

[transitive functional dependency](https://www.geeksforgeeks.org/types-of-functional-dependencies-in-dbms/)


