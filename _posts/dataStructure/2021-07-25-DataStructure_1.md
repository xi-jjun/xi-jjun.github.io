---
layout: post
title: "Arrays in Java"
author: "xi-jjun"
tags: Data-Structure


---

# Data Structure

`Java`언어 공부와 병행하면서 자료구조에 대해 공부할 생각이다. [해당 사이트](https://www.geeksforgeeks.org/data-structures/) 를 참고하여 이론부터 구현까지 꼼꼼히 공부할 것이다.

<br>

## Arrays in Java

- 배열은 유사한 type의 변수들로 이뤄진, 하나의 공통된 이름을 가지는 group이다.

- `C/C++`에서의 배열은 `Java`와 다르게 동작한다.

  1. Java의 모든 배열은 dynamically allocated 된다.

  2. Java에서 배열은 object이기 때문에 우리는 배열의 길이를 object property인 `length` 를 사용하여 알 수 있다. 

     → `C/C++`에서는 `sizeof()` 를 사용하는 것이 차이점이다.

  3. Java 배열 변수는 type[] 이후 선언될 수 있다.

     ```java
     int[] intArray; // recommended
     int intArray[]; // C style
     ```

     위에서 보이는 2개의 선언은 **의미적으로는 같다**. [stackoverflow의 글](https://stackoverflow.com/questions/129178/difference-between-int-array-and-int-array)을 보면 의미적으로 같으나 헷갈리지 않게 위의 코드를 추천한다고 하였다.

     <br>

     ```java
     int[] intArray; // 실제 intArray는 존재하지 않는다. print 결과는 null.
     intArray = new int[10]; // intArray에 실제로 값이 Heap영역에 생긴다. print 결과는 @address
     ```

     1. `int[] intArray;` : compiler에게 intArray라는 변수가 int type의 배열을 갖는다는 것을 뜻하는 것 뿐이다.

     2. `int[] intArray = new int[10]` : intArray의 값들이 **자동으로 초기화**되어 배열의 크기만큼 Heap 영역에 메모리를 할당해준다. 이 때 반드시 배열의 크기를 명시해줘야 한다.

     2단계 방법으로 배열을 얻을 수 있다. 먼저 원하는 배열 type의 변수를 선언해야만 한다. 다음으로 `new`를 사용해서 배열을 보관할 메모리를 할당해야 한다. → Java의 모든 배열은 dynamic allocated인 이유.

     <br>

     ```java
     int[] intArray = new int[10]; // 한번에 선언 + 할당 
     ```

     이와 같이 한번에 선언과 할당을 해줄 수도 있다. **배열의 크기는 무조건 명시해야 함.**

     <br>

     **< Default Java Array Values >** - 배열의 값이 자동으로 초기화 되는 이유 : [출처](https://www.geeksforgeeks.org/default-array-values-in-java/)

     배열이 new로 할당될 때 값을 입력 안했을 때 각 type별로 배열의 요소들은 자동으로 초기화 된다.

     - `int` : 0
     - `boolean` : false
     - `double` : 0.0
     - `String` : null
     - `User defined type` : null

     이렇게 초기화가 된다. 

     <br>

     Q1) 그렇다면 어떻게 초기화가 되는 것일까? 

     A1) 초기화가 되는 이유 - [출처1](https://stackoverflow.com/questions/3426843/what-is-the-default-initialization-of-an-array-in-java) [출처2](https://docs.oracle.com/javase/specs/jls/se7/html/jls-10.html#jls-10.3) 

     [출처2](https://docs.oracle.com/javase/specs/jls/se7/html/jls-10.html#jls-10.3) 에서 다음과 같은 말이 있다.

     > An array initializer creates an array and provides initial values for all its components.

     `array initializer`라는게 배열의 값을 초기화 시킨다는 것이다.

- Java 배열은 static field, local variable, method parameter로도 사용할 수 있다.

- 배열의 크기는 int 혹은 short type이어야 한다. long type은 안된다.

- 배열 type의 direct superclass는 `Object` class이다.

  Q2) `Object` class가 도대체 무엇인가?

  A2) 이거는 찾아보고 난 후에 조금 부끄러웠다. 어디가서 Java로 백엔드 공부한다고 말하면 안될 정도인 것 같았다. [공식문서](https://docs.oracle.com/javase/specs/jls/se7/html/jls-4.html#jls-4.3.2)에서는 다음과 같이 말한다.

  > The class `Object` is a superclass of all other classes.

  따라서 사실 내가 만든 class는 다음과 같이 표현할 수도 있다.

  ```java
  class MyClassHiHi {} // 해당 클래스는 아래와 같이 표현해도 같은 것이다.
  class MyClassHiHi extends Object {} // 모든 class는 Object를 상속받는다.
  ```

- Every array type implements the interfaces [Cloneable](https://www.geeksforgeeks.org/marker-interface-java/) and [java.io.Serializable](https://www.geeksforgeeks.org/serialization-in-java/). → 무슨 말인지 아직 잘 모르겠다...

<br>

배열 안에는 `int, char...`와 같은 primitives reference 뿐만 아니라 Object reference도 포함될 수 있다. Primitive data type의 경 실제 값들은 연속적인 메모리 위치에 저장된다. Class의 Object의 경우 실제 object들은 heap 영역에 저장된다.

<br>

## Array Members

배열은 class의 object이고 배열의 direct superclass는 `Object`라는 class임을 알게 됐다. 배열 type의 members는 다음과 같다.

- 배열의 구성요소의 개수를 나타대는 public final field `length`. `length`는 양수 또는 0의 값이다.

- 모든 members는 `Object` class로 부터 상속된다. `Object`의 `clone` method만이 상속되지 않는 유일한 method이다.

- public method `clone()`은 `Object` class의 `clone() method`를 overriding하고, checked exception을 발생시키지 않는다.

  > 원문 : The public method *clone()*, which overrides clone method in class Object and throws no checked exceptions.

<br>

공부하다가 모르는 부분이 엄청 많았고, 빠진 부분들을 채워가면서 열심히 해야할 것 같다.