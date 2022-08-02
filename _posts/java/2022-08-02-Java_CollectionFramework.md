---
layout: post
title: "RE:JAVA - Collection Framework"
author: "xi-jjun"
tags: Java


---

# Java

Java 공부를 정리 tag

<br>

# Collection Framework

> Collection이란? ➡️ data의 집합, 그룹이며 Java의 collection framework는 이러한 데이터, 자료구조인 컬렌션과 이를 구현하는 클래스를 정의하는 인터페이스를 제공한다.

![collection1_1](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/java/img/collection1_1.png?raw=True){: width="75%"}

<sub>[사진출처](http://dawoonjeong.com/java-jcf/)</sub>

그렇다면 왜 등장했을까?

<br>

## Why?

우리가 알고리즘을 푸는 상황이라고 가장하자. 알고리즘은 배열을 만들어야했고, 매 순간 순간 입력으로 들어오는 데이터를 추가할지 말지에 대해 판단을 했어야 한다고 해보자. 그럴 때 우리는

```java
for (int i = 0; i < N; i++) {
  if (condition(input)) {
    list.add(input);
  }
}
```

라는 코드를 짤 수 있을 것이다. 

> 그런데 만약 입력될 데이터의 양(N)에 대해 미리 알수가 없다면...?

대부분의 알고리즘 문제에서는 당연하게도 데이터의 크기에 대한 명시를 해주겠지만, 실제 개발은 이와는 다르다. 사용자가 인터넷 쇼핑몰 장바구니에 10개의 제품을 담을지, 100억개의 제품을 담을지 누가 알겠는가.

위 상황을 해결하기 위해 생각을 해보면, '어떤 상황에서라도 추가가 가능해야 함' 이라는게 보장되어야 한다. 

> 아니 그러면 배열써서 크기 넘어가면 더 큰 배열 선언해서 데이터 옮기면 되는거 아니냐?

더 큰 배열을 선언해줌과 동시에 원래 있던 정보를 새로 생성한 배열에 모두 복사를 한다면 우리는 더 이상 아까와 같이 '얼만큼의 데이터가 들어올지'에 대한 두려움을 가질 필요가 없어지게 된다. 그래서 여기서 한가지 더 질문을 하자면,

> 맨날 가변길이 배열에 대한 메서드를 프로젝트 할 때마다 만들 것인가?

이다. 당연하게도 해당 기능에 대한 코드는 디테일에 대한 차이는 있어도 결국엔 같은 기능을 할 수 밖에 없게 된다. 같은 기능을 매번 개발하는 것은 반복되는 작업이기에 지루하고 개발자는 정작 집중해야할 비니지스 로직보다 해당 코드들을 복사 붙여넣기 하기에 바빠진다.

> **따라서 이러한 반복된 작업, 데이터를 모으는 방법에 대한 명세를 interface로 모아놓은 것이 `Collection` 이다**

<br>

## Collection 주요 interface

1. List
2. Set
3. Queue

여기서 드는 의문 하나.

> Map은...?

위 사진에서도 알 수 있듯이 `Map` interface가 보이지 않는다. Map은 알다시피 key-value pair로 이뤄진 자료구조이다. 요소 하나만 받아서 다루는 List, Set, Queue와는 그 구조 자체가 다르기에 Map은 Collection framework에는 속하나, Collection은 아니라고 한다.

> A `Set` is a special kind of `Collection`, a `SortedSet` is a special kind of `Set`, and so forth. Note also that the hierarchy consists of two distinct trees — a `Map` is not a true `Collection`. - [출처:orcale공식docs](https://docs.oracle.com/javase/tutorial/collections/interfaces/index.html)

<br>

## Why? 왜 맨 위에 Iterable interface가 존재할까?

위 사진을 보면, 맨 위에 `Iterable` 이라는 interface가 존재함을 볼 수 있다. 왜 그럴까?

> Implementing this interface allows an object to be the target of the enhanced for statement (sometimes called the "for-each loop" statement).

IntelliJ에서 Java8 기준으로 `Iterable` interface에 위와 같은 글이 적혀 있었다. 

생각해보면 당연하게도, 결국엔 배열 또는 적절한 자료구조에 저장되어 해당 구현체 class에서 정의된 method대로 동작할 것이다. 따라서 `Collection` interface에서 for loop로 각 요소에 접근하기 위해 존재한다.

<br>

## Collection interface

`Iterable` interface 아래에 `Collection` interface가 존재하는 것을 볼 수 있다. 

> The root interface in the collection hierarchy. A collection represents a group of objects, known as its elements.

라고 Java8에 되어 있었다. `Collection` interface는 Collection class들을 다루는데 필요한 가장 기본적인 기능에 대한 명세이다.

```java
public interface Collection<E> extends Iterable<E> {
  ...
}
```

`Iterfable` interface도 상속받은 것을 볼 수 있다.

<br>

## Conclusion

마치며 평소에 List, Set등으로 편하게 알고리즘을 풀게 해준 Collection framework에 감사한다.

<br>

## Rerference

[Collection vs Collections in java](https://www.geeksforgeeks.org/collection-vs-collections-in-java-with-example/)



