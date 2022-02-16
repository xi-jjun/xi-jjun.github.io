---
layout: post
title: "JAVA - JVM Garbage Collector"
author: "xi-jjun"
tags: Java


---

# Java

Java 공부를 정리 tag

<br>

JVM 내에서 공유되는 영역인 Heap부터 각각의 method별로 할당되는 Stack까지 다양한 메모리 영역이 존재한다. 우리는 프로그램을 실행시키며 작업을 하는 도중 새로운 데이터들이 메모리에 추가되는 상황이 많다. 그러면 한가지 의문이 생긴다. 

> Java에는 메모리 해제하는 방법에 대해서는 들은게 없는데... 과연 프로그램이 실행되는 순간부터 차지하는 메모리의 양은 점점 늘어나게 되는 것일까??

이게 대한 의문을 풀기 위해 `Garbage Collector`에 대해 알아볼 것이다.

<br>

# JVM : Garbage Collector

> JVM : OS의 메모리 영역에 접근하여 메모리를 관리하는 프로그램. 해당 메모리 관리를 Garbage Collector가 수행.

JVM의 메모리를 `Garbage Collector`가 관리해준다는 것을 알았다. 그렇다면 Garbage Collector(GC)는 뭘까?

> 프로그램에서 더 이상 참조되지 않는 할당한 메모리를 회수하는게 *Garbage Collector*(GC)이다 - [wiki](https://en.wikipedia.org/wiki/Garbage_collection_(computer_science))

JVM에서 자동으로 동작하기 때문에 Java는 특별한 경우가 아니라면 개발자가 직접 메모리를 관리할 일이 생기지 않는다. 저번 포스팅에서도 알아봤듯이 GC가 가장 많이 일어나는 곳은 Heap영역이다. (참조되지않는 데이터가 발생할 수 있기 때문)

- 예시 상황 : 참조되지 않는 데이터란?

  ```java
  public class Main {
    public static void main(String[] args) {
      Foo foo = new Foo(100); // 여기서 Heap영역에 생긴 Foo(100) 은 아래 코드에 의해 참조가 끊긴다.
      foo = new Foo(123123);
    }
  }
  ```

  ![javaGC1](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/java/img/javaGC1.png?raw=True){: width="65%"}

  위 코드를 그림으로 표현하면 이렇게 된다. 이처럼 Heap영역에는 참조가 되지 않는, 소위 Garbage라 말할 수 있는 데이터가 존재할 수 있는 것이다. 이는 메모리를 차지하기 때문에 GC가 주기적으로 없애준다고 한다.

<br>

- reachability
  - reachable : 유효한 참조. *Method Area* , *Stack* , *Native Stack* 에서 참조되면 *reachable* 로 판정 (*root set*이라고 한다.)
  - unreachable : 유효하지 않은 참조. GC가 Garbage로 인식하는 객체들.

<br>

## Stop-the-World

JVM은 GC를 통해 여유 메모리를 확보할 수 있어서 마냥 좋아만 보인다. 하지만 실제로 GC가 자주 일어날수록 프로그램의 성능은 저하된다.

→ *GC* 가 일어나면 *GC* 를 담당하는 쓰레드를 제외한 모든 쓰레드들은 작동이 일시적으로 정지되는데 이를 `Stop-the-World` 현상이라고 한다. 작업을 정지시키고 진행하기 때문에 자주 GC가 일어나면 프로그램의 성능저하로 이어지는 것이다.

<br>

## Process

1. GC가 Stack의 모든 변수를 스캔하면서 각각 어떤 객체를 참조하고 있는지 찾아서 마킹한다.
2. Reachable Object가 참조하고 있는 객체도 찾아서 마킹한다.
3. 마킹되지 않은 객체를 Heap에서 제거한다.

1, 2번째 과정을 Mark. 3번째 과정을 Sweep라고 한다. 

<br>

## Heap 구조

- New Generation
  - Eden 
  - Survival 0  
  - Survival 1
- Old Generation

<br>

## Minor GC

Eden영역에는 객체가 새로 생성되면 할당되는 Heap의 메모리 영역이다. 만약 해당 영역이 꽉 차게 되면 GC가 발생하는데 이 때 GC를 `Minor GC`라고 한다. Eden에서 `Mark and Sweep`과정 이후에 살아남은 객체가 `Survival 0`로 오게 된다.

위 과정이 반복된다. 그러다가 `Survival 0`영역이 가득차게 될 수 있다. 이 때에 `Survival 0`영역에 대해서 `Mark and Sweep`과정이 발생하게 되고 살아남은 객체가 `Survival 1`로 가게 된다. `Survival 0`영역에 있던 객체는 `Survival 1`영역으로 이동할 때에 이동한 객체의 `Age` 값이 증가된다. 반대로 `Survival 1`에서 GC발생하여 0으로 가게 된다면, 또 `Age`값이 증가하게 된다.

Eden영역에서 GC가 일어날 때에는 `Survival 0, 1` 영역 중 이미 객체가 존재하는 곳으로 이동하게 된다. → 따라서 둘 중 하나의 영역은 완전히 비워져 있다.

<br>

## Promotion

> 특정 Age값을 넘어서면 Old Generation으로 이동하게 된다.

그렇게 `Promotion`이 계속 발생하여 `Old Generation` 영역이 가득차게 되면, GC가 발생한다. 이 때 발생하는 GC를 `Major GC`라고 한다.

<br>

<br>



## Rerference

[우아한Tech Youtube](https://www.youtube.com/watch?v=vZRmCbl871I)

[Tecoble - GC](https://tecoble.techcourse.co.kr/post/2021-08-30-jvm-gc/)

Wikipedia 참고



