---
layout: post
title: "C-study-2"
author: "xi-jjun"
tags: 동아리

---

# 2주차

아래와 같은 것들을 공부하시면 됩니다. [동빈나 유투브](https://www.youtube.com/watch?v=dh4hdtZ00EU&list=PLRx0vPvlEmdDNHeulKC6JM25MmZVS_3nT)를 참고하였으나 순서가 약간 다를 수 있습니다.

- for
- while
- Array(배열)
- Function(함수 선언)

<br>

## For 

`for`는 반복문을 쓸 때 쓰인다. 예를 들면, 

```c
printf("이거 5번 쓰고 싶어요!!\n");
printf("이거 5번 쓰고 싶어요!!\n");
printf("이거 5번 쓰고 싶어요!!\n");
printf("이거 5번 쓰고 싶어요!!\n");
printf("이거 5번 쓰고 싶어요!!\n");
```

라는 코드가 있다고 생각해보자. 딱 봐도 비효율적이다. 그래서 우리는 `for`를 사용하여 해당 코드를 5번 반복하게 만들 수 있다.

```c
for(i=0; i<5; i++) {
  printf("이거 5번 쓰고 싶어요!!\n");
}
```

아까 코드보다 훨씬 간결해진 것을 보실 수 있다.

<br>

근데 같은 코드만 계속 반복하면 의미가 없다. 다음은 반복횟수에 따라 숫자를 자동으로 증가시키는 코드이다.

```c
for(i=0; i<5; i++) {
  printf("i가 %d이 됐다ㅏㅏㅏ\n", i);
}
```

```console
i가 0이 됐다ㅏㅏㅏ
i가 1이 됐다ㅏㅏㅏ
i가 2이 됐다ㅏㅏㅏ
i가 3이 됐다ㅏㅏㅏ
i가 4이 됐다ㅏㅏㅏ
```

<sub>결과</sub>

1주차에 공부했던 `int`형 변수를 출력하는 방법은 `printf`함수에 `%d`를 넣는 것이다. `i`라는 변수를 `%d`로 보여주겠다 라는 뜻.

<br>

## 이중 For

`for`는 2개를 겹쳐서 사용할 수도 있습니다.

```c
for(i=1; i<=5; i++) {
  for(j=1; j<=5; j++) {
    printf("%d x %d = %d\n", i, j, i * j);
  }
}
```

출력될 결과가 예상 가시나요? 간단하게 곱셈을 해본 것입니다.

```console
1 x 1 = 1
1 x 2 = 2
1 x 3 = 3
...
5 x 1 = 5
5 x 2 = 10
..
5 x 5 = 25
```

<sub>결과</sub>

이처럼 `for`는 2개를 동시에 사용할 수 있다. 물론 3개, 4개도 사용할 수 있다.

<br>

## While

`while`은 주로 무한루프를 구현할 때 많이 썼던 것 같다. `for`와 비슷한 점이 많아 보인다.

```c
while(조건) {
  //우리의 코드
}
```

이런 형식으로 쓰인다. 조건이 `False`가 되면 `while`밖으로 나와지게 된다.

<br>

```c
int i = 0; // 초기화. 안하면 i가 어디서부터 시작하는지 컴퓨터는 모른다.
while(i < 5) {
  printf("i가 %d이 됐다ㅏㅏㅏ\n", i);
  i++; // i값 1 증가
}
```

```console
i가 0이 됐다ㅏㅏㅏ
i가 1이 됐다ㅏㅏㅏ
i가 2이 됐다ㅏㅏㅏ
i가 3이 됐다ㅏㅏㅏ
i가 4이 됐다ㅏㅏㅏ
```

보다시피 i가 5가 되는 순간, `i<5`라는 `while`의 조건을 `False`로 만들기 때문에 `while loop`가 끝나는 것이다.

<br>

```c
while(1) {
  printf("계속 출력된다ㅏㅏㅏㅏ\n");
}
```

```console
계속 출력된다ㅏㅏㅏㅏ
계속 출력된다ㅏㅏㅏㅏ
계속 출력된다ㅏㅏㅏㅏ
...
계속 출력된다ㅏㅏㅏㅏ
계속 출력된다ㅏㅏㅏㅏ
..
계속 출력된다ㅏㅏㅏㅏ
..
```

`while`의 조건에 `1`을 주게 되면 **무한루프**가 된다. 그 이유는 `1`이 `True`라는 의미를 갖기 때문에 `while loop`의 조건이 계속 `True`라서 무한하게 실행되는 것이다. 반대로 `False`는 `0`을 뜻한다.

<br>

## Array

내 생각엔 배열은 중요하다. 사실상 배열을 지금 배운다고 해놨지만 사실은 우린 배열을 썼었다. 1주차에 말이다.

```c
// 배열 선언
int arr[5] = {10,20,30,40,50};
int i = 0;

for(i=0; i<5; i++) {
  printf("arr[%d] = %d\n", i, arr[i]);
}
```

~~코드가 살짝 복잡해진 것 같지만 그렇게 어려운게 아니에요..!! 할 수 있습니다!!~~

```console
arr[0] = 10;
arr[1] = 20;
arr[2] = 30;
arr[3] = 40;
arr[4] = 50;
```

라는 결과가 나오게 된다.

<br>

위에서 우리가 선언했던 배열을 더 자세히 살펴보면,

![Cstudy2_1](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/club/img/Cstudy2_1.png?raw=True){: width="87%" height="22%"}

이런식으로 배열이 이뤄지는 것이다. 현재 이 구조를 잘 익혀두고 이해가 돼야 다음에 배울 **pointer**의 이해가 더 쉬울 것이다.

<br>

아까 우리가 이미 배열을 썼다고 얘기했었는데 바로 이 부분이다.

```c
char hello[10] = "hello";
```

![Cstudy2_2](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/club/img/Cstudy2_2.png?raw=True){: width="90%" height="20%"}

10칸짜리 `char`type의 배열을 선언했지만, 실제로 쓴 공간은 6칸이다.(`NULL`은 문자열 선언할 때 항상 뒤에 붙는다고 생각해주시면 됩니다.) 나머지 공간은 버려지는 것이다. 

<br>

## Function

`함수`같은 경우 생소할 수 있다. `for, while, array, printf, scanf`는 꽤 친숙하지만 `함수`같은 경우 C언어를 포기했었다면 그 이후에 배웠을 내용이기 때문이다. 포기를 안했다 하더라도 나에겐 너무 어려웠어서 나중에서야 겨우 이해했었다.

```c
출력datatype 함수이름(입력 변수) {
  // 이 함수의 코의
  return 출력datatype에 따라 다름;
}
```

이때까지 했던 것들 중 가장 어려워 보인다. 예를 들어 보겠다.

```c
#include <stdio.h>

int adder(int num1, int num2) { // 2개의 int형 수를 받아서 더하는 함수를 만들려고 한다.
  int result = 0;				// adder라는 함수 안에 '지역변수' result를 선언한다.(main함수의 result와 다르다.)
  result = num1 + num2; // result는 이 함수가 받은 2개의 int형 정수를 더한 결과값이다.
  
  return result;				// adder의 반환값(return값)은 'int'형이므로 result(int형)를 반환할 수 있다.
}

int main() {
  
  int a, b, result;		// 여기의 result는 main함수의 '지역변수'이다. adder안에 있는 result와 다르다.
  a = 10;
  b = 20;
  result = adder(a, b); // adder함수에 a,b값을 입력으로 준다. adder함수는 return result 하므로 반환된 값을 main함수의 result에 준다는 코드
  
  printf("result = %d", result);
  
  return 0;
}
```

혹시나 헷갈릴 수 있기에 이번에는 코드 전체를 적었다.(복붙하시면 바로 컴파일 후 실행 시킬 수 있을 겁니다) 주석을 읽으면 어느정도 이해가 될 것이다. 여기서 가장 이해 안될 수 있는 것은 `지역변수`라는 개념이다. 

<br>

## Local variable(지역 변수)

각 함수는 그 함수만의 `변수(variable)`를 갖는다. 해당 `변수`는 함수가 실행될 때 생성되고, 함수가 끝나면 사라진다. 위의 경우를 보자면, `adder()`라는 함수가 생성될 때 result라는 변수가 생기고 `result` 에 `num1+num2`를 넣어준 값을 return(반환)하는 순간 `adder()` 함수가 끝난다. 따라서 `result`도 사라지게 된다.

그렇게 반환된 값은 `main()`의 `변수`인 `result`에 들어가게 되는 것이다. 마찬가지로 `main()`의 끝부분에 `return 0;`으로 0이 반환되면 `main()`이 끝나면서 선언된 `변수`들을 모두 사라지게 된다.

> 결국, 두 함수에 선언된 변수들은 서로에게 영향을 못 준다.

<br>

## 과제 기한 (6/28~7/3)

## 문제1

역 피라미드 문제입니다. 매우 흔한 문제죠.

```console
*****
 ***
  *
```

위와 같은 결과가 나오면 됩니다. **단, 무조건 `for`만 사용하셔야 합니다**

<br>

## 문제2

```console
[1]단 입니다
1 x 1 = 1
1 x 2 = 2
1 x 3 = 3
[2]단 입니다
2 x 1 = 2
2 x 2 = 4
2 x 3 = 6
[3]단 입니다
3 x 1 = 3
3 x 2 = 6
3 x 3 = 9
```

위와 같은 결과가 나오면 됩니다. **단, 무조건 `while`만 사용하셔야 합니다.**

<br>

## 문제3

> 길이가 100이하인 문자열(영어+숫자)을 사용자가 입력하면, 해당 문자열의 길이가 출력되게 하세요. 
>
> **조건**
>
> 1. 문자열의 길이를 찾아내는 '함수'를 만드세요. 함수 이름은 arrayLen()

```console
<예시>
dfls
해당 문자열의 길이는 4 입니다.
```

```c
// 2주차 문제3 코드 형식
#include <stdio.h>

?? arrayLen(char* input) {
  ??
}

int main() {

  char input[100];
  int result = 0;

  scanf("%s", input);
  result = arrayLen(input);
	printf("해당 문자열의 길이는 %d 입니다\n", result);
  
  return 0;
}
```

이번에는 위의 코드에서 `arrayLen()`함수를 완성시켜 보세요. 좀 어려울 수 있습니다. **단, strlen() 함수는 사용하면 안됩니다.**

<br>

<br>

<br>

<br>

<br>

<br>

## 문제1 힌트

```c
// 2주차 문제1
#include <stdio.h>

int main() {

  int i, j, k= 0;

  for(i=0; ??; i++) {

    for(k=0; ??; k++) {
      printf(" ");
    }
    
    for(j=0; ??; j++) {
      printf("*");
    }
    printf("\n");
  }

  return 0;
}
```

아마 이 3문제 중에서는 중간 난이도였을 거라고 생각합니다. 어려우셨다면 말씀해 주세요. 힌트를 보고도 이해가 안가신다면 말씀해 주세요.

<br>

<br>

## 문제2 힌트

```c
// 2주차 문제2
#include <stdio.h>

int main() {

  int i = 1, j = 1;

  while(??) {
    printf("[%d]단 입니다\n", i);
    while(??) {
      printf("%d x %d = %d\n", i, j, i * j);
      j++;
    }
    ?? = ??;
    i++;
  }

  return 0;
}
```

가장 쉬운 문제였습니다. 큰 어려움이 없었을 거라고 믿습니다.

<br>

<br>

## 문제3 힌트

```c
// 2주차 문제3
#include <stdio.h>

int arrayLen(char* input) {
  int i = 0;
  while(input[i] != '\0') {
    ??;
  }

  return i;
}

int main() {

  char input[100];
  int result = 0;

  scanf("%s", input);
  result = arrayLen(input);
  printf("해당 문자열의 길이는 %d 입니다\n", result);
  
  return 0;
}
```

가장 어려운 문제가 아니었을까 합니다. `pointer`의 개념이 쓰였기에 제가 코드 형식을 드린 것입니다. 아까 글에서 언급했던 **문자열 끝에는 항상 `\0`이 붙습니다** 라고 했던게 여기서 쓰이는 것입니다. `\0`은 항상 문자열의 끝에 있기에 `\0`가 감지되면 `while`을 종료시켜서 문자열의 길이를 구하는 것입니다.

<br>

궁금하신 것 있으면 언제든지 물어봐주세요!!

E-mail : rlawowns97@naver.com, rlawowns97@gmail.com

카카오톡 가능

