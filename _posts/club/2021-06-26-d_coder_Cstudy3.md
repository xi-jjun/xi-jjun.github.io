---
layout: post
title: "C-study-3"
author: "xi-jjun"
tags: 동아리

---

# 3주차

아래와 같은 것들을 공부하시면 됩니다. [동빈나 유투브](https://www.youtube.com/watch?v=dh4hdtZ00EU&list=PLRx0vPvlEmdDNHeulKC6JM25MmZVS_3nT)를 참고하였으나 순서가 약간 다를 수 있습니다.

- Array(다차원 배열)
- Pointer

<br>

## Array(다차원 배열)

2주차에서는 1차원 배열에 대해서만 알아봤다. 하지만 배열은 선언하기에 따라 2,3차원으로 다양해질 수 있다.

```c
int arr[3][3]; // 2차원 배열
int i = 0, j = 0;

for(i = 0; i < 3; i++) {
  for(j = 0; j < 3; j++){
    arr[i][j] = i + j;
    printf("%d ", arr[i][j]);
  }
  printf("\n");
}
```

```shell
0 1 2 
1 2 3 
2 3 4 
```

간단하게나마 2차원 배열을 선언하고, `for`를 이용하여 값을 주고 출력을 해봤다.

<br>

![Cstudy3_1](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/club/img/Cstudy3_1.png?raw=True){: width="80%" height="65%"}

위 그림을 보면 2차원 배열이 어떤 느낌인지 감이 더 잘오게 될 것이다. 결론적으로

> Array [row] [column] 이 되는 것.
>
> 3차원 배열에 대해서는 array[row] [column] [height] 가 된다.

<sub>row : 행 , column : 열</sub>

<br>

## Pointer

[TCP School](http://tcpschool.com/c/c_pointer_intro)를 약간 참고하여 작성했다.

C언어의 꽃인 `pointer`에 대해 배울 차례이다. 유투브나 인터넷에 다양한 자료를 보면서 본인이 가장 이해가 쉬운 것을 찾길 바란다.

> 말 그대로 **가리키는 것**이다. 어디를? 변수의 `주소`를 가리킨다.

<br>

## 주소값의 이해 - [출처 : TCP School](http://tcpschool.com/c/c_pointer_intro)

데이터의 주소값이란 해당 데이터가 저장된 메모리의 시작 주소를 의미한다. C언어에서는 이러한 주소값을 1바이트 크기의 메모리 공간으로 나누어 표현합니다.

예를 들어, int형 데이터는 4바이트의 크기를 가지지만, int형 데이터의 주소값은 시작 주소 1바이트만을 가리킵니다.

<br>

포인터의 경우 처음 들으면 이해하기가 매우 어렵다. 컴퓨터 구조의 지식에 대해 조금 알고있다면 그나마 이해가 쉽다고 생각한다. 먼저 우리가 변수를 선언할 때 우리는 `변수`와 `값(상수)`에 대해서만 생각했다. 하지만 이제는 변수가 저장되어 있는 `주소`가 중요하다. 아래 그림을 보도록 하자.

![Cstudy3_2](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/club/img/Cstudy3_2.png?raw=True){: width="70%" height="70%"}

```c
int num1 = 2; // 위 그림을 코드로 표현
```

- num1 : `변수`의 이름.
- 2 : `num1`이라는 `변수`의 값.
- &num1 : `num1`이라는 `변수`의 값인 `2`가 담겨져 있는 곳의 `address(주소)`

<br>

>C언어에서 `pointer`란 메모리의 `주소`값을 저장하는 `변수`이다. - [출처](http://tcpschool.com/c/c_pointer_intro)

<br>

```c
int num1 = 2; // 변수의 값을 저장하는 num1
int *ptr = &num1; // num1의 주소값을 저장하는 ptr
```

`int` datatype은 4byte의 크기를 가진다. 이는 메모리 1칸이 1byte의 크기를 가지기 때문에 아래 그림과 같이 표현할 수 있다.

![Cstudy3_3](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/club/img/Cstudy3_3.png?raw=True){: width="35%" height="80%"}

- Addr : 메인 메모리의 `address`. 16진수이다.
- num1 : `int`형 `변수`이므로 4byte 소모한다. 각 메인 메모리의 1칸은 1byte이므로 총 4칸을 사용하여 선언했음을 알 수 있다.
- &num1 : `num1`의 `주소`값이다. 무조건 가장 맨 앞의 `주소`값이 해당 `변수`의 `주소`값이다. 그림에서는 `0x00`번지이다. (만약 `0x03`부터 `2`라는 값이 저장됐다면, `주소`는 `0x03`이다.)

<br>

```c
int num1 = 2; // 변수의 값을 저장하는 num1
int *ptr = &num1; // num1의 주소값을 저장하는 ptr
```

![Cstudy3_4](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/club/img/Cstudy3_4.png?raw=True){: width="70%" height="80%"}

- Addr : 메인 메모리의 `address`. 16진수이다.
- num1 : `int`형 `변수`이므로 4byte 소모한다. 각 메인 메모리의 1칸은 1byte이므로 총 4칸을 사용하여 선언했음을 알 수 있다.
- &num1 : `num1`의 `주소`값이다. 무조건 가장 맨 앞의 `주소`값이 해당 `변수`의 `주소`값이다. 그림에서는 `0x01`번지이다.
- ptr : `포인터 변수`로 선언되었다. 값으로는 `&num1`의 값을 갖고 있다. 따라서 `0x01`값을 갖고 있는 것이다.

`ptr`은 `포인터 변수`로써 선언이 됐다. 따라서 `num1`의 주소를 가리키고 있다. 

> 어떤 변수의 주소를 **가리키고**있기 때문에 `pointer`라 불리는 것이다.

<br>

## Pointer Operator(포인터 연산자)

```c
// 한번 해보세요!
int num1 = 2;
int* ptr = &num1;

printf("%d\n", num1);
printf("%x\n", &num1); // %x : 16진수의 형태로 출력된다.
printf("%x\n", ptr);
printf("%d\n", *ptr);
```

```shell
2
703d0a1c
703d0a1c
2
```

위와 같은 결과가 나온다. 주소는 코드를 실행시킬 때마다 다르게 나오기 때문에 본인의 컴퓨터에서 한번 실행해보는 것을 추천한다.

```shell
~/D-Coder-Study623$ ./test 
2
703d0a1c
703d0a1c
2
~/D-Coder-Study623$ ./test 
2
526141fc
526141fc
2
```

다른 `주소`값이 출력되는 것을 볼 수 있다. 여기서 우리는 `*`와 `&`연산자의 의미를 알 수 있다.

- &(ampersand) : `주소 연산자`. 변수의 앞에 붙이면 해당 변수의 `주소값`을 반환한다.
- `*` : `참조 연산자`. 포인터의 이름이나 주소 앞에 사용. 포인터가 가리키는 주소에 저장된 값을 반환한다.
  - 포인터 : ptr
  - 포인터(ptr)가 가리키는 주소 : &num1
  - 그 주소에 저장된 값 : 2

아까 예시를 다시 보자.

```c
int num1 = 2;
int* ptr = &num1;

printf("%d\n", num1);
printf("%x\n", &num1); // num1 앞에 & 붙였기 때문에 주소값 반환.
printf("%x\n", ptr);
printf("%d\n", *ptr); // num1의 주소값(&num1)을 저장한게 ptr이다. *ptr = *(&num1) = num1 = 2 가 되는 것이다.
```

<br>

<br>

## 과제기한(7/5~7/11)

## 문제1

```c
#include<stdio.h>
void ChangePlusOne(int n) {

	n += 1;

}

int main(void) { // main함수는 건들지 마시오.

	int number = 3;
	printf("%d\n", number);

	number = 5;
	printf("%d\n", number);

	ChangePlusOne(number);
	printf("%d\n", number);

	return 0;
}
```

위 코드의 출력은

```shell
3
5
5
```

이다.

 `pointer`를 배우기 전의 우리라면 당연하게 `3,5,6`이라는 결과가 나왔어야 할텐데 보다시피 예상과는 다른 값이 나왔다. 따라서 `ChangePlusOne()`함수를 알맞게 고쳐서 `3,5,6`이라는 결과를 도출하여라. (**단, 함수의 반환값(return)은 없습니다.**)

<br>

```shell
3
5
6
```

<sub>예상 결과</sub>

<br>

## 문제2

```c
int arr[] = {10,20,30,40,50};
```

위와 같은 배열이 있다. **`*(arr+3)`의 값과 `arr+3` 의 값을 구하여라.**

**단, arr 배열의 첫번째 주소는 `0xF5` 이다. 정답의 형식은 16진수, 10진수, 2진수 아무거나 상관없다.**

- 추가 

  ```c
  char arr2[] = "My name is WakWak";
  ```

  `arr2`배열의 첫번째 주소는 `0x7B`라고 하자. `i`의 `주소값`을 구하여라.

<br>

## 문제3

```c
#include <stdio.h>
 
void swap(int a, int b) {
    int temp;
    temp = a;
    a = b;
    b = temp;
}

int main(void) { // main 함수는 건들지 마시오.
    int n = 3, m = 5;
    
    printf(" 값을 교환하기 전 값 n : %d , m : %d\n",n,m);
    swap(n, m);
    printf(" 값을 교환한 후 값 n : %d , m : %d", n, m);
 
    return 0;
}
```

위 코드의 출력결과를 아래와 같이 바꿔라.

```shell
값을 교환하기 전 값 n : 3 , m : 5
값을 교환하기 전 값 n : 5 , m : 3
```

<br>

<br>

<br>

<br>

<br>

<br>

<br>

궁금하신 것 있으면 언제든지 물어봐주세요!!

E-mail : rlawowns97@naver.com, rlawowns97@gmail.com

카카오톡 가능

<br>

## 참고자료 + 힌트

[문제1 출처](https://prosto.tistory.com/253)

문제2는 검색으로 찾아보세요. 예시 키워드 : 배열 주소값

[문제3 출처](https://jeak.tistory.com/34?category=832913)

