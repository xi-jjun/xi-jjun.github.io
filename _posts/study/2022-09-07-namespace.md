---
layout: post
title: "Namespace가 뭐에요"
author: "xi-jjun"
tags: Study

---

# Tag "Study"

이 'study' 태그의 목적은 공부하다가 모르는 것이나, 더 자세히 알고 싶은 내용이 생겼을 때 포스팅 하기 위한 태그이다.

<br>

# What is 'namespace' ?

`namespace`가 과연 무엇일까? 최근 몇 주전에 코딩테스트 언어를 Java에서 C++로 변경했다. Java의 코드가 너무 길기도 길어서 짜증났는데 속도는 C++에 비해 느리고 코드 스타일은 비슷했기에 

> 이럴거면 그냥 C++ 써버릴까...?

라는 생각으로 바꿨다.

그렇게 열심히 레퍼런스 찾아가며 공부를 하던 도중 의문이 생겼다.

```c++
#include <iostream>
#include <vector>

using namespace std; // => 얘는 왜 항상 써주는거지...?

int main() {
  // code...
  return 0;
}
```

어떤 때에는 저 코드를 안쓰고 코드를 실행시키려고 했다가 계속 컴파일 에러가 발생해서 1시간을 삽질한 적도 있었다. 그러다보니 궁금해졌다. 저게 뭐길래 없으면 에러까지 날까?

<br>

## C++ namespace

> In C++, a **namespace** is a collection of related names or identifiers (functions, class, variables) which helps to separate these identifiers from similar identifiers in other namespaces or the global  namespace. - [reference](https://www.programiz.com/cpp-programming/std-namespace)

따라서 '같은 이름의 method, variable, class'이어도 '다른 namespace'를 붙여줌으로써 이를 구별할 수 있음을 뜻한다.

<br>

## Why? 왜 이런 방법을 도입한 것일까?

다음과 같은 상황을 가정해보자.

```c++
...
int show(int element) {
  ...
}
...
```

대충 어떤 계산의 결과를 보여주는 코드라고 해보자..

<br>

```c++
...
int show(int item) {
  ...
}
```

대충 vector를 보여주는 코드라고 해보자...

그리고 이제 내가 위 2개의 라이브러리가 필요하여 가져다 쓴다고 생각해보면, 아래와 같은 코드를 만들 수 있다.

```c++
#include "file1.h"
#include "file2.h"

int main() {
  show(123); // => file1의 show? file2의 show?
  return 0;
}
```

`show()`라는 method가 어떤 라이브러리의 메서드인지 구별을 못하는 문제가 생긴다. 따라서 C++는 `namespace` 라는 것으로 함수, 클래스, 변수 등을 구별해주는 것이다. 다음과 같은 방식으로 써볼 수 있다. [참고](https://learn.microsoft.com/ko-kr/cpp/cpp/header-files-cpp?view=msvc-170)

```c++
// my_class.h
namespace N
{
    class my_class
    {
    public:
        void do_something();
    };

}
```

```c++
// my_class.cpp => 구현체
#include "my_class.h" // header in local directory
#include <iostream> // header in standard library

using namespace N; // => 이렇게 사용!
using namespace std;

void my_class::do_something()
{
    cout << "Doing something!" << endl;
}
```

이처럼 `N` 이라는 이름으로 구별해서 사용해줄 수 있다!

<br>

따라서 내가 매일 쓰는 `using namespace std` 라는 코드는 `std`라는 namespace의 코드를 사용하겠다는 것을 뜻하는 것이다.

<br>

## Reference

[c++ namespace](https://www.programiz.com/cpp-programming/std-namespace)

[microsoft cpp header file](https://learn.microsoft.com/ko-kr/cpp/cpp/header-files-cpp?view=msvc-170)

