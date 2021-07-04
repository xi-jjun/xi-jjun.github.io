---
layout: post
title: "Python-study-1"
author: "xi-jjun"
tags: 동아리

---

# 1주차

1주차입니다! python 스터디 진행방식을 알려드리겠습니다. 해당 주차에 배울 것들에 대해서 미리 말을 하고, 그것들에 대해서 공부를 알아서 해주신 다음 제가 낸 과제를 진행해주시면 됩니다. 기간 내에 제출을 2번이상 못하면 스터디를 나가주시면 됩니다.

본격적으로 python에 대해 배워 볼 것입니다. [노마드 코더(유투브)](https://nomadcoders.co/python-for-beginners/lobby)를 참고하여 스터디를 진행할 것입니다. 해당 영상을 보고 과제를 하신다면 더 도움이 될 것입니다. [노마드 코더(유투브)](https://nomadcoders.co/python-for-beginners/lobby)에 가입을 해주신 다음, 들어주시면 됩니다!! 무료강의에요!(참고하시면 됩니다. 꼭 들으실 필요는 없어요)

- #0 Introduction 을 들어 주세요. Python 설치 등등을 알려줍니다. (0주차랑 비슷. 생략가능)
- 변수와 자료형
- 기본 입출력 함수
- If, else

<br>

### 그 외 도움이 될만한 사이트

1. [테크보이 워니 유투브](https://www.youtube.com/watch?v=M6kQTpIqpLs&t=2s) - 1시간 짜리 강의만 들으면 python을 할 줄 알게 됩니다. 처음하시는 분들에겐 좀 빠를 수 있어요.
2. [나도 코딩 유투브](https://www.youtube.com/watch?v=kWiCuklohdY) - 6시간 짜리 강의 하나만 들으면 됩니다. 초보자에겐 좋은 강의지만, 6시간을 이것만 보기엔 시간이 아까워서 저는 그다지 추천을 드리지 않습니다. 노마드 코더나 테크보이 워니가 어려우면 추천드립니다.

<br>

## 변수와 자료형

```python
# 변수 = value # (datatype)
num1 = 10 # int
char1 = "hello" # string 문자형
char2 = "world!!" # string
double1 = 23.432 # double
```

`python`같은 경우, C언어처럼 `변수` 를 선언할 때 앞에 `자료형`을 명시해줄 필요가 없다. '어떤' 값을 넣는지에 따라 그 `변수`의 `자료형`이 결정되는 것이다. 

<br>

## Casting(형변환)

Datatype(자료형)을 바꾸는 것이다. 예를 들어서 `23.535`이라는 `double`형 데이터를 소수점을 버린 `int`형으로 바꾸고 싶다면,

```python
num1 = 23.535
num2 = int(num1)
```

이런식으로 해주면 된다.

<br>

## 기본 입출력 함수

다음은 코딩의 국룰 코드인 `Hello World!!`를 출력해보자. 

```python
print("Hello World!!")
```

너무 쉽다. C언어 마냥 `#include <stdio.h>...`이런거 할 필요 없이 그냥 저 한줄만 입력하면 우리가 원하는 문자를 화면에 출력할 수 있다.

<br>

이번에는 사용자의 입력을 받는 함수를 써볼 것이다. C언어의 `scanf()`의 기능과 유사하다.

```python
userNum1 = input("숫자 1개를 입력하시오 : ")
print(userNum1)
```

```shell
~/D-Coder-PythonStudy77$ python week01_ex1.py 
숫자 1개를 입력하시오 : 213
213
```

해당 코드와 그에 대한 결과창이다. 우리가 입력한 값을 출력해서 확인해봤다. 하지만 지금 우리의 코드는 언뜻보면 우리가 원하는대로 잘 실행이 되었다고 생각될 수 있는데 사실은 아니다. 우리는 **숫자**를 입력했지만, 출력된 `213`은 `int`형이 아니라는 뜻이다. 아래의 코드를 보자.

```python
userNum1 = input("숫자 1개를 입력하시오 : ")
print(userNum1)
print(type(userNum1)) # type(변수) : 변수의 자료형을 출력
```

```shell
~/D-Coder-PythonStudy77$ python week01_ex1.py
숫자 1개를 입력하시오 : 234
234
<class 'str'>
```

아까와는 달리 `<class 'str'>`가 출력된 것을 볼 수 있다. `userNum1`이라는 변수는 우리가 의도한대로라면 `int`형이어야 했지만 지금 보다시피 `str`형이다. (`str`==문자형) 이것에 대한 해결방법은 한번 찾아보길 바랍니다! 아주 쉽게 찾을 수 있을 거에요!

<br>

## If, Else

```python
# 주석을 순서대로 그대로 읽으시면 됩니다.
if a == b: # a와 b가 같다면,
  print("a == b") # 이 코드를 실행해줘.
elif a == c: # 근데 a와 b가 같지않았는데 a와 c가 같다면,
  print("a == c") # 이 코드를 실행해줘.
else: # a와 b가 같지 않고, a와 c도 같이 않으면,
  print("a != b") # 이 코드를 실행해줘.
```

`if, else`는 위와같이 쓰면 된다.

<br>

## 1주차 과제(7/8 목~7/11 일)

기간 내에 과제 제출 못할 시 2번 이상이 되면 스터디 나가주시면 됩니다.

## 문제1

```console
~/D-Coder-PythonStudy77$ python week01_1.py 
숫자1 입력 : 2
숫자2 입력 : 4
2개의 숫자를 더하면 6 입니다.
```

위와 같은 결과가 나오도록 코드를 작성해 주세요. 

<br>

## 문제2

```python
~/D-Coder-PythonStudy77$ python week01_2.py 
숫자1 입력 : 5
숫자2 입력 : 43
기준 숫자 입력 : 40
num1+num2= 48 는  40 보다 큽니다.
2개의 숫자를 더하면 48 입니다.
```

위와 같은 결과가 나오도록 코드를 작성해 주세요. 

<br>

<br>

<br>

<br>

<br>

<br>

## 문제1 힌트

```python
# week01_1.py
num1 = ??(input("숫자1 입력 : "))
num2 = ??(input("숫자2 입력 : "))

print("2개의 숫자를 더하면",??+??,"입니다.")
```

<br>

## 문제2 힌트

```python
# week01_2.py
num1 = ??(input("숫자1 입력 : "))
num2 = ??(input("숫자2 입력 : "))
standard = ??(input("기준 숫자 입력 : "))

if ??? + ??? > ????:
  print("num1+num2=",??+??,"는 ",????,"보다 큽니다.")
elif ??? + ??? == ????:
  print("num1+num2=",??+??,"는 ",????,"이랑 같습니다.")
else:
  print("num1+num2=",??+??,"는 ",????,"보다 작습니다.")
```

궁금하신 것 있으면 언제든지 물어봐주세요!!

카카오톡으로 답안 제출해 주시면 감사합니다.

