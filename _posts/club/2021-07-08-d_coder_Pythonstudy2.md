---
layout: post
title: "Python-study-2"
author: "xi-jjun"
tags: 동아리

---

# 2주차

2주차입니다! 아래 내용을 공부한 다음에 과제를 해결해주세요!

- While, for
- List, dictionary, tuple

<br>

## While

```python
while "조건":
  # 코드
```

위와 같은 형태로 쓸 수 있다. `조건`이 참이 되면 계속 `# 코드`를 실행하고, 거짓이 되면 `# 코드`가 실행 안되고 끝나게 된다. 다음 예시 코드를 보자.

```python
count = 0
while True:
  count += 1
  print(count)
```

```shell
1
2
3
... (무한반복)
```

`while`의 조건이 `True`(항상 참)이기 때문에 코드가 무한번 반복된다. 따라서 우리는 적절한 조건을 제시하여 우리가 의도한대로 코드를 실행시켜야 한다.

<br>

## For

```python
for i in range(3):
  print(i)
```

```shell
0
1
2
```

C언어에서 썼던 것과는 많이 다르다. 일단 설명을 하자면, 

> `i`가 `range(3)`이라는 횟수동안 우리의 코드를 반복하는 것.

그러면 여기서 `range()`라는 함수가 뭔지 궁금할 것이다. `range(num)`이란 `0 ~ num`까지의 범위의 숫자들 이라고 생각하면 된다. 더 자세한 이용법은 인터넷에 많이 나와있으니 찾아보길 바란다.

<br>

```python
arr = ["Hello", "Bye", "TangTang"] # list
for i in arr:
  print(arr)
```

```shell
Hello
Bye
TangTang
```

`arr`라는 list를 만들고 `for`로 출력을 해본 것이다. 이처럼 python의 `for`는 **숫자의 범위 뿐만 아니라, 배열의 요소**로도 반복을 할 수 있다.

<br>

## List

쉽게 말하자면 배열이다.

```python
arr1 = [1, 2, 3, 4]
arr2 = ["a", "b", "c", "d"]
arr3 = ["Hello", 2021, True, "What?"]
```

이런식으로 우리가 원하는 것들을 하나의 배열로 저장할 수 있다는 것이다. 

```python
arr1[0] # 1
arr2[3] # "d"
arr2[-1] # "d"
arr2[-3] # "b"
arr3[4] # Error. 0번째부터 카운트하기 때문이다.
```

배열의 요소 하나를 가져올 때 이러한 특징들이 있다. 배열의 가장 맨 앞에 오는 요소는 0번째이다. 그리고 가장 마지막에 오는 요소는 -1번째이다. 따라서 이러한 특성을 잘 알아놓으면 나중에 코딩할 때 좋다. 각 요소들은 수정도 가능하다.

<br>

```python
arr1 = [1,2,3]
arr1.append(6)
print(arr1)
```

```shell
[1,2,3,6]
```

이런식으로 `append()`함수를 사용하여 배열의 가장 마지막 부분에 요소를 추가할 수도 있다.

<br>

## Tuple

튜플의 가장 큰 특징은 ( ) 안에 쓰이고 **수정이 불가**하다는 점이다. 앞서 봤던 리스트는 배열의 요소가 수정이 가능한 반면, 튜플은 그렇지가 않다.

```python
tu1 = (1, 2, 3)
tu2 = ("a", ) # 한개만 선언하면 꼭 , 를 붙여줘야 한다.
```

<br>

## Dictionary

`Key`와 `Value`값으로 이뤄진 배열같은 것이다. { }안에 쓴다는 점이 다르다.

```python
dict1 = {Key1:Value1, Key2:Value2, Key3:Value3, ...}
```

```python
dict1 = {"name":"xi-jjun", "age":25, "weight":100, "univ":"Donnguk Univ"}
print(dict1["name"]) # "xi-jjun"
print(dict1["age"]) # 25
```

이런식으로 요소를 찾을 때 `Key`값으로 찾는다. 리스트에서는 `[0]`과 같은 index로 찾았다면 딕셔너리에서는 위와 같이 `[Key]`로 찾는 다는 것이 다르다.

<br>

## 2주차 과제(7/12~7/18)

기간 내에 과제 제출 못할 시 2번 이상이 되면 스터디 나가주시면 됩니다.

## 문제1(난이도 중)

```python
# 딕셔너리는 이걸로 쓰셔도 됩니다.
personality = {
  "name":"Kim Jae Jun",
  "age":25,
  "friends":None,
  "grade":[20,30,50]
}
```

```shell
~/D-Coder-PythonStudy77$ python week02_1.py 
{'name': 'Kim Jae Jun', 'age': 25, 'friends': None, 'grade': [20, 30, 50]}
추가할 KEY 값 : face
추가할 VALUE 값 : trash
{'name': 'Kim Jae Jun', 'age': 25, 'friends': None, 'grade': [20, 30, 50], 'face': 'trash'}
찾을 정보의 KEY : age
25
계속1 / 종료0 0
```

위와 같은 결과가 나오도록 코드를 작성해 주세요. 딕셔너리에 요소를 추가하는 방법을 찾으셔야 할 것입니다. 딕셔너리에서 요소를 찾는 방법 중 `get`이라는 함수가 쓰일 수도 있습니다.

<br>

## 문제2(난이도 중상)

```shell
~/D-Coder-PythonStudy77$ python week02_2.py 
*** 성적분포 프로그램 ***
성적 1 입력(종료:-1) : 10
성적 2 입력(종료:-1) : 20
성적 3 입력(종료:-1) : 54   
성적 4 입력(종료:-1) : 23 
성적 5 입력(종료:-1) : 75 
성적 6 입력(종료:-1) : 90
성적 7 입력(종료:-1) : 13
성적 8 입력(종료:-1) : 66
성적 9 입력(종료:-1) : 88
성적 10 입력(종료:-1) : 24
성적 11 입력(종료:-1) : -1
성적 그래프
00~40점:*****
41~60점:*
61~80점:**
81~100점:**
```

직접 성적을 입력하여 해당 분포에 존재하는 사람 수 만큼 `*`을 그리세요. `print()`는 항상 실행되면 줄 바꿈을 합니다. 그걸 방지하는 방법은 `print("와아아아ㅏ", end="")`입니다. 뒤에 `end=""`를 붙여 주시면 줄바꿈이 안됩니다. 배열의 길이를 아는 방법 중 하나는 `len(arr1)`이라는 함수입니다. 해당 함수를 사용하면 arr1의 길이를 알 수 있습니다.

<br>

<br>

<br>

<br>

<br>

<br>

## 문제1 힌트

```python
personality = {
  "name":"Kim Jae Jun",
  "age":25,
  "friends":None,
  "grade":[20,30,50]
}

print(personality)

mode = 1
while mode ?? 0:
  add_info_KEY = input("추가할 KEY 값 : ")
  add_info_VALUE = input("추가할 VALUE 값 : ")
  personality[????] = ????

  print(personality)

  get_info = input("찾을 정보의 KEY : ")
  print(personality.get(???,"Don't exist!"))

  mode = int(input("계속1 / 종료0"))
```

<br>

## 문제2 힌트

```python
print("*** 성적분포 프로그램 ***")

score_list = [[], [], [], []]
score_graph = ["00~40점:", "41~60점:", "61~80점:", "81~100점:"]
score = 0
i = 1

while score ?? -1:
  print("성적",i,"입력(종료:-1) : ",end="")
  score = int(input())
  i += 1
  if score == -1:
    break
  
  if score > 80:
    score_list[?].append(???)
  elif score > 60:
    score_list[?].append(???)
  elif score > 40:
    score_list[?].append(???)
  else:
    score_list[?].append(???)

print("성적 그래프")
for k in range(?):
  print(score_graph[k], end="")
  for i in range(len(?????)):
    print( ? )
  print( ? )
```

궁금하신 것 있으면 언제든지 물어봐주세요!!

카카오톡으로 답안 제출해 주시면 감사합니다.

