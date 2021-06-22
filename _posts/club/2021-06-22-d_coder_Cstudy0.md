---
layout: post
title: "C-study-0"
author: "xi-jjun"
tags: 동아리

---

# 동아리 Tag

동아리를 운영하고 있는 입장에서 스터디를 더 원활하게 운영하기 위해 만든 태그입니다. 많이 부족하겠지만 이게 도움이 되길 바랍니다.

<br>

# 0주차

0주차에는 스터디를 진행하기 위한 개발환경에 대해 설명해드릴 예정입니다. 그리고 대략적인 계획도 알려드릴거에요.

<br>

## IDE(Integrated Development Environment)

IDE에는 `visual studio`나 많은 개발 툴이 있지만 쓰기에 힘들고 설치하는 것이 처음 하시는 분들께는 복잡할 수 있습니다. 저는 [여기](https://replit.com/)를 추천드립니다. 사이트에서 가입만 하면 무료로 컴파일을 할 수 있습니다. 다른 개발툴들을 설치하는데 어려움을 느끼거나 시간이 오래걸리는 것을 방지하기 위해 저는 replit.com 사이트를 추천드립니다.

<br>

## Replit.com 사용방법

![club0_1](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/club/img/club0_1.png?raw=True){: width="80%" height="38%"}

먼저 로그인을 해줍니다. 아이디가 없으면 `sign up`버튼을 눌러서 회원가입을 해줍니다.

<br>

![club0_2](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/club/img/club0_2.png?raw=True){: width="57%" height="60%"}

빨간색 박스로 된 `+New repl`을 눌러서 새로운 프로젝트를 만들어 줍니다.

<br>

![club0_3](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/club/img/club0_3.png?raw=True){: width="70%" height="38%"}

1. 본인이 작성하고 싶은 언어를 선택하세요. 우리는 C언어 스터디니깐 `C`를 선택해주시면 됩니다!!
2. 프로젝트 이름입니다. 원하시는거 아무거나 해주세요. 저는 `D-Coder-Study(6/23~)`으로 했습니다.
3. `Create repl`을 클릭해 주시면 프로젝트가 만들어집니다!

<br>

![club0_4](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/club/img/club0_4.png?raw=True){: width="80%" height="22%"}

왼쪽 위의 초록색 박스 : 우리가 설정한 프로젝트 이름

Run : main.c 코드 실행

Shell : 우리가 코드를 진짜로 실행시킬 곳

여기서 우리는 한개의 프로젝트 안에서 많은 파일을 만들 것이기 때문에 `shell`에서 코드를 실행시킬 줄 알아야 합니다.

<br>

![club0_5](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/club/img/club0_5.png?raw=True){: width="80%" height="38%"}

여기서부터는 이해가 안될 수 있습니다. C언어랑은 관계없는 것들이라 이해하실 필요는 없지만 코드를 실행시키는 방법이기에 잘 보셔야 합니다.

1. 왼쪽에서 `main.c`파일을 `hello.c`로 파일 이름을 바꿔줍니다.

2. 오른쪽에서 `console`이 아닌 `shell`버튼을 클릭해주세요. 그러면 `~/본인이 설정한 프로젝트 이름`이 나타날 것입니다. 저 같은 경우는 `~/D-Coder-Study623` 입니다. 

3. 이제 코드를 컴파일 하기 위해 `shell`에 아래와 같이 입력하시면 됩니다.

   ```shell
   gcc "우리가 작성한 코드의 이름" -o "컴파일 될 파일의 이름"
   ~/D-Coder-Study623$ gcc hello.c -o hello 
   ```

4. 컴파일 된 코드는 왼쪽에 `hello`라는 이름으로 나옵니다. `hello.c`와는 다른 파일입니다. 컴파일된 코드를 실행시키기 위해선 아래와 같이 입력하면 됩니다. `hello.c`라는 우리가 작성한 코드가 `hello`라는 이름으로 컴파일이 되었습니다. 우리는 컴파일된 `hello`를 실행하는 것입니다.

   ```shell
   ./"우리가 컴파일 시킨 파일의 이름"
   ~/D-Coder-Study623$ ./hello
   ```

5. 결과는 `Hello World!!`가 잘 출력되는 것을 보실 수 있습니다.

   ```console
   Hello World!!
   ```

<br>

## 0주차 과제(~6/25 금)

6/23부터 시작이지만 그 전에 과제 하나만 내겠습니다. 과제는 6/25(금)까지 해주시면 됩니다. 

```c
/* 코드 형식 */
#include<stdio.h>

int main(void) {
  
  ???? // 여기에 알맞은 코드를 넣어주세요!
  
  return 0;
}
```

결과

```console
안녕하세요! 스터디 파이팅입니다!!
```

위와 같은 결과가 나오도록 코드를 작성해 주세요. 궁금하신 것 있으면 언제든지 물어봐주세요!!

E-mail : rlawowns97@naver.com, rlawowns97@gmail.com

카카오톡 가능

