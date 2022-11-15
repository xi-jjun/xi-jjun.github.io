---
layout: post
title: "우테코 백엔드 3주차 : java-lotto"
author: "xi-jjun"
tags: wooteco

---

# 우테코 백엔드 3주차 : java-lotto

우테코 5기를 뽑기 시작했다. 요구사항에 기능정리와 함께 내가 알게된 것들을 정리해볼려고 한다.

<br>

# 알게된 것 또는 도전해본 것

## printf에서 '%'출력하기..

이거는 너무 기초지만 Java에서는 의외로 많이 접해보기 힘들어서 까먹기 쉬운 것 같다. `C,C++`같은 경우는 `printf`를 자주 쓰기에 escape 문자에 대해서 자주 쓰게 되기에 익숙하다. 그러나 `Java`의 경우에는 `System.out.println()`을 쓰기에 `Format Specifiers`를 잘 안쓰게 됐던게 컸다.

- **%c** character
- **%d** decimal (integer) number (base 10)
- **%e** exponential floating-point number
- **%f** floating-point number
- **%i** integer (base 10)
- **%o** octal number (base 8)
- **%s** String
- **%u** unsigned decimal (integer) number
- **%x** number in hexadecimal (base 16)
- **%t** formats date/time
- **%%** print a percent sign
- **\%** print a percent sign

이렇게... [여기서](https://www.digitalocean.com/community/tutorials/java-printf-method) 복붙해왔으니 나중에는 까먹지 말도록 하자.

<br>

## String isBlank() vs isEmpty()

이거는 매번 볼 때마다 헷갈려서 찾아보는 것 같다. 그래서 이번에 아예 깔끔하게 글로 정리하려 한다.

```java
public class BlankAndEmptyTest {
    public static void main(String[] args) {
        String str = "";
        System.out.println("'' is empty? => " + str.isEmpty()); // true
        System.out.println("'' is blank? => " + str.isBlank()); // true

        String str1 = "   ";
        System.out.println("'  ' is empty? => " + str1.isEmpty()); // false
        System.out.println("'  ' is blank? => " + str1.isBlank()); // true
    }
}
```

`공백`만으로 이뤄진 문자열이라면 `blank`, 문자열 길이 자체가 0인 경우에는 `empty`이자 `blank`이다.

### isEmpty()과 isBlank() 내부구현

```java
// String.java 
...
public boolean isEmpty() {
    return value.length == 0;
}
...
public boolean isBlank() {
    return indexOfNonWhitespace() == length();
}
```

역시 직접 보니깐 더 명확하다.

<br>

## Count list elements frequency by Stream API

이번에 '사용자의 로또들의 결과를 `Rank`횟수로 나타내는 기능'이 필요했다. 이게 말이 좀... 이상하게 쓴 것 같은데 예시로 설명하자면

```text
가정 : 사용자는 4개의 로또를 샀고, 5등 1번, 3등 1번에 당첨되었다.
== 출력 ==
5등 1번
4등 0번
3등 1번
2등 0번
1등 0번
```

위와 같이 출력해야 하는 상황에서 **'당첨된 횟수를 `Rank`별로 카운팅 하는 기능'**이 필요했던 것이다.

> 깔끔하게 `stream`으로 해결할 수 없을까...?

라는 생각을 하게 됐고 다음과 같은 [해결책](https://stackoverflow.com/questions/505928/how-to-count-the-number-of-occurrences-of-an-element-in-a-list)을 만날 수 있었다. 구현을 위해서 채용했지만 **어떤 원리로 동작하는지 알고 써야** 진정한 개발자라고 할 수 있겠다. 적어도 나는 그렇게 생각한다.

### Collectors.counting()

> ... It returns the total count of elements in the stream which reach the collect() method...

`collect()` method로 도달하는 모든 요소들의 개수를 카운팅 해주는 기능이라고 한다...

> 아니 그러면 쓸모가 없잖아;; `count()` terminal method써서 든든~ 하게 구하면 되는거 아닌가? 그리고 애초에 빈도수 구한다면서 뭔 말도 안되는 기능이냐?

애초에 '빈도 수'라는 것은 '어떤 숫자에 대한 빈도 수'를 구하는 것이다. 지금 우리는 '어떤 숫자에 대한' 의 정보가 빠진 채로 코드를 작성한 것이다. 그리고 애초에 `Collectors.counting()`이라는 method의 기능은

> **Collectors counting()** method is used to count the number of elements passed in the stream as the parameter.

stream에서 parameter로 들어오는 요소들의 개수를 카운팅 하기 위해 사용된다고 한다. 그 자체로는 빈도 수를 계산하기 위해 생긴 것이 아니다. 우리의 목적은 `List`와 같은 단순 나열 자료형으로 이루기 어렵다. 따라서 구하려는 결과에 맞게 `Map`이라는 자료구조를 만들어야 한다.

<br>

### Collectors.groupingBy()

> The **groupingBy()** method of Collectors class in Java are used for grouping objects by some property and storing results in a Map instance.

그러니깐 `SQL`의 `GROUP BY` 와 비슷하다고 생각하면 될 것 같다. 우리는 방금 위에서 '어떤 숫자 에 대한 빈도수'를 구할 때 **'어떤 숫자'**에 대한 정보를 깜빡했었다. 그러나 `groupingBy()`로 '각 숫자에' 대한 정보를 모으라고 명시를 해줌으로써 해결할 수 있다.

```java
protected Map<Rank, Long> countResultRankFromUserLotteries() {
    return userLotteries.stream()
          .map(userLotto -> winningLotto.compareTo(userLotto))
          .filter(rank -> !rank.isOutOfRank())
          .collect(Collectors.groupingBy(Function.identity(), Collectors.counting()));
}
```

`Function.identity()`는 collect로 들어온 parameter 그 자신을 뜻하고, 거기에 매칭되는 값은 `Collectors.counting()`으로서 사용자 로또들의 결과인 `Rank`의 빈도수를 계산한다는 것이다.

```java
static <T> Function<T, T> identity() {
    return t -> t;
}
```

`Function.identity()`를 보ㄴ 위와 같이 정의되어 있다.

<br>

<br>

## Mock test

이번에 3주차를 하면서 알게된 가장 큰 수확이 아닐까 싶다. 2주차에서는 그저 '가장 작은 단위 테스트'만을 진행했다. 통합테스트를 안했던 이유는

> 단위 테스트가 다 성공했는데 같은 테스트를 다시 해야하나...?

라는 생각 때문이었다. 

하지만 2주차가 끝나고 생각해보니 과연 '코드를 결합하는 과정에서의 오류'에 대해서는 검증이 되지 않은 불안정한 코드라고 생각이 됐다. 이번에 통합 테스트도 진행을 하게된 이유였다.

`Mock`객체를 어떻게 만들지..? 어렵지는 않을까..? 라는 생각과는 달리 매우 단순했다.

```java
@DisplayName("로또 기계 통합 테스트")
@Test
void lottoMachineBuilderTest() {
    /*
    ===== given =====
    lottoFactory Mock 객체 주어짐 => 4000원을 넣으면 아래와 같은 로또 번호 생성. 테스트 난수 생성 역할
    lottoMachineBuilder => 통합 테스트하려는 객체
    purchaseAmount : 사용자가 구매할 로또 금액 입력
    winningLottoNumber : 당첨로또 번호 입력
    winningLottoBonusNumber : 당첨로또 보너스 번호 입력
     */
    final LottoFactory lottoFactory = mock(LottoFactory.class);
    when(lottoFactory.issueLottoNumbersByPurchaseAmount(4000)).thenReturn(List.of(
        ...
    ));

    LottoMachineBuilder lottoMachineBuilder = new LottoMachineBuilder();
    ...

    // when : 통합 테스트
    lottoMachineBuilder.init(lottoFactory)
        ...

    // then
    assertThat(outputStream.toString())...
}
```

`lottoMachineBuilder` class는 `lottoFactory`를 필요로 한다. 이럴 때 가짜 `lottoFactory` 객체를 주입하고, `when(), thenReturn()`등과 같은 method로 필요한 동작을 정의해주게되면 우리가 의도한 상황에서의 테스트가 가능해진다는 것이다.

### 최선일까...?

좋은 테스트라면 로직의 변화 없이는 테스트의 변화도 없어야 한다는 것이고 유연해야 한다고 생각한다. 그러나 위에서 보면

```java
when(lottoFactory.issueLottoNumbersByPurchaseAmount(4000)).thenReturn(List.of(
    ...
));
```

`4000`으로 정의한 부분이 마음에 걸렸다. 이 테스트는 반드시 4000이라는 parameter가 들어오는 상황에서만 정의가 된다는 것인데 이게 과연 좋은 테스트 코드가 맞을까...?

### anyInt()

`mock`은 처음 사용했기에 다소 생소한 부분이 많아서 4000을 그대로 사용했다. 하지만 좀 더 찾아보니 `anyInt()`라는 함수를 `mockito` package에서 제공을 했다. 따라서 어떠한 정수가 들어오게 되더라도 인식을 해준다는 뜻이다! 이로써 4000일 때 뿐만 아니라 1000, 5000일 때도 테스트가 가능하게 되어 좀 더 유연한 테스트 코드를 작성할 수 있게 됐다고 생각한다.

<br>

## List.of에 대해...

이번에는 '로또 번호를 출력할 때에 **오름차순**으로 정렬'하라는 요구사항이 있었고, 따라서 나는 `Collections.sort(LIST)`로 해결하려 했었다. 그러나 다음과 같은 에러가 테스트케이스에서 발생했다.

```shell
java.lang.UnsupportedOperationException
	at java.base/java.util.ImmutableCollections.uoe(ImmutableCollections.java:72)
	at java.base/java.util.ImmutableCollections$AbstractImmutableList.sort(ImmutableCollections.java:111)
	at java.base/java.util.Collections.sort(Collections.java:145)
	...
	at lotto.core.LottoMachineBuilder.purchaseLotteries(LottoMachineBuilder.java:28)
	at lotto.core.LottoMachine.start(LottoMachine.java:16)
	at lotto.Application.main(Application.java:11)
	at lotto.ApplicationTest.runMain(ApplicationTest.java:59)
	at camp.nextstep.edu.missionutils.test.NsTest.run(NsTest.java:35)
	at lotto.ApplicationTest.lambda$기능_테스트$0(ApplicationTest.java:19)
	at camp.nextstep.edu.missionutils.test.Assertions.lambda$assertRandomTest$4(Assertions.java:89)
```

![wooteco5_3th_week1_1](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/wooteco/img/wooteco5_3th_week1_1.png?raw=True){: width="70%"}

> 갑자기...? `UnsupportedOperationException`

너무 뜬금없었다고 생각했던 이유가 그도 그럴게 단순하게 '정렬 코드 1줄'만 추가했을 뿐이었다. 하지만 얼마전에 스터디에서 토론했던 'getter method는 정말로 주기만 하는가?' 라는 대화가 머리에 스쳤다.

간략하게 말하자면 getter method자체는 주기만 하는게 아니라, 우리가 해당 method에 `add`와 같은 연산을 할 수도 있다. 따라서 이는 `getter`의 역할만을 온전히 수행한다고 볼 수 없다는 것이었다. 이 때 사용할 수 있는 `Collections.unmodifiableList(list)`에 대해서도 말했다.

> 설마 `List.of()`도 불변 클래스인가...?

싶어서 바로 확인했더니 아래와 같았다.

```java
...
static <E> List<E> of(E e1, E e2, E e3, E e4, E e5, E e6) {
    return new ImmutableCollections.ListN<>(e1, e2, e3, e4, e5, e6);
}
...
```

그렇다. 바꿀 수 없는 배열에 '정렬'이란 불순한 행동을 취하려다가 생긴 오류였다. 하지만 조금 억울할 수 있다고 생각되는 것은 이건...기본적으로 우테코 측에서 제공한 test case였기 때문이다. 

```java
@Test
void 기능_테스트() {
      assertRandomUniqueNumbersInRangeTest(
              () -> {
                  run("8000", "1,2,3,4,5,6", "7");
                  assertThat(output()).contains(
                      ...
                  );
              },
              List.of(8, 21, 23, 41, 42, 43),
              List.of(3, 5, 11, 16, 32, 38),
              List.of(7, 11, 16, 35, 36, 44),
              List.of(1, 8, 11, 31, 41, 42),
              List.of(13, 14, 16, 38, 42, 45),
              List.of(7, 11, 30, 40, 42, 43),
              List.of(2, 13, 22, 32, 38, 45),
              List.of(1, 3, 5, 14, 22, 45)
      );
}
```

위 코드는 기본적으로 제공해준 테스트 코드이다. 약간 억울했지만 '요구사항'에 맞춰서 개발하는 것이란 이런게 아닐까 싶다.

<br>

## 우테코 탓?

근데 진짜 우테코 탓을 약간이라도 해도 될까? 에 대해서 생각해봤다. 진심으로 그래도 될까...?

`로또 번호`에 대해서 생각해보자. 

1. 랜덤하고 겹치지 않는 1~45 사이의 숫자 6개로 이뤄짐
2. 생성되고 '바뀔 일'이 전혀 없음

간략하게 생각해본 특성들이다. 2번째를 집중해서 보자. '바뀔 일'이 없다... 라는 속성이랑 위의 상황이랑 뭔가 비슷해 보인다.

[우테코 2기 선배님이 작성하신 글](https://tecoble.techcourse.co.kr/post/2020-04-28-ask-instead-of-getter/)을 보면

> 무분별한 getter? 객체에 메시지를 보내 객체가 로직을 수행하도록 하자
>
> ...

라고 적힌 글을 볼 수 있다. 그리고 그 조금 아래에

> getter를 무조건 사용하지 말라는 말은 아니다.
>
> 당연히 getter를 무조건 사용하지 않고는 기능을 구현하기 힘들것이다. 출력을 위한 값 등 순수 값 프로퍼티를 가져오기 위해서라면 어느정도 getter는 허용된다. 그러나, Collection 인터페이스를 사용하는 경우 외부에서 getter메서드로 얻은 값을 통해 상태값을 변경할 수 있다.
>
> ```java
> public List<Car> getCars() {
>     return cars;
> } (x)
> 
> public List<Car> getCars() {
>     return Collections.unmodifiableList(cars);
> } (o)
> ```
>
> 이처럼 `Collections.unmodifiableList()` 와 같은 `Unmodifiable Collecion` 을 사용해 외부에서 변경하지 못하도록 하는 게 좋다.





로또 번호는 생성되면 바뀔 일이 없고, 따라서 '불변객체'가 되어야 안정적인 서비스가 될 것이다.

### 아니 그걸 누가 건드나요..?

> 모르죠...

모른다. 그러니깐 무서운 것. A개발자의 의도를 B라는 개발자가 파악 못한채 `로또 번호`배열을 마구잡이로 수정한다면 결국엔 의도치 않은 결과(🪲`bug`)가 생길 수 있다는 것이다. 회사에서도 '슈퍼 개발자 1명'을 뽑는데 집중하기 보다는 '슈퍼 버그 개발자 1명'을 안뽑기 위해서 더 노력을 하는 것처럼 우리는 최악의 결과를 애초에 원천차단하기 위해서 코드로 우리의 의도를 전달할 수 있어야만 한다.

<br>

## 느낀점

그렇다. 지금 나의 코드는 이미 제출이 되었고 돌이킬 수 없다. 하지만 이를 이제라도 알았으니 다음에는 더 잘하면 될 일이다. 테스트 코드 짜는데 진짜 많은 시간이 걸렸으나 그만큼 가치있는 곳에 자원을 썼다고 생각한다. 테스트 없는 코드는 결국 언제 깨질지 모르는 유리 위에서 계속 코드를 작성하는 것과는 다름없다고 생각하기 때문이다. 

<br>

## Reference

[java printf formatting](https://www.digitalocean.com/community/tutorials/java-printf-method)

[java stream counting frequency](https://stackoverflow.com/questions/505928/how-to-count-the-number-of-occurrences-of-an-element-in-a-list)

[java Collectors.counting()](https://www.geeksforgeeks.org/java-8-collectors-counting-with-examples/)

[java Collectors.groupingBy()](https://www.geeksforgeeks.org/collectors-groupingby-method-in-java-with-examples/)

[Mock test](https://www.crocus.co.kr/1556)

[java immutable list](https://www.baeldung.com/java-immutable-list)








