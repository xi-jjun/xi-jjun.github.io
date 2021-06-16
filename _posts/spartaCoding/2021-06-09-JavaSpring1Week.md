---
layout: post
title: "Sparta Coding(Java Spring) - 1"
author: "xi-jjun"
tags: SpartaCoding
---

# Java Spring 수업 시작!!

코딩보다는 그에 기초한 지식들이 먼저고, 완벽하게 알아놔야 한다고 생각한다. 하지만 한이음에서 무료로 강의를 제공하기에 이를 버리기엔 아깝다고 생각돼서 또 다시 강의를 듣게 됐다.

Java를 주력 언어로 선택할 것이기 때문에 저번에 들었던 <왕초보 웹개발반 30기> 에서처럼 대충대충 빨리빨리 할 일은 없어야 한다. 그래서 이번 강의는 '왜?', '어떻게?' 라는 질문을 계속 던져가면서 진행할 예정이다.

<br>

## API(Application Programming Interface)

진짜 많은 영상과 글을 찾아봐도 감이 잘 안오는 단어이다. 

>인간이 아닌 코드와 코드가 소통하기 위해 만들어진 것. 키보드와 같은 느낌 - 노마드 코더(유투브)

> 은행 창구와 같은, 하나의 "약속" 입니다. 정해진대로 요구를 하면, 정해진 결과물을 돌려주는 창구이죠. - 스파르타코딩

> 어떤 응용 프로그램에서 데이터를 주고 받기 위한 방법.

> 개발자가 스스로 얻지 못하는 정보도 있다. 버스 정보라든가 , 날씨 정보라든가 , 도서관 정보 등의 데이터들이다. 대부분 국가, 기업, 기관등에서 해당 정보들을 공유한다. 해당 데이터들을 쉽게 사용할수 있도록 간단하고 쉽게 제공해주는것을 오픈API라고 한다.  - https://namjackson.tistory.com/27

<br>

## 웹의 동작

![spartaSpring1_1](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/spartaCoding/img/spartaSpring1_1.png?raw=True){: width="70%" height="30%"}

1. 우리가 브라우저(구글 크롬)로 네이버 도메인 입력. -> request

   => 이 때 서버가 만들어놓은 'API'라는 창구에 미리 정해진 약속대로 보내는 것.

2. 네이버 서버는 요청한 HTML을 우리에게 준다 -> response

이렇게 request와 response으로 이뤄지는 프로토콜이 바로 HTTP 이다.

<br>

### 웹에서 데이터만 받을 때(HTML은 안받고)

보통 영화 티켓을 사는 페이지로 이동하게 되면 해당 HTML을 서버에서 받아오게 되고, 브라우저는 HTML을 우리에게 화면으로 보여준다. 그리고 티켓을 예매하기 위해 자리를 고를 때 내가 고른 자리는 골랐다고 '표시'가 돼야 한다. 하지만 HTML상에서 표시를 하려면 '요청(request)'가 돼야 하는데 요청을 하려면 새로고침이 돼야 한다. 그렇게 매번 사소한 몇가지 때문에 새로고침을 할 수는 없기에 데이터만 받아오는 것이다.

![spartaSpring1_2](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/spartaCoding/img/spartaSpring1_2.png?raw=True){: width="70%" height="30%"}

위 같은 방식으로 서버 DB에서 '데이터'만 받아와서 HTML을 업데이트 시킨다. 이렇게 되면 굳이 HTML 전부를 다시 받아올 필요 없이 우리가 필요한 데이터만 받아와서 바로 반영할 수가 있다.

<br>

![spartaSpring1_3](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/spartaCoding/img/spartaSpring1_3.png?raw=True){: width="60%" height="47%"}

<sub>JSON format</sub>

웹에서는 JSON 형식으로 데이터를 주고 받는다.

<br>

### JSON(JavaScript Object Notation)

> JavaScript 객체문법을 따르는 문자 기반의 데이터 포맷 ([출처](https://developer.mozilla.org/ko/docs/Learn/JavaScript/Objects/JSON))

* JavaScript 객체문법과 매우 유사하지만 JavaScript가 아니더라도 JSON을 읽고 쓸 수 있는 기능이 다수의 프로그래밍 환경에서 제공된다.
* JSON은 문자열 형태로 존재. 네트워크를 통해 전송할 때 아주 유용. 데이터에 억세스하기 위해서는 네이티브 JSON 객체로 변환돼야 한다.
* Javascript 프로그램에서 로드하고 점/브라켓 표현법을 통해 객체 내 데이터에 접근할 수 있다.

Q1) 네이티브 JSON 객체로 변환한다는 것이 뭔 말이냐?

A1) 문자열에서 네이티브 객체로 변환하는 것은 파싱(Parsing)이라고 한다.

Q2) Parsing은 무엇이냐?

A2) 일련의 문자열을 의미있는 token으로 분해하고 이들로 이루어진 parse tree를 만드는 과정. 향후 parsing에 대한 포스팅을 따로 해서 다루도록 하겠다. 찾다가 너무 많았다.

<br>

### Spring

요청에 따라 눈에 보이는 것들 또는 데이터를 제공해주는 스프링은 "서버"를 담당한다. 그리고 스프링은 자바 언어 바탕으로 만드는 것이다. 본 강의에서는 스프링으로 서버를 만드는 것이 목적이다.

<br>

## 그 외

class = 빵틀

Instance = 빵

REST : 서버의 응답이 json형태인 것. HTML CSS가 아닌 데이터만 주고받음. 향후 더 정확하게 다뤄봐야겠다.

browser에서 요청하는 것을 GET

Gradle 배포할 때와 라이브러리 가져올 때 쓴다

<br>

## 1주차 후기

웹개발 종합반처럼 빨리빨리 진행 못하는 부분들이 많았다. 그리고 기말고사 기간이었기 때문에 졸업작품 만들랴 논문 쓰랴 하는 외부적인 것들도 진행을 많이 막았다. 기본적으로 Intellij IDEA라는 새로운 개발툴을 사용하는 점에 있어서도 매우 낯설었다. vscode처럼 간단하지가 않았었다. 그래도 최근에 Intellij IDEA라는 툴을 많이 쓴다고 들었기에 이런 트랜드(?)를 따라가게 해주는 강의인 것 같아서 좋았다.
