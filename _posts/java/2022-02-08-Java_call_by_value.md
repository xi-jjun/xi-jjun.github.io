---
layout: post
title: "JAVA - Call By Value"
author: "xi-jjun"
tags: Java


---

# Java

Java 공부를 정리 tag

<br>

# Java 는 Call By Value!!!

코딩의 시작은 C언어로 했어서 포인터에 대한 지식이 없지는 않았다. 그저 C언어에서는 주소값을 함수에 넘겨주면 '원본에 직접 접근'할 수 있다는 것 정도만 알았다. 그래서 대표적으로 `swap(a, b)`를 수행할 때를 예시로 들어서 공부를 했던 것 같다. 잠깐 살펴보고 가자.

<br>

## C 는 Call By Reference

```c
#include <stdio.h>

void swap_call_by_value(int a, int b);
void swap_call_by_reference(int* a, int* b);

int main(void) {
  int a = 10;
  int b = 20;

  swap_call_by_value(a, b);
  printf("call by value (10, 20) => (%d, %d)", a, b);

  swap_call_by_reference(&a, &b);
  printf("\ncall by reference (10, 20) => (%d, %d)", a, b);

  return 0;
}

void swap_call_by_value(int a, int b) {
  a = a + b;
  b = a - b;
  a = a - b;
}

void swap_call_by_reference(int* a, int* b) {
  *a = *a + *b;
  *b = *a - *b;
  *a = *a - *b;
}
```

```console
<실행결과>
call by value (10, 20) => (10, 20)
call by reference (10, 20) => (20, 10)
```

위처럼 변수의 주소에 직접 접근하여 값을 수정할 수 있다. 

<br>

## Java Call By Reference

먼저 Java에서는 C언어처럼 포인터가 없다. 그래서 위처럼 `primitive type`에서는 함수에 호출되어 값이 바뀔 수 있는 경우는 없다. 

하지만 `reference type`에서는 어떨까? - [결과 코드 바로 보기](https://github.com/xi-jjun/data-structure-and-algorithm/blob/master/src/javastudy/callbyvalue/CallByValueTest.java)

```java
package javastudy.callbyvalue;

public class CallByValueTest {
  public static void main(String[] args) {
    /**
     * #1 : foo 생성과 초기 상태
     * foo 값 : foo 객체의 내용이 실제로 존재하는 "Heap space 의 주소"
     * foo 주소 : 대충 여기서 구체적인 설명을 위해 0x1000 이라고 가정하자.
     * foo 객체의 내용이 실제로 존재하는 Heap space 의 주소 : 0x3000 이라고 가정하자.
     */
    Foo foo = new Foo(10, "Hello");

    System.out.println("=== Init Foo ===");
    System.out.println(foo);

    /**
     * #2 : foo 객체 processing
     * foo 라는 객체를 process 라는 함수에서 호출하여 접근하려고 한다.
     */
    process(foo);
    System.out.println();

    System.out.println("=== After processing Foo ===");
    System.out.println(foo);
  }

  private static void process(Foo paramFoo) {
    /**
     * #2-1 :
     * main 에서 foo 라는 객체가 현재 이 process 라는 함수에 의해 호출되어
     * paramFoo 라는 parameter 로 들어왔다.
     * paramFoo 값 : foo 의 값(0x3000)
     * paramFoo 주소 : 구체적으로 설명하기 위해 대충 0x1200 이라고 하자.
     */

    /**
     * 아래는 paramFoo 가 가리키는 객체의 'value', 'name' 이라는 variable 에 접근하는 것이다.
     * 말했다시피 객체의 변수에 직접 접근했기에 원본을 변경한 것과 동일하다.
     */
    paramFoo.value = 4321;
    paramFoo.name = "Bye";

    /**
     * 새롭게 생성된 객체를 new Foo 라고 하자. 해당 객체를 paramFoo 에 넣어주겠다.
     * new Foo 값 : 새롭게 생성된 객체의 주소(0x4000 이라 하자)
     * new Foo 주소 : == paramFoo 의 주소 (0x1200)
     */
    paramFoo = new Foo(99999, "Changed!");
  }

  static class Foo {
    int value;
    String name;

    public Foo(int value, String name) {
      this.value = value;
      this.name = name;
    }

    @Override
    public String toString() {
      return "Foo{" +
        "value=" + value +
        ", name='" + name + '\'' +
        '}';
    }
  }
}
```

```console
=== Init Foo ===
Foo{value=10, name='Hello'}

=== After processing Foo ===
Foo{value=4321, name='Bye'}
```

이라는 결과가 나온다. Java가 `Call By Reference`로 동작했다면, `Foo{value=99999, name="Changed!"}`가 나왔겠지만, Java는 `Call By Value`로 동작하기에 위와 같은 결과가 나올 수 있었다.(주석의 설명을 참고하길 바란다)

<br>

주석을 읽어도 되지만 그림을 보면 더 쉽게 설명이 된다.

![java_call_by_value1](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/java/img/java_call_by_value1.png?raw=True){: width="70%"}

위 주석 내용을 그림으로 표현한 것이다.

Java에서는 method가 parameter로 값 받으면 해당 value를 복사하여 local variable(stack) 에 저장하여 쓴다고 한다. 따라서 `paramFoo`는 `foo`와 동일한 위치를 가리키고 있기에 똑같이 접근할 수 있다. 나중에는 method가 끝남에 따라 없어진다.

<br>

## 지금 함수 내에서의 값 변경이 main에서도 적용되어 있는데 어떻게 된 일인가?

라고 생각할 수 있다. 하지만 우리가 변경한 값은 객체 내부의 `value, name`이라는 변수이다. `paramFoo, foo` 둘 다 동일한 객체를 가리키고 있기에 같은 변수에 접근이 가능하다. 따라서 수정이 가능했던 것이다. 

## 그러면 Call By Reference 아닌가??

그 말을 반박하기 위해 정리해보겠다.

- `Call By Reference` : 함수 호출시 인자로 전달되는 **변수의 reference를 전달**한다. (해당 변수 자체를 가르킨다.)
- `Call By Value` : 함수 호출시 인자로 전달되는 **변수의 값을 복사하여 전달**한다. 

따라서 'Java의 argument 전달 방식이 변수 자체에 접근하는 `Call By Reference`라면 `paramFoo` 자체를 변경했을 때도 main에 수정사항이 반영되어야 한다' 라는 말은 `true`가 되어야 하지만 코드의 결과만 봐도 알 수 있듯이 `false`이다. 아래는 왜 거짓인지 한 눈에 알 수 있게 해준다.

 `paramFoo`에 다른 객체의 reference value를 주게 되면 아래 그림과 같이 '새로운 객체를 `paramFoo`에 생성하는 것' 뿐이다.

![java_call_by_value2](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/java/img/java_call_by_value2.png?raw=True){: width="70%"}

코드와 그림을 보면 parameter로 넘어온 값을 변경하더라도, main에서의 `foo`는 변함이 없는 것을 알 수 있다. 이는 `paramFoo`가 변한 것이지, `foo`가 변경된 것이 아니기 때문이다. 따라서 Java는 `Call By Value`로 parameter를 전달하는게 맞다.
