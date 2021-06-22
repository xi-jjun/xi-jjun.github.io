---
layout: post
title: "C-study-1"
author: "xi-jjun"
tags: 동아리

---

# 1주차

1주차에는 본격적으로 C언어를 배워볼 것입니다. 아래와 같은 것들을 공부하시면 됩니다. [동빈나 유투브](https://www.youtube.com/watch?v=dh4hdtZ00EU&list=PLRx0vPvlEmdDNHeulKC6JM25MmZVS_3nT)를 참고하여 1주차 진도를 정했습니다.

- 변수와 상수
- 자료형
- 연산자
- if, else 조건문

<br>

### 사이트 추천

- [동빈나 유투브](https://www.youtube.com/watch?v=dh4hdtZ00EU&list=PLRx0vPvlEmdDNHeulKC6JM25MmZVS_3nT) : 앞으로 글을 쓸 때 참고할 유투브 입니다. 잘 가르칩니다.

- [C언어 코딩 도장](https://dojang.io/course/view.php?id=2) : 개념을 잘 풀어 써놨습니다. 당장 C언어 문법이 궁금하면 여기서 찾아보시면 됩니다.

- [홍정모 유투브](https://www.youtube.com/watch?v=PDM_w2b4UA0&list=PLNfg4W25Tapyl6ahul_8VS_8Tx3_egcTI) : C언어에 대해 좀 깊고 제대로 알고 싶은 분들께 추천합니다. 

<br>

## 변수와 상수(Variables and Constants)

변수(variable) : 값이 변할 수 있는 수.

상수(constant) : 숫자.

```c
// 변수 = 상수; 의 형태로 변수에 값을 준다.
int num1 = 103; // num1 : 변수, 103 : 상수
```

<br>

## 자료형(Data Type)

변수를 선언할 때에는 자료형을 명시해줘야 합니다. int, float..등등 말이죠. 가장 많이 쓰게 되는 변수형은 `int`, `float`, `double`, `char` 정도 되는 것 같습니다.

- `int` : 정수형 변수를 선언할 때 사용합니다.

  ```c
  int num1 = 29; // 29는 정수
  printf("num1 : %d", num1); // int형을 출력하기 위해 %d 사용
  ```

- `float` : 실수형 변수를 선언할 때 사용합니다.

  ```c
  float num1 = 34.545; // 34.545는 실수
  printf("num1 : %f", num1); // float형 출력 위해 %f 사용
  ```

- `double` : 실수형 변수를 선언할 때 사용합니다. 

  ```c
  double num1 = 34.453; // 34.453는 실수
  printf("num1 : %lf", num1); // double형 출력 위해 %lf 사용
  ```

- `char` : 문자형 변수를 선언할 때 사용합니다.

  ```c
  char a = 'h'; // h 는 문자
  printf("num1 : %c", num1); // char형 출력 위해 %c 사용. '문자' 하나만 출력.
  char a[10] = "hello"; // hello 는 문자열
  printf("num1 : %s", num1); // char형 출력 위해 %s 사용. '문자열'로 문자들을 출력.
  ```

<br>

## 연산자(Operator)

변수로 선언된 숫자들을 +,-,x,/ 를 하기 위해선 연산자가 필요합니다. 기본적인 것들만 적었습니다.

```c
// + 연산
result = num1 + num2;
// - 연산
result = num1 - num2;
// x 연산
result = num1 * num2;
// / 연산
result = num1 / num2;

// ++ 연산
num1++; // 이 줄의 명령이 끝난 후에 num1에 +1
++num1; // 이 줄의 명령이 시작할 때에 num1에 +1
```

<br>

## if , else(조건문)

조건문은 특정 조건일 때 해당 코드를 실행시킬지 정할 수 있습니다.

```c
if(a == b) {
  printf("a와 b는 같아요!");
}
else if(a == c) {
  printf("a와 c는 같아요!");
}
else {
  printf("저 위 2개의 경우 말고 다른 것들이면 출력!");
}
```

여기서 `a==b`라는 연산자를 보실 수 있습니다. 이 연산자의 의미는 **a와 b가 같다**라는 의미입니다.

따라서 `if(a == b)`를 풀어서 설명하자면, **만약 a와 b가 같다면, {코드}를 실행해라** 라는 뜻입니다.

<br>

```c
if(10 > 5) {
  printf("10은 5보다 큽니다.");
}
else {
  printf("10은 5보다 작습니다.");
}
```

이렇게 조건에 `>, <`를 쓸 수도 있습니다. 결론적으로,

>조건문의 괄호 안 식이 `True`면 코드를 실행하고, `False`면 실행을 안하는 것입니다.

<br>

## 1주차 과제(6/23 수요일~6/27 일요일)

6/23부터 시작이지만 그 전에 과제 하나만 내겠습니다. 과제는 6/25(금)까지 해주시면 됩니다. 

코드를 `shell`에서 실행시키는 방법은 [0주차 스터디](https://xi-jjun.github.io/2021-06-22/d_coder_Cstudy)를 봐주세요.

## 문제1

```shell
~/D-Coder-Study623$ ./dataType 
459
79.563004
1.594900
a
hello
world
hello world
```

위의 결과가 나오도록 해보세요.

<br>

## 문제2

```shell
~/D-Coder-Study623$ ./ifElse 
숫자 하나를 입력하세요 : 20
숫자 하나를 더 입력하세요 : 49
20가 49 보다 작습니다.
```

위의 결과가 나오도록 해보세요. 숫자를 직접 입력받아 주세요.

<br>

문제 1,2의 힌트는 밑에 적어놓겠습니다. 헷갈리시는 분들은 밑에 힌트를 보시고 진행해주세요.

<br>

<br>

<br>

<br>

<br>

<br>

## 문제1 힌트

```c
// dataType.c 파일
#include <stdio.h>

int main(void) {

  int num1 = ???;
  float num2 = ???;
  double num3 = ???;
  char a = ??;
  char hello[10] = ?????;
  char world[10] = ?????;

  printf("??\n", num1);
  printf("??\n", ??);
  printf("??\n", ??);
  printf("??\n", ?);
  printf("??\n", ????);
  printf("??\n", ????);
  printf("?? ??\n", ????, ????);

  return 0;
}
```

??에 들어갈 코드를 작성하시면 됩니다.

<br>

## 문제2 힌트

```c
// ifElse.c 파일
#include <stdio.h>

int main(void) {

  int num1, num2;
  
  printf("숫자 하나를 입력하세요 : ");
  scanf(" ?? ", ??);

  printf("숫자 하나를 더 입력하세요 : ");
  scanf(" ?? ", ??);

  if( ?? ) {
    printf("%d가 %d 보다 큽니다.\n", ??, ??);
  } else {
    printf("%d가 %d 보다 작습니다.\n", ??, ??);
  }

  return 0;
}
```

궁금하신 것 있으면 언제든지 물어봐주세요!!

E-mail : rlawowns97@naver.com, rlawowns97@gmail.com

카카오톡 가능

