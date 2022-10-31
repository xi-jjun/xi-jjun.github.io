---
layout: post
title: "Wooteco backend first week - Problem4 @CsvSource 입력 issue"
author: "xi-jjun"
tags: wooteco

---

# Wooteco backend first week

우테코 5기를 뽑기 시작했다. 요구사항에 기능정리와 함께 내가 알게된 것들을 정리해볼려고 한다. 1주일치를 한 곳에 모아둘려니...이번 리뷰는 좀 긴 것 같아서 따로 빼서 포스팅을 하게 됐다.

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

## Reference

[@CsvSource - junit docs](https://junit.org/junit5/docs/5.8.1/api/org.junit.jupiter.params/org/junit/jupiter/params/provider/CsvSource.html)

[@CsvSource - ignoreLeadingAndTrailingWhitespace](https://junit.org/junit5/docs/snapshot/api/org.junit.jupiter.params/org/junit/jupiter/params/provider/CsvSource.html)

