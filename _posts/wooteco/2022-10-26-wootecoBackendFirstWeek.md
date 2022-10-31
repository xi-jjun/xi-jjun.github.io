---
layout: post
title: "Wooteco backend first week"
author: "xi-jjun"
tags: wooteco

---

# Wooteco backend first week

우테코 5기를 뽑기 시작했다. 요구사항에 기능정리와 함께 내가 알게된 것들을 정리해볼려고 한다.

<br>

# 새롭게 해보게 된 것들

## 기능 단위 개발

이전까지는 한번에 모든 기능을 개발하는 무지몽매한 자세를 가졌었다. 그러나 이번에 `기능 단위 개발`에 집중해서 개발을 했더니 실제로 다음과 같은 느낌을 받았다.

1. 테스트 코드 작성하기가 쉬워진다.
2. 테스트 코드를 작성하니 디버깅이 쉽고 실수를 찾기 쉽다.
3. 한번에 모든걸 만들려면 복잡한데 부품을 만들어가며 마지막에 부품을 모아 상품을 만드니, 이전 코드들에 대한 신뢰도도 올라가고 복잡해지지가 않는다.

<br>

## Test Case작성하며 좋았던 점

`Problem1`의 `getPlayerMaxScore` method를 만드는데 내가 예상한 값이랑 다르게 나왔다. 분명히 그 전의 unit test에서 값들이 잘 나오는 것을 확인했으나 예상한 결과랑 다르게 나왔는데 알고보니 

```java
private static final int LEFT_PAGE = 0;
private static final int RIGHT_PAGE = 0;
...
```

.... `RIGHT_PAGE` 는 1의 값을 가져야 `List`의 1번째 index에 접근할 수 있는데 복사 붙여넣기만 해놓고 바꾸지를 않았던 것이다...

이렇듯 테스트 코드의 중요함을 알게 됐다.

### 테스트 코드 적는데 시간이 더 소요된다?

이 부분에 대해서는 얼마전 `Clean Architecture`라는 책의 머릿말을 읽고 확고한 신념을 가지게 됐다.

> 정확하게 가는 길이 빨리 가는 길이다.

이번에 느꼈듯 테스트 코드의 작성이 곧 정확하게 가는 길이고, 나중에 문제가 생겼을 때 전체를 다 보지않고 테스트 케이스를 보면서 어떤 것이 문제인지 잘 알 수 있다고 생각했다.

<br>

## @ParameterizedTest

테스트를 진행할 때에 매번 for loop에 데이터를 넣고 만들었다. 하지만 그렇게 했을 때 실패하는 케이스가 생기는 순간 테스트가 멈추기에... 이런 부분에 있어서 일단 실패하더라도 다른 케이스의 결과를 볼 수 있어서 좋았다.

`@CsvSource`, `@MethodSource`와 같이 사용했다.

<br>

## Regex 

예전에 `regex`의 성능이슈...에 대해 얼핏 들었던 것 같아서 사용할 때 한번 찾아봤다. Java11 docs의 `Pattern`에 대해 찾아보면,

> A regular expression, specified as a string, must first be compiled into an instance of this class. 

문자열로 명시된 정규표현식은 클래스 인스턴스에서 **먼저 컴파일되어야** 한다고 적혀있다. 그리고 다음과 같은 절차로

```java
Pattern p = Pattern.compile("a*b");
Matcher m = p.matcher("aaaaab");
boolean b = m.matches();
```

> The resulting pattern can then be used to create a [`Matcher`](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/regex/Matcher.html) object that can match arbitrary [character sequences](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/lang/CharSequence.html) against the regular expression.  All of the state involved in performing a match resides in the matcher, so many matchers can share the same pattern.  

수행되는게 전형적이라고 한다. 순서를 보자면,

1. `a*b` 라는 `regex`를 `Pattern`으로 컴파일한다.
2. 위 과정에서 `Pattern`이라는 객체를 사용하여 임의의 문자열를 `regex`에 대해 일치시킬 수 있는 `Matcher` 객체를 만든다.
3. `match`에 수행되는 모든 state는 `matcher` 에 있기에, 다른 `matcher`들이 같은 `Pattern` 을 공유할 수 있다고 한다.

따라서 regex `Pattern` 을 `static final`로 선언해서 재사용성을 높였다. 사용법에 대해서는 `String`의 `replaceAll` method내부를 봤다. 

```java
...
public String replaceAll(String regex, String replacement) {
    return Pattern.compile(regex).matcher(this).replaceAll(replacement);
}
...
```

기본적으로 `regex` pattern을 1번만 사용하는 것으로 보인다.

<br>

# @CsvSource 에 대해...

우테코 1주차 4번 과제 진행 도중, 기능은 어렵지 않게 만들었으나 예외 케이스를 찾던 도중, 신기한 현상을 발견했다.

> `StringBuilder`로 공백을 추가한 결과 문자열은 좌우공백이 사라진다...?

말도 안되는 소리라고 생각했으나 일단 보이는 결과가 그랬기에... 혼란스러웠다. 하지만 개발에는 변하지않는(immutable) 진리가 존재한다.

> 컴퓨터는 잘못하지 않는다. 잘못은 사람이 하는 것이다. - by 나의 학교 선배

내가 어떤 것을 잘못했는지 원인을 파악하기 위해서 `test code`를 작성해서 찾아보도록 하자.

## 현재 분석하려는 문제는 1주차 Problem4에 관련된 것임을 미리 말씀드립니다.

<br>

## Problem : 좌우공백이 사라졌다?!

문제는 내가 만든 기능 `convertTextToOpposite` method를 테스트 하면서 발견됐다.

```java
@ParameterizedTest(name = "Text ''''{0}'''' ==opposite all==> ''''{1}''''")
@CsvSource(value = {..., "  Wooteco is my LIFE  ~ !  :  Dllgvxl rh nb ORUV  ~ !  "}, delimiter = ':')
void convertTextToOppositeTest(String text, String expected) {
  // when : text 의 문자들을 모두 반대로 바꿀 때
  String convertedResult = Problem4.convertTextToOpposite(text);

  // then : 잘 바뀌었는지 확인
  assertThat(convertedResult).isEqualTo(expected);
}
```

위 테스트에서 `"  Wooteco is my LIFE  ~ !  "`라는 테스트 입력의 좌우에는 2칸의 공백이 존재한다. `convertTextToOpposite` method의 기능은 '문자열에서 알파벳만 모두 반대로 바꾸는 기능'이었다. 따라서 공백과 같은 알파벳이 아닌 문자에 대해서는 그대로 출력이 되어야 한다. 

위 테스트를 실행하면 문제 없이 통과된다.

![wooteco5_firstweek1](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/wooteco/img/wooteco5_firstweek1.png?raw=True){: width="70%"}

하지만 뭔가 안좋은 느낌이 들어서 프린트를 해보니 다음과 같은 결과를 보여줬다.

![wooteco5_firstweek2](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/wooteco/img/wooteco5_firstweek2.png?raw=True){: width="70%"}

입력과 출력에 공백이 사라진 것이다. (~~지금 보면, '입력'에도 공백이 사라진 것을 본 순간 당연하게도 `@CsvSource`문제임을 생각했어야 했는데 당황한 것 같다...~~)

`$`는 문자열의 시작과 뒤를 확인하기 위해 붙여준 문자이다. 테스트 코드는 아래와 같다.

```java
@ParameterizedTest(name = "Text ''''{0}'''' ==opposite all==> ''''{1}''''")
@CsvSource(value = {..., "  Wooteco is my LIFE      ~ !:  Dllgvxl rh nb ORUV      ~ !"}, delimiter = ':')
void convertTextToOppositeTest(String text, String expected) {
  // when : text 의 문자들을 모두 반대로 바꿀 때
  String convertedResult = Problem4.convertTextToOpposite(text);
  System.out.println("input text = START$" + text + "$END");
  System.out.println("convertedResult = START$" + convertedResult + "$$END");

  // then : 잘 바뀌었는지 확인
  assertThat(convertedResult).isEqualTo(expected);
}
```

<br>

## 가정 1 : StringBuilder로 문자를 모으는 과정에서 공백이 무시됐다.

```java
public static String convertTextToOpposite(String text) {
    return text.chars()
            .map(c -> convertCharacterToOpposite((char) c))
            .collect(StringBuilder::new, StringBuilder::appendCodePoint, StringBuilder::append)
            .toString();
}
```

테스트를 진행한 `convertTextToOpposite` 코드이다. `StringBuilder`로 collect를 하여 `toString()`을 써서 문자열로 결과를 반환했다.

`StringBuilder`가 문제였을까...? 를 처음에 생각하며 테스트를 진행했었다.

```java
@DisplayName("StringBuilder 의 좌우에 공백을 삽입했을 때 만들어지는 문자열 확인")
@Test
void stringBuilderWhitespaceTest() {
  // given : StringBuilder 에 좌우공백을 append 로 추가한 객체
  StringBuilder sb = new StringBuilder();
  sb.append(' ');
  sb.append(' ');
  sb.append(' ');
  sb.append("Hello");
  sb.append(' ');
  sb.append(' ');

  // when : StringBuilder 객체를 문자열로 바꿨을 때
  String result = sb.toString();

  // then : 주어진 문자열과 같은지 확인
  final String expected = "   Hello  ";

  assertThat(result).isEqualTo(expected);
}
```

![wooteco5_firstweek3](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/wooteco/img/wooteco5_firstweek3.png?raw=True){: width="70%"}

테스트 결과는 당연하게도 같다고 나왔다. `StringBuilder`로 모으는 과정에서 좌우 공백이 무시된 것은 틀렸다. 

<br>

## 가정 2 : convertCharacterToOpposite method에서 공백을 그대로 반환하지 않았다.

진짜 말도 안되는 소리였다. `convertTextToOpposite`는 `convertCharacterToOpposite`를 사용하여 문자열에서 각각의 문자를 반대로 바꾸는 method이다. 만약 `convertCharacterToOpposite`가 공백을 그대로 반환 못했다면 중간에 존재하는 공백 또한 없어야 할 것이었다.

<br>

## Test convertTextToOpposite with whitespace

결국 간단하게 `convertTextToOpposite`을 다시 테스트하기로 했다.

```java
@DisplayName("convertTextToOpposite method 로 문자열을 변환할 때 좌우공백이 사라지는지 확인")
@Test
void convertTextToOppositeWhitespaceIgnoreTest() {
  // given : 좌우에 공백이 있는 문자열
  final String stringWithWhitespaceOnSides = "    Hello World  ";

  // when : 좌우 공백이 존재하는 문자열을 모두 반대로 바꿨을 때
  String convertedResult = Problem4.convertTextToOpposite(stringWithWhitespaceOnSides);

  // then : 변환된 결과(result)의 좌우 공백이 사라졌는지 확인
  final String expected = "    Svool Dliow  ";
  System.out.println("convertedResult = " + convertedResult);
  System.out.println("expected = " + expected);
  assertThat(convertedResult).isEqualTo(expected);
}
```

![wooteco5_firstweek4](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/wooteco/img/wooteco5_firstweek4.png?raw=True){: width="80%"}

...? 정상적으로 잘 동작함을 볼 수 있다.

이 때 머릿속에서는 '아.. 설마 입력 자체가 잘못 들어왔다면...?' 이라는 생각을 하게 됐고, `@CsvSource`를 의심하게 됐다.

<br>

## ignoreLeadingAndTrailingWhitespace in @CsvSource

결국 공식 문서를 찾아보고서야 알게 됐다.

> Except within a quoted string, leading and trailing whitespace in a CSV column is trimmed by default. This behavior can be changed by setting the [`ignoreLeadingAndTrailingWhitespace()`](https://junit.org/junit5/docs/snapshot/api/org.junit.jupiter.params/org/junit/jupiter/params/provider/CsvSource.html#ignoreLeadingAndTrailingWhitespace()) attribute to `true`.

인용구(따옴표)안에 있는 문자열이 아니라면 `@CsvSource`는 기본적으로 좌우 `trim`을 한다고 되어 있다. 그리고 이러한 옵션을 바꾸기 위해서는 `ignoreLeadingAndTrailingWhitespace`을 `false`로 해야한다.

<br>

## Test

```java
@ParameterizedTest(name = "Text ''''{0}'''' ==opposite all==> ''''{1}''''")
@CsvSource(value = {..., "  Wooteco is my LIFE    ~ !  :  Dllgvxl rh nb ORUV    ~ !  "}, delimiter = ':', ignoreLeadingAndTrailingWhitespace = false)
void convertTextToOppositeTest(String text, String expected) {
  // when : text 의 문자들을 모두 반대로 바꿀 때
  String convertedResult = Problem4.convertTextToOpposite(text);
  System.out.println("input text = START$" + text + "$END");
  System.out.println("convertedResult = START$" + convertedResult + "$$END");

  // then : 잘 바뀌었는지 확인
  assertThat(convertedResult).isEqualTo(expected);
}
```

![wooteco5_firstweek5](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/wooteco/img/wooteco5_firstweek5.png?raw=True){: width="80%"}

`trim`이 되지 않은채로 의도한 문자열로 출력이 되는 것을 볼 수 있었다.

<br>

## 느낀점

문제의 원인을 찾아가는 행위 자체는 좋았으나 파악을 할 때 좀 더 생각을 해야싶다. 위에서도 적어놨듯이 처음에 잘못된 입력이 들어가는 것만으로 `@CsvSource`의 문제라고 생각했어야 했는데 그런 점을 못보고 애먼 테스트를 해서 시간을 낭비했다는 생각이 든다.

<br>

## 매일 추가되는 사항이 있다면 업데이트 된다...

<br>

## Reference

[@CsvSource - junit docs](https://junit.org/junit5/docs/5.8.1/api/org.junit.jupiter.params/org/junit/jupiter/params/provider/CsvSource.html)

[@ParameterizedTest  test case naming](https://lannstark.tistory.com/52)

[Java Stream API를 써야 하는 이유 - tecoble](https://tecoble.techcourse.co.kr/post/2021-05-23-stream-api-basic/)

[Java Stream.reduce() - baeldung](https://www.baeldung.com/java-stream-reduce)

[@MethodSource - baeldung](https://www.baeldung.com/parameterized-tests-junit-5)

[git commit 취소](https://inpa.tistory.com/entry/GIT-%E2%9A%A1%EF%B8%8F-git-add-commit-push-%EC%B7%A8%EC%86%8C%ED%95%98%EA%B8%B0-%F0%9F%92%AF-%EC%A0%95%EB%A6%AC-git-reset-restore-clean)

[Regex performace](https://goodgid.github.io/Regex-Performance-Caution/)

[Pattern class - Java11 docs](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/regex/Pattern.html)

[@CsvSource - ignoreLeadingAndTrailingWhitespace](https://junit.org/junit5/docs/snapshot/api/org.junit.jupiter.params/org/junit/jupiter/params/provider/CsvSource.html)

