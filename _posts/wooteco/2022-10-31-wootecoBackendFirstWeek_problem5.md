---
layout: post
title: "가독성 좋은 test code에 대해서"
author: "xi-jjun"
tags: wooteco

---

# Wooteco backend 1st week

우테코 5기를 뽑기 시작했다. 요구사항에 기능정리와 함께 내가 알게된 것들을 정리해볼려고 한다. 1주일치를 한 곳에 모아둘려니...이번 리뷰는 좀 긴 것 같아서 따로 빼서 포스팅을 하게 됐다.

<br>

# 가독성 좋은 Test Code에 대해서

이번에 또 따로 포스팅하게 된 이유

> 내가 작성한 테스트 코드는 과연 의미가 있을까?

'테스트 코드는 작성하면 좋지!' 라는 생각을 갖고 이번에 처음으로 작성하며 과제를 진행했다. 그러다 문득 '내가 작성한게 과연 의미가 있을까..?'라는 생각을 하게 됐다. 그러한 생각을 하게된 이유는 

> 테스트는 상황을 가정하고 하는게 아닌가...?

라는 생각 때문이다. 말이 너무 추상적인 것 같아서 구체적으로 설명하자면, 케이스를 나누지 않고 '그냥 unit테스트를 진행하고 끝'났다는게 문제라고 생각했다. 아래 코드를 보며 뭔 말인지 설명하겠다.

```java
@ParameterizedTest(name = "Validation Test : {0} : {1}")
@MethodSource("generatePlayerInputAndExpectedResult")
void validationTest(List<Integer> pages, boolean expected) {
    // when : 플레이어가 고른 책의 페이지가 유효한지 검증할 때
    boolean validationResult = Problem1.isInvalidInput(pages);

    // then : 유효한지 확인
    assertThat(validationResult).isEqualTo(expected);
}
```

`1st Problem1`이다. 플레이어의 pages가 유효한지 검증해주는 `isInvalidInput`이라는 method를 테스트하는 코드이다. 입력은 `@MethodSource`로 아래와 같이 넣어주었다.

```java
private static Stream<Arguments> generatePlayerInputAndExpectedResult() {
    final boolean INVALID = true;
    final boolean VALID = false;

    return Stream.of(
        Arguments.of(Arrays.asList(1, 2), INVALID),
        Arguments.of(Arrays.asList(2, 3), INVALID),
        Arguments.of(Arrays.asList(132, 133), INVALID),
        Arguments.of(Arrays.asList(399, 400), INVALID),
        Arguments.of(Arrays.asList(0, 1), INVALID),
        Arguments.of(Arrays.asList(-123, -122), INVALID),
        Arguments.of(Arrays.asList(1111, 1112), INVALID),
        Arguments.of(Arrays.asList(3, 5), INVALID),
        Arguments.of(Arrays.asList(0), INVALID),
        Arguments.of(Arrays.asList(133), INVALID),
        Arguments.of(Arrays.asList(97, 98), VALID),
        Arguments.of(Arrays.asList(243, 244), VALID),
        Arguments.of(Arrays.asList(123, 124), VALID),
        Arguments.of(Arrays.asList(99, 100), VALID),
        Arguments.of(Arrays.asList(), INVALID)
    );
}
```

<br>

## 문제라고 생각한 것 : 케이스를 나누지 않았다는 것

> 뭐가 문제라는거야;; 

라는 생각을 할 수도 있다. 코드 자체와 검증 자체는 틀리지 않았을 수 있다. 내가 생각한 문제의 상황은

> **케이스를 나누지 않은 것**

위 코드는 테스트를 하게 되면 아래와 같이 나온다.

![wooteco5_firstweek6](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/wooteco/img/wooteco5_firstweek6.png?raw=True){: width="50%"}

나는 [나의 포트폴리오](https://www.notion.so/e582a7eea8ff4b24a8ba1e21c9d3f8cd)에 적었듯이 '가독성 좋은 코드를 지향한다'라고 적어놨다. 그러나 위 테스트 결과로는 도대체 `true`가 유효한건지, `false`가 유효한지 헷갈릴 수 있다는 것이다. 

### isInvalidInput의 결과가 true면 invalid라는 뜻 아닌가...?

>맞다.

물론 여기서는 method naming이 `isInvalidInput`으로 true, false에 대해 테스트 결과를 예측하기 쉽다. 그러나 일반적인 경우에서 위와 같은 테스트를 하게 되면 맞는지 틀리는지 모르게 될 수 있다는 것이다.

<br>

## 내가 지향한 케이스를 나눈 테스트 코드

```java
@ParameterizedTest(name = "Validation Test : {0} is invalid")
@MethodSource("generatePlayerInvalidInput")
void invalidInputTest(List<Integer> invalidPages) {
  // when : 플레이어가 고른 책의 페이지가 유효한지 검증할 때
  boolean invalidResult = Problem1.isInvalidInput(invalidPages);

  // then : 유효한지 확인
  final boolean INVALID = true;
  assertThat(invalidResult).isEqualTo(INVALID);
}
```

![wooteco5_firstweek7](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/wooteco/img/wooteco5_firstweek7.png?raw=True){: width="50%"}

이렇게 테스트 결과에 `invalid`라는 글자를 적었기에 좀 더 가독성 있게 되면 좋을 것 같았다. 그리고 각 method의 역할을 줄였기 때문에 OOP의 single responsibility 정책도 지키는 좋은 코드라 생각한다.

<br>

## 결과

![wooteco5_firstweek8](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/wooteco/img/wooteco5_firstweek8.png?raw=True){: width="50%"}

좀 더 알아보기 쉬운 테스트 결과 메세지가 되었다. 이번 케이스는 리팩토링을 하여 제출할 생각이다.

<br>

## Next...

우테코가 진행중이고, 주말에 농협은행 코테와 논술로 인하여... 어서 빨리 못한 나머지 문제들을 해결해야 한다. 따라서 나의 생각만을 빠르게 정리하고 다음에 기회가 된다면 다시 제대로 정리하고 싶다.

<br>
