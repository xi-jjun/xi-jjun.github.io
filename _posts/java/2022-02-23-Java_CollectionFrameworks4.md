---
layout: post
title: "JAVA - Collection Frameworks4"
author: "xi-jjun"
tags: Java


---

# Java

Java 공부를 정리 tag

<br>

[JDK 8 Collections: Java docs](https://docs.oracle.com/javase/tutorial/collections/index.html)를 공부하며 정리하는 글입니다.

<br>

# Map Interface

> A `Map` is an object that maps keys to values.

- 중복 key를 가질 수 없다.

- operations
  - Basic : `put`, `get`, `remove`, `containsKey`, `containsValue`, `size`, `empty`)
  - bulk operation : `putAll`, `clear`
  - collection views :  `keySet`, `entrySet`, `values`
  
- Java platform에서는 `HashMap`, `TreeMap`, `LinkedHashMap` 3개의 `Map` 구현체를 제공한다.

  → `Set` interface의 `HashSet`, `TreeSet`, `LinkedHashSet`와 기능과 성능이 유사하다.

<br>

## Map Interface Basic Operations

`Map`의 기본 연산(`put`, `get`, `containsKey`, `containsValue`, `size`, `isEmpty`)은 `HastTable`과 정확히 동일하다. 만약 `Map`에서 key에 따라서 특정 순서를 갖고 싶다면, `TreeMap`을 쓰면 된다. `Map`에 입력하는 순서를 지키고 싶다면, `LinkedHashMap`을 쓰면된다.

→ 이렇게 유연함은 interface기반의 framework이 가지는 강력한 기능을 보여준다.

`Set`, `List` interface와 마찬가지로 `Map`은 `equals`, `hashCode` method에 대한 요구사항을 강화하하여 구현체에 관계없이 2개의 Map 객체가 논리적으로 동일성을 비교할 수 있게 해준다.

→ 여기서 2개의 Map 객체는 '같은 key-value' pair를 가졌다면 동일한 것이다.

<br>

## Map Interface Bulk Operations

- `clear` : `Map`에서 모든 mapping을 제거한다.
- `putAll` : `Collection` interface의 `addAll` 연산과 비슷한 연산이다.

<br>

## Collection Views

`Collection` view method는 `Map`이 `Collection`으로 보이게 해준다.

- `keySet` : `Map`에 포함된 key의 set
- `values` : `Map`에 포함된 value들의 `Collection`. 다수의 key들이 같은 value로 mapping됐을 수 있기에 이 `Collection`은 `Set` 이 아니다.
- `entrySet` : `Map`에 포함된 key-value pair의 `Set`. `Map`은 `Map.Entry`라고 불리는 작은 내부 interface를 제공한다. 이 elements type은 `Set`의 element이다.

`Collection` view는 단지 `Map`을 iterate하겠다는 의미이다. 

- `Map`이 삭제기능을 제공한다면 Iteration 도중에 `Iterator`의 `remove` method로 element를 삭제할 수 있다.
- `entrySet` view에서 `Map.Entry`의 `setValue` method를 호출하여 key를 통해 `Map`의 value를 바꾸는게 가능하다. iteration 도중 수정할 수 있는 유일한 방법이라고 한다. 이 동작은 반복 도중 다른 방법으로 `Map`이 수정되면 동작하지 않는다.
- `Collection` view는 다양한 삭제 기능들을 제공한다. `remove`, `removeAll`, `retainAll`, `clear`, `Iterator.remove`
- `Collection` view는 어떠한 상황에서도 element 추가 기능은 제공하지 않는다. `put`, `putAll` method가 `Map`에 이미 있기 때문에 굳이 필요없기 때문이다.

<br>

# Object Ordering

`List` type인 list라는 객체에 대해 정렬을 수행한다면, 아래와 같이 할 수 있다.

```java
Collections.sort(list);
```

만약 list가 `String` type이라면 알파벳 순서로 정렬이 될 것이고, `Date` type이라면 시간 순서로 정렬이 될 것이다. 어떻게 위 코드 1줄을 적었다고 type에 관련하여 적절하게 순서를 정렬해주는 것일까? → `String`, `Date` 는 모두 `Comparable` interface를 implements하기 때문이다.

`Comparable` 구현체는 해당 클래스의 객체가 자동으로 정렬될 수도록 ordering 기능을 제공한다.

![javaCollection4_1](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/java/img/javaCollection4_1.png?raw=True){: width="65%"}

<sub>[사진 출처: Java JDK 8 docs-Object ordering](https://docs.oracle.com/javase/tutorial/collections/interfaces/order.html)</sub>

만약에 `Comparable`을 구현 하지 않는 element의 list를 정렬할 때는 `ClassCastException`이 발생한다. 따라서 만약에 `Person`이라는 사용자 정의 클래스를 만들었다면,

```java
static class Person implements Comparable<Person> {
    static int SERIAL = 0;
    String name;
    int seq;
    Long id;

    public Person(String name, Long id) {
      this.name = name;
      this.id = id;
      seq = (++SERIAL);
    }

    @Override
    public int compareTo(Person o) {
      return this.seq - o.seq;
  }
}
```

`Comparable` interface를 구현해야 list에서 정렬이 가능하다. 따라서 `mutually comparable` 한 element들만 정렬이 가능하다.

- `compareTo`

  특정 객체와 받은 객체를 비교해서 특정 객체보다 더 작은지, 같은지, 더 큰지에 따라서 음수, 0, 양수를 반환한다. 특정 객체가 받은 객체와 비교할 수 없다면, `ClassCastException`을 던진다.

근데 위와 같이 비교할 경우, `overflow`, `underflow`에 대해 고려를 안한 것이 된다. 따라서 어떤 값이 들어올지 알 수 없을 경우,

```java
@Override
public int compareTo(Person o) {
  if (this.seq > o.seq) return 1;
  else if (this.seq < o.seq) return -1;
  else return 0;
}
```

과 같이 대소비교를 해주는게 안전해서 좋다고 한다.

<br>

## Comparators

만약에 원래 정렬방법이 아닌 다른 방법으로 정렬하고 싶거나 `Comparable`을 구현하지 않는 객체를 정렬하고 싶을 땐 어떻게 해야 할까? → `Comparator`사용.

```java
public interface Comparator<T> {
    int compare(T o1, T o2);
}
```

```java
Comparator<Person> comp = new Comparator<Person>() {
  @Override
  public int compare(Person o1, Person o2) {
    return o1.id - o2.id;
  }
};
```

를 생성하여 비교해줄 수 있다. 더 간단하게 lambda eq로 만들어보면

```java
Comparator<Person> comp = Comparator.comparingInt(o -> o.id);
```

로 만들 수 있다. [여기](https://github.com/xi-jjun/data-structure-and-algorithm/blob/master/src/javastudy/collectionframework/sorting/ComparatorTest.java)서 테스트 코드를 작성해봤다.

<br>

## Comparators vs Comparable

- Comparable : compareTo(T o) 라는 method가 있다. '자기 자신과 매개변수 객체를비교' 하는 것.
- Comparator : compare(T o1, T o2) 라는 method가 있다. '두 parameter 객체를 비교' 하는 것.



<br>

## Rerference

[Java JDK 8 docs - Collections](https://docs.oracle.com/javase/tutorial/collections/index.html)

[Java JDK 8 docs - Object Ordering](https://docs.oracle.com/javase/tutorial/collections/interfaces/order.html)

[Java <T extends Comparable<? super T>> 란?](https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=zxwnstn&logNo=221550689930)

[Comparable vs Comparator](https://st-lab.tistory.com/243)





