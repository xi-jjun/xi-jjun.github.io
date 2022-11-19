---
layout: post
title: "Kotlin: Basic syntax"
author: "xi-jjun"
tags: kotlin

---

# Kotlin: Basic syntax

최근에 기업 공고를 보면 `java/kotlin` 기반으로 spring boot를 사용할 줄 아는 사람들을 뽑는다는 글을 많이 봤다. 내가 지원한 기업에서도 질문으로 kotlin을 사용해봤냐는 질문이 있었기에 기본적인 공부는 해볼려고 한다. [여기](https://kotlinlang.org/docs/basic-syntax.html)를 보고 정리해놓을려고 한다.

<br>

이 글은 kotlin의 기본적인 syntax를 간략하게 적어놓은 [해당 사이트](https://kotlinlang.org/docs/basic-syntax.html)를 보고 번역의 의미와 함께 공부를 위해 정리하는 글이다.

<br>

## Program entry point﻿

프로그램을 실행하는 부분은 `main` method이다.

```kotlin
fun main() {
    println("Hello Kotlin!")
}
```

기존에 `Java`에서는 `public static void main(String[] args)`와 같이 아주 길게도 적어줬어야 했는데 매우 간략해진 것을 볼 수 있다.

```kotlin
fun main(args: Array<String>) {
    println(args.contentToString())
}
```

이렇게 `args`로도 입력을 받을 수 있다.

<br>

## Print to the standard output

- `print` : prints its args
- `println` : prints its args + line break

```kotlin
print("Hello Kotlin\n")
println("Hello Kotlin")
```

```shell
Hello Kotlin
Hello Kotlin
```

이걸 보면서 감격을 했다. `System.out.println("Hello Java!")`라고 출력할 문자보다 긴 코드를 적으면서 현타가 왔던 나에게 '이래도 되는거야...?'라는 기분을 느끼게 해줬다.

확실히 `kotlin`으로 넘어가는 이유 중 하나인 

> `Java`보디 더욱 간결한 코드

가 잘 드러나는 순간인 것 같다. 

<br>

## Functions

```kotlin
fun add(num1: Int, num2: Int): Int {
    return num1 + num2
}
```

```kotlin
fun add(num1: Int, num2: Int) = num1 + num2
```

이렇게 하면 return type이 알아서 추론된다고 한다.

```kotlin
fun add(num1: Int, num2: Int): Unit {
    println("$num1 + $num2 = ${num1 + num2}")
}
```

위와 같이 `Unit`으로 의미없는 value도 return할 수 있다고 한다.

### Unit이 뭔데...?

>- For Common, JVM, JS : [kotlin docs](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-unit/)
>
>  The type with only one value: the `Unit` object. This type corresponds to the `void` type in Java.

`Java`에서의 void method랑 대응된다고 보면될 것 같다. 그리고 당연하게도 `Unit`은 아래와 같이 생략될 수 있다고 한다.

```kotlin
fun add(num1: Int, num2: Int) {
    println("$num1 + $num2 = ${num1 + num2}")
}
```

하지만 위와 같은 코드는 [kotlin code convention #Function](https://kotlinlang.org/docs/coding-conventions.html#functions)에 의하면 좋지 않은 스타일이라고 한다.

> Prefer using an expression body for functions with the body consisting of a single expression.

따라서 고치면 아래와 같다.

```kotlin
fun add(num1: Int, num2: Int) = println("$num1 + $num2 = ${num1 + num2}")
```

<br>

## Variables

- `val` : read only immutable variable => 한번 선언하면 재할당이 불가능하다.

  ```kotlin
  val num: Int = 3
  num = 4 // (x) => `num` is immutable!! 재할당 불가능!
  
  val num1: Int
  num1 = 100 // (o) => deferred assignment
  ```

  `Val cannot be reassigned` error message가 출력된다.

- `var` : mutable variable => 재할당이 가능하다!

  ```kotlin
  var num: Int = 3
  var num2 = 4 // `Int` type is inferred!!
  var num3: Int // Type required when no initializer is provided!!
  num = 100 // (o) => `num` is mutable!!
  num3 = 13 // deferred assignment
  ```

`Java`와의 가장 이질감이 있다고 생각했던 부분이었다. 그 동안 항상 type을 명시해왔으나 이제는 추론하라고 하니... 

<br>

## Conditional expressions

```kotlin
fun max(a: Int, b: Int): Int {
    if (a > b) {
        return a
    }
    return b
}
```

```kotlin
fun max(a: Int, b: Int) = if (a > b) a else b
```

해당 문법은 조금 익숙했다. 예전에 `python`을 공부했을 때 비슷한 문법이 있었기 때문이다.

```python
## python code ##
a = 1
b = 4
max_number = a if a > b else b
```

<br>

## for loop

```kotlin
val names = listOf("김재준", "김근우", "문명석")
for (name in names) {
    println("$name 는 동아리 회원입니다.")
}
```

```kotlin
val names = listOf("김재준", "류도연", "이찬희")
for (index in names.indices) {
    println("$index's member is $names[index]")
}
```

일단은 `kotlin`언어에 대하여 전반적으로 훑어보는 중이어서 나중에 깊게 볼 것이다. 하지만 `for loop`도 `python`에서 비슷한 문법이 존재했기에 익숙했다.

<br>

## while loop

```kotlin
val items = listOf("apple", "banana", "kiwifruit")
var index = 0
while (index < items.size) {
    println("item at $index is ${items[index]}")
    index++
}
```

`Java`랑 똑같다.

<br>

## when expression

```kotlin
fun describe(obj: Any): String =
    when (obj) {
        1 -> "this is 1"
        "Hello" -> " Kotlin!!"
        "xi-jjun" -> " : owner of this repo"
        else -> "Unknown"
    }
```

`when`... 진짜 생소했다. 왜냐하면 처음 들어보니깐! 눈치껏 보자면 `Java`의 `switch`를 표현하는 것 같았다. 하지만 `Java`보다 뤌씬 간결해진 코드를 볼 수 있다. 기존 `Java`에서는

```java
String describe(Object obj) {
    switch ...
}
```

이렇게 위의 `kotlin` code를 표현해볼 수 있을 것이다. 그러나 애초에 `Java`에서는 `switch`의 조건에 `Any`라는 타입을 넣을 수 없다...?

```java
// Java code
String formatterPatternSwitch(Object o) {
    return switch (o) {
        case Integer i -> String.format("int %d", i);
        case Long l    -> String.format("long %d", l);
        case Double d  -> String.format("double %f", d);
        case String s  -> String.format("String %s", s);
        default        -> o.toString();
    };
}
```

> ??? 있다. `Java SE 17`에 생겼다고 한다.

설마해서 찾아봤는데 진짜 있을 줄은 몰랐다. 하지만 완전하게 같지는 않은 것 같아 보인다.

```java
static void test(Object o) {
    switch (o) {
        case String s && (s.length() == 1) -> ...
        case String s                      -> ...
        ...
    }
}
```

하지만 또 이렇게 `String s && (s.equals("SOME_STRING"))`으로 case condition을 준다면 비슷해질지도 모르겠다. 재밌어 보이니 다음에 따로 정리해보도록 하겠다.

<br>

## Ranges

```kotlin
val start = 1
val end = 4
for (number in start..end) {
    println(number)
}
```

```shell
1
2
3
4
```

> Put spaces around binary operators (`a + b`). Exception: don't put spaces around the "range to" operator (`0..i`).

[code convention #whitespace](https://kotlinlang.org/docs/coding-conventions.html#horizontal-whitespace)를 보면 위와 같이 적혀 있다. 따라서 `start..end`사이에는 공백이 없어야 함을 주의하자.

```kotlin
println("1 to 10 by step 2")
for (x in 1..10 step 2) {
    print("${x} ")
}

println("\n5 to 2 by step 1")
for (x in 5 downTo 1) {
    print("${x} ")
}

println("\n7 to 1 by step 2")
for (x in 7 downTo 1 step 2) {
    print("${x} ")
}
```

```shell
1 to 10 by step 2
1 3 5 7 9 
5 to 2 by step 1
5 4 3 2 1 
7 to 1 by step 2
7 5 3 1 
```

`Modern`언어는 대부분 `python`도 마찬가지지만 비슷한 것 같다. 간결함과 직관성을 추구하다 보니 저렇게 된게 아닐까.

<br>

## 느낀점

`swift`와 비슷하다는 느낌을 많이 받았다. 그도 그럴게

```swift
// swift code
func hello(name: String) {
    print("Hello, \(name)!")
}
```

```kotlin
// kotlin code
fun hello(name: String) {
    print("Hello $name!")
}
```

비슷한 부분이 확실하게 많이 보인다. 비교적 최근에 만들어진 언어들은 비슷하다는 느낌인 것 같다. 그리고 'null 에 대한 안정성'에 대해서는 두 언어가 `null check`가 잘 된다는 점을 보면...

`swift`는 잠시 머리 식힐겸 한 것이라 익숙하게 다룬다고는 할 수 없다. 그러나 비슷한 부분이 많았기에 큰 어려움 없이 이해할 수 있는 부분들이 많았다. 꾸준히 공부하여 `spring boot` project도 모두 `kotlin`으로 바꿔볼 생각이다.

<br>

## Next...

`Collections`, `Nullable`, `Type check` 등등이 남아있다. 이 3가지는 중요해보이기에 다음 포스팅에서 마저 정리하도록 하겠다.

<br>

## Reference

[kotlin docs:basic syntax](https://kotlinlang.org/docs/basic-syntax.html)

[why kotlin? : Jetbrain ](https://kotlinlang.org/)

[what is Unit in kotlin?](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-unit/)

[kotlin functions code convention](https://kotlinlang.org/docs/coding-conventions.html#functions)

[Java switch condition by any type](https://openjdk.org/jeps/406)

[Kotlin whitespace code convention](https://kotlinlang.org/docs/coding-conventions.html#horizontal-whitespace)

