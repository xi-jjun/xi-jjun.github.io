---
layout: post
title: "Long vs Int in C"
author: "xi-jjun"
tags: Study

---

# Tag "Study"

이 'study' 태그의 목적은 공부하다가 모르는 것이나, 더 자세히 알고 싶은 내용이 생겼을 때 포스팅 하기 위한 태그이다.

<br>

# Long(4byte) vs Int(4byte) in C

갑자기 운영체제 복습을 하고 백준 문제를 풀려고 했을 때 의문이 생겼다.

![lvsint1](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/study/img/lvsint1.png?raw=True){: width="65%" height="80%"}

> C언어에서 long(4byte)와 int(4byte)는 크기가 같은데 왜 구분을 하는 것일까??? Long 안쓰고 그냥 int쓰면 안되는건가??

<br>

답을 찾기 위해서 인터넷을 돌아다니던 도중 어떤 [블로그](https://sckllo7.tistory.com/entry/32bit%EC%99%80-64bit%EC%9D%98-C-%EC%9E%90%EB%A3%8C%ED%98%95Data-Type-%ED%81%AC%EA%B8%B0-%EC%B0%A8%EC%9D%B4)글을 보게 됐다. 옛날에 16bit OS에서는 `int`의 크기가 2byte라고 했다. 또 어떤 [블로그](https://gudwns999.tistory.com/53)에서는 OS가 16/32/64bit 일 때 `int`의 크기가 다르다고 했다. 따라서 결론적으로는

> `int` size : 16bit→2byte, 32bit→4byte, 64bit→4byte
>
> `long` size : 16bit→4byte, 32bit→4byte, 64bit→8byte

가 된다...

...진짜??? 라며 좀 더 찾아봤다.

<br>

[해당 글](https://stackoverflow.com/questions/7456902/long-vs-int-c-c-whats-the-point)을 읽어봤다. stackoverflow 사이트에 있는 질문글인데 답변을 살펴보자. (대충 의역했습니다...)

- 답변 1

  C/C++ 를 쓸 때 모든 datatype은 architecture 와 compiler에 따라 다르다. 어떤 system에서는 `int`가 4byte일 수도 있지만, 다른 systems에서는 2byte, 8byte일 수 있다. 결국 정의된 것이 아니라 컴파일러에 의해 결정된다.

  - 답변 1에 대한 내 생각

    이해는 갔지만 [여기서](https://sckllo7.tistory.com/entry/32bit%EC%99%80-64bit%EC%9D%98-C-%EC%9E%90%EB%A3%8C%ED%98%95Data-Type-%ED%81%AC%EA%B8%B0-%EC%B0%A8%EC%9D%B4) `int`의 크기를 실질적으로 System vendor에서 정한다고 했다. System vendor가 뭔지를 아직 몰라서... 답변이 완벽하게 맞는지 판단을 못하고 다른 답변을 찾으려 했다.

    <br>

- 답변 2

  다음과 같은 것들이 보장된다.

  - `char` : is at least 8 bits (1 byte by definition, however many bits it is)
  - `short` : is at least 16 bits
  - `int` : is at least 16 bits
  - `long` : is at least 32 bits
  - `long long` : (in versions of the language that support it) is at least 64 bits

  → 위의 목록에 있는 각 datatype은 적어도 이전 datatype만큼 크기가 큽니다(단, 동일할 수도 있음).

  따라서 사용자가 적어도 32bit가 필요하다면 `long`을 쓰는 것이고, 16bit 이상의 어느정도 빠른 datatype이 필요하다면 `int`를 써라.

  - 답변 2에 대한 내 생각

    꼭 `int`가 4byte라는게 아니라 **적어도** 어느정도의 크기를 보장해준다는 것 같다.

    <br>

- 답변 3

  `long`은 `int`와 크기가 같지 않다. 이것은 *적어도* `int`와 크기가 같다는 것이다. C++03 standard (3.9.1-2):을 참고하면,

  > There are four signed integer types: “signed char”, “short int”, “int”, and “long int.” In this list, each type provides at least as much storage as those preceding it in the list. Plain ints have the natural size suggested by the architecture of the execution environment); the other signed integer types are provided to meet special needs.

  나름 필요한 부분을 해석해보자면...

  > `int`는 실행환경의 architecture에 의해 정해지는 크기를 가진다.

  - 답변 3에 대한 내 생각

    위 답변을 읽고, 블로그를 돌아다니며 검색을 해본 결과 이 답변이 가장 와닿았다. 일단 공식 문서를 근거로 들어서 신뢰감이 높았다. 


<br>

## 결론

stackoverflow의 답변과 여러 검색을 해본 결과 과거에 16bit OS일 때 `int`가 2byte, `long`이 4byte 였을 때가 있었는데 32/64bit OS로 발전함에 따라 최소한으로 보장해주는 크기가 늘어났어서 32bit일 때 크기가 4byte로 표시된 것 같다. 하지만 결론적으로 크기가 정해진 것이 아니라 **최소한**의 크기를 보장해준다고 생각하면 될 것 같다.

