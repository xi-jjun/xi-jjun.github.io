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

