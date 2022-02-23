---
layout: post
title: "JAVA - Collection Frameworks3"
author: "xi-jjun"
tags: Java


---

# Java

Java 공부를 정리 tag

<br>

[JDK 8 Collections: Java docs](https://docs.oracle.com/javase/tutorial/collections/index.html)를 공부하며 정리하는 글입니다.

<br>

# Queue Interface

> A [`Queue`](https://docs.oracle.com/javase/8/docs/api/java/util/Queue.html) is a collection for holding elements prior to processing

`Queue`는 어떤 프로세스를 진행하기 전에 element들이 대기하는 곳이다. 우리가 물건을 사기위해 '줄을 서서 기다리는' 것과 같다고 생각하면 된다. `Collection`의 기본 동작에 더해서 `Queue`는 추가적인 insertion, removal, inspection operations 을 제공한다.

간단하게 `Queue` interface를 한번 보도록 하자.

```java
// Queue.java
public interface Queue<E> extends Collection<E> {
  boolean add(E e);
  boolean offer(E e);
  E remove();
  E poll();
  E element();
  E peek();
}
```

`Queue` method는 2가지로 나뉜다

1. 실행에 실패하면 exception 던지는 method
2. 실행에 실패하면 다른 특정 값을 반환하는 method(null or false... 어떤 동작인지에 따라 다르다)

| Type Of Operation | Throws Exception | Returns Special Value |
| :---------------: | :--------------: | :-------------------: |
|      Insert       |     `add(e)`     |      `offer(e)`       |
|      Remove       |    `remove()`    |       `poll()`        |
|      Examine      |   `element()`    |       `peek()`        |

<br>

> Queues typically, but not necessarily, order elements in a FIFO (first-in-first-out) manner.

`Queue`는 일반적으로 FIFO 방식으로 element를 정렬한다. 예외적으로 `Priority Queue`와 같이 해당 element의 값을 기준으로 순서가 되는 것도 있다. 따라서 FIFO 방식의 `Queue`에서는 아래와 같이 동작한다.

- `add`, `offer` : `Queue`의 가장 마지막에 element가 추가된다.
- `remove`, `poll` : `Queue`의 가장 첫 element가 삭제된다.

`Queue` 구현체는 해당 큐가 가질 수 있는 요소의 개수를 제한할 수 있다. 이러한 큐를 `Bounded Queue`라고 부른다. `java.util.concurrent`에 있는 큐는 `Bounded Queue`지만, `java.util`에 있는 큐는 그렇지 않다. 

- Concurrent Queue 가 뭘까...?

  Multi-threading 환경에서 `Queue`는 concurrent 생산자-소비자 상황을 처리할 수 있어야 한다. `Concurrent Queue`를 알맞게 쓰는 것은 알고리즘에서 우수한 성능을 달성하는 것에 중요할 수 있다.

  - Blocking Queue vs Non-Blocking Queue

    - Blocking Queue : 단순한 thread-safe 메커니즘을 제공한다. 여기서 thread는 queue를 이용 가능할 때까지 기다려야 한다. 생산자는 element를 추가하기 전에 큐의 용량의 추가 가능한 용량이 될 때까지 기다리는 반면에 소비자는 큐가 빌 때까지 기다릴 것이다. 따라서 `Blocking Queue` interface에서는 `put`, `take` method를 제공한다. 이 2개의 method는 `Queue`의 `add`, `remove`와 동일하다.
    - Non-Blocking Queue : 위와 같은 경우, 예외를 발생시키거나 `null`, `false`와 같은 special value를 반환한다.

  - Bounded Queue Types

    - Bounded Queue : 큐 용량이 제한됨

      bounded queue를 쓰는 이유는 concurrent 프로그램을 설계할 때 좋은 방법이기 때문이다. 큐가 이미 가득찼을 때 element를 삽입하려고 한다면, 해당 동작은 소비자가 큐에 빈 공간을 만들 때까지 기다려야 한다. 따라서 우리가 더이상 신경 쓸 것 없이 알아서 조절을 해주기 때문에 쓰는 것이다.

    - Unbounded Queue : 큐 용량이 거의 무제한임.(컴퓨터가 허락하는 한)

  Concurrent Queue, Blocking Queue에 대해서 나중에 더 자세히 다루도록 하겠다.

<br>

각 동작에 대해 좀 더 자세한 글을 볼 수 있었다.

- `add` : `Collection`으로부터 상속받은 method. 큐 용량을 위반하지 않는다면 element를 삽입한다. 위반하게 될 경우 throws  `IllegalStateException`
- `offer` : `Bounded Queue`에서 사용되도록 의도한 `offer` method는 동작이 실패했을 경우 return `false`하는게 `add`와의 차이점이다.
- `remove`, `poll` : 둘 다 큐의 처음을 제거한다. 정확히 어떤 element가 제거되는지는 큐의 ordering policy의 기능이다. 큐가 비었을 때 호출되면, `remove`는 `NoSuchElementException`을 호출하고, `poll`은 `null`을 반환한다.
- `element`, `peek` : 큐의 처음을 반환한다. 그러나 제거하지는 않는다. 큐가 비었을 때 `remove`, `poll`과 같은 행동을 취한다.

<br>

`Queue`에는 일반적으로 `null`값이 추가될 수 없다고 한다. 근데 예외적으로 `LinkedList`로 구현된 큐는 가능하다고 한다. 역사적으로 `null`값을 허용하지만 `null`이라는 값은 `peek`, `poll` method의 실패했을 때 special return값이기에 해당 구현체에서 해당 `null`값을 추가할 수 있는 기능은 사용안하는게 좋다고 한다.

`Queue`의 구현체의 `equals`, `hashCode` method는 일반적으로 element 기반으로 정의된게 아니라 `Object`으로 부터 identity 기반으로 정의됐다.

`Queue` interface는 concurrent 프로그래밍에 일반적으로 쓰이는 `Blocking Queue method`를 정의하지 않는다. 큐에 element가 들어오길 기다리거나, 사용가능한 공간이 생길 때 까지 기다리는 이 method들은 `Queue`를 상속받아서 `java.util.concurrent.BlockQueue`에 정의되어 있다.

<br>

# Deque Interface

> Double-ended Queue. 주로 deck이라 불린다.

양 끝에서 element의 삽입, 삭제 기능을 제공하는 element들의 선형 자료구조이다. `Deque` interface는 abstract data type인 `Stack`, `Queue`가 동시에 구현됐기 때문에 둘에 비해 더 많은 기능을 제공하고, deque instance에는 양 끝의 element들에 접근할 수 있는 method들을 정의한다. 삽입, 삭제, 조회의 기능들이 제공된다. `ArrayDeque`, `LinkedList` 등이 구현체로 있다.

## Deque methods

1. Insert
   - `addFirst`, `offerFirst` : deque의 처음에 element를 삽입하는 method.
   - `addLast`, `offerLast` : deque의 마지막에 element를 삽입하는 method.
   - `Queue`에서와 마찬가지의 차이점을 가졌다. 따라서 생성된 `Deque` instance의 용량이 제한되어 있다면, `offerFirst`, `offerLast` method가 선호된다.
2. Remove
   - `removeFirst`, `pollFirst` : deque의 처음 element를 삭제.
   - `removeLast`, `pollLast` : deque의 마지막 element를 삭제.
   - 예외를 던지느냐의 차이. 마찬가지로 `Deque`의 용량이 제한되어 있다면, poll~~가 선호된다.
3. Retrieve
   - `getFirst`, `peekFirst` : deque의 첫 element를 반환.
   - `getLast`, `peekLast` : deque의 마지막 element를 반환.
   - 예외를 던지느냐의 차이. 마찬가지로 `Deque`의 용량이 제한되어 있다면, peek~~가 선호된다.
4. 기타
   - `removeFirstOccurence` : deque instance에 첫번째로 나타나는 특정 element 삭제하고 return true. 존재하지 않는다면, 해당 deque는 바뀌지 않는다.
   - `removeLastOccurence` : deque instance에 마지막에 나타나는 특정 element 삭제 return true. 존재하지 않는다면, 해당 deque는 바뀌지 않는다.



<br>

## Rerference

[Java JDK 8 docs - Collections](https://docs.oracle.com/javase/tutorial/collections/index.html)

[Concurrent Bounded Queue vs Concurrent Unbounded Queue](https://www.baeldung.com/java-queue-linkedblocking-concurrentlinked)

[Java-Concurrent-Queue](https://www.baeldung.com/java-concurrent-queues)

[Java-Blocking-Queue](https://www.baeldung.com/java-blocking-queue)





