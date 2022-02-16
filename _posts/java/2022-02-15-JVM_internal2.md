---
layout: post
title: "JAVA - JVM Run-Time Data Area"
author: "xi-jjun"
tags: Java


---

# Java

Java 공부를 정리 tag

<br>

# JVM Run-Time Data Area

> JVM이라는 프로그램이 OS위에서 실행되면서 할당받는 메모리 영역

지난 포스팅에서 나온 사진을 다시 보면서 시작하도록 하자.

![javaJVM_internal1](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/java/img/javaJVM_internal1.png?raw=True){: width="65%"}

<sub>[사진 출처 : Naver D2](https://d2.naver.com/helloworld/1230) </sub>

<br>

![javaJVM_internal2_1](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/java/img/javaJVM_internal2_1.png?raw=True){: width="65%"}

저번 포스팅까지는 *.class* 파일이 JVM에 어떻게 loading되는지까지 알아봤다. 이번 포스팅에서는 JVM 쓰는 메모리 공간인 *Run-Time Area* 에 대해 알아보도록 하자.

<br>

## Method Area

- JVM이 읽어 들인 각각의 클래스와 인터페이스에 대한 `Run-Time Constant Pool`, 필드와 메서드 정보, Static 변수, method의 bytecode 등을 보관.

  - Run-Time Constant Pool

    클래스와 method, field에 대한 모든 reference를 저장한다. JVM을 해당 method의 실제 메모리 상 주소를 찾아 참조시킨다.

- 모든 thread가 공유하는 영역이다. 따라서 JVM이 시작될 때 생성된다. JVM당 하나만 생성되는 영역이다.

- `method area` 는 JVM vendor마다 다양하게 구현이 가능하다.

- JVM의 다른 메모리 영역에서 해당 정보에 대한 요청이 들어오면, 실제 물리 메모리 주소로 변환해서 전달한다.

라고 한다... 하지만 너무 생소해서 와닿지가 않았다. 다음 [공식문서](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-2.html#jvms-2.5.4)에 대한 글을 읽으니 좀 감이 왔다.

> The method area is analogous to the storage area for compiled code of a conventional language or analogous to the "text" segment in an operating system process.

*`Method Area` 는 예전 언어의 컴파일된 코드를 위한 저장 영역이나 OS process의 'text' 영역과 유사하다.* 라는 말인데 이렇게 들으니깐 이해가 갔다. 

<br>

## Heap

> The heap is the run-time data area from which memory for all class instances and arrays is allocated.

라고 [공식문서](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-2.html#jvms-2.5.3)에서 말한다.

- Heap에서 객체는 명시적으로 deallocated 되지 않는다. 오직 GC에 의해서 자동으로 할당된 메모리가 회수된다.

- 문자열에 대한 정보를 가진 `String Pool` 뿐만 아니라 실제 데이터를 가진 인스턴스, 배열등이 저장된다.

  - String Pool이란?

    Java에서 String type을 literally하게 선언을 하게 되면 String Constant Pool에 저장이 된다. 이후에 같은 내용의 문자열을 선언하게 되면, **새로 선언되는 것이 아닌, String Constant Pool에 있는 같은 내용을 참조하는 것**이다.

  - 왜? 그냥 새로 만들면 안되는건가??

    constant같은 경우는 또 쓰일 일이 많다고 생각하여 같은 내용을 불필요하게 또 생성하지 않는 것이다. 그러나 `new String("STRING_SOMETHING")`으로 선언을 하게 되면, 같은 내용이어도 Heap영역에 하나 더 생성되게 된다.

- Heap영역이 가지는 모든 데이터는 `Java Stack` 영역에서 참조되어 Thread간에 공유가 된다. 

  → Concurrency issue. Thread-safe 하지 않기 때문에 `synchronized` block을 사용하여 해결해야 한다.

- Garbage Collector의 대상이 되는 영역이다.

  - 왜?

    참조되지 않는 `reference type` 객체들은 쓰이지를 않기 때문에 메모리만 차지할 뿐이다. 따라서 참조되는 객체들이 저장되는 이 Heap영역에서 생성되기 때문에 GC가 소멸시키기 위해 주 대상이 되는 것이다.

- Thread별로 메모리를 할당받는 `Java Stack` 영역과는 달리 조금 속도가 느리다. 

- JVM당 하나만 생성된다.

<br>

## Java Stacks

> 각 Thread별로 따로 할당받는 영역. 

- Heap 보다 빠르다.

- Thread 별로 메모리를 따로 할당. → Thread-safe

- Thread는 method를 호출할 때마다 `Frame`이라는 단위를 저장(push)한다. method가 return되면 `Frame` 은 Stack으로 부터 pop 된다.

  - Frame

    메소드에 대한 정보를 가지고 있는 *Local Variable*, *Operand Stack* 그리고 *Constant Pool Reference* 로 구성

    - Local Variable Array : method 내 지역변수를 저장하기 위한 배열 공간
    - Operand Stack : method 내 연산을 위해 존재하는 공간
    - Constant Pool Reference : Constant Pool을 참조하기 위한 공간

    이들에 대해서는 나중에 자세히 다루도록 하겠다.

- Stack영역이 가득차게 되면 `StackOverflowError` 발생.

<br>

`Stack` 에서 push되는 `Frame` 과 Heap영역과의 관계를 아래 그림으로 살펴보자.

- 예시 코드

  ```java
  class Person {
      private int id;
      private String name;
  
      public Person(int id, String name) {
          this.id = id;
          this.name = name;
      }
  }
  
  public class Main {
      public static void main(String[] args) {
          int id = 1;
          String name = "SSAFY";
          Person person = null;
          person = buildPerson(id, name);
      }
  
      private static Person buildPerson(int id, String name) {
          return new Person(id, name);
      }
  }
  ```

  <br>

- Stack, Heap 관계도

  ![javaJVM_internal2_2](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/java/img/javaJVM_internal2_2.png?raw=True){: width="65%"}

  <sub>[해당 사이트](https://tecoble.techcourse.co.kr/post/2021-08-09-jvm-memory/)를 참고하여 만들었습니다.</sub>

  1. 그림에서도 알 수 있듯이 `main` method를 처음에 호출했기에 Stack에 push됨을 알 수 있다.
  2. `main` method에서 `buildPerson` method를 호출했기에 Stack에 Frame이 하나 더 push되었음을 볼 수 있다.
  3. `buildPerson` method에서 Person객체 생성자를 호출하였기에 Frame하나 더 push.
  4. 그 이후로 return 이 될 때 마다 Frame은 pop된다.

<br>

## PC register

> Program Counter를 의미. 현재 Thread가 실행되는 부분의 address와 명령을 가리킨다.

- 각 Thread마다 1개씩 존재하며 Thread가 시작될 때 생성된다.

  JVM은 한번에 많은 Thread 실행시킬 수 있다. 따라서 동시에 실행하는 환경을 보장하기 위해 '최근에 실행중인 Thread에서 JVM의 instruction address를 저장할 공간이 필요'한 것이다. 따라서 그 부분을 pc register가 관리하며 추적한다.

- 현재 실행중인 JVM instruction address를 가진다.

- 만약 실행되는 method가 native하다면 pc register는 `undefined` 다.

<br>

## Native Methods Stacks

> An implementation of the Java Virtual Machine may use conventional stacks, colloquially called "C stacks," to support `native` methods (methods written in a language other than the Java programming language).

Java 외의 언어로 작성된 native code를 위한 stack이다.

JVM 구현체는 native method를 지원하기 위해 일반적으로 `C stack`이라고 불리는 기존의 stack을 사용할 수 있다고 한다. 해당 영역이 존재하는 이유는 Java로 작성된 프로그램을 실행하면서 순수하게 Java로 구성된 코드만을 사용할 수 없는 시스템 자원이나 API가 존재하기 때문이다.

<br>

# Execution Engine

> *Class Loader*에 의해 메모리에 올라온 bytcode들을 기계어로 변경해 명령어 단위로 실행하는 역할.

-  Interpreter : bytecode 명령어를 하나씩 읽고 해석하고 실행. 하나씩 읽고 해석하기에 결과의 실행은 느리다. Interpreter 언어가 가지는 단점을 그대로 가진다는 뜻이기에 bytecode라는 언어는 기본적으로 'interpreter 방식으로 동작'한다.

- JIT(Just-In-Time) 

  >자주 실행되는 bytecode 영역을 run-time 중에 기계어로 컴파일하여 사용하는 방법

  위와 같은 interpreter방식의 단점을 보완하기 위해 도입된 것이 JIT Compiler이다. 같은 코드를 매번 해석하는게 아니라 실행할 때 컴파일을 하면서 해당 코드를 캐싱 해버린다. 따라서 바뀐 부분만 컴파일 하고 나머지는 캐싱된 코드를 사용하기에 interpreter의 속도를 개선할 수 있는 것이다.

시간이 없어서 자세한 내용은 나중에 다루도록 하겠다.

<br>



## Rerference

[우아한Tech Youtube](https://www.youtube.com/watch?v=UzaGOXKVhwU)

[Tecoble - JVM](https://tecoble.techcourse.co.kr/post/2021-08-09-jvm-memory/)

[Naver D2 - JVM](https://d2.naver.com/helloworld/1230)

[Oracle Java SE8 JVM Spec](https://docs.oracle.com/javase/specs/jvms/se8/html/index.html)

[Execution Engine](https://catsbi.oopy.io/df0df290-9188-45c1-b056-b8fe032d88ca)

[JIT](https://junhyunny.github.io/information/java/jvm-execution-engine/)

Wikipedia 참고



