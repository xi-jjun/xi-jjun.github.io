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

<br>

## Java 기초 문법

public static void main 에서 메소드 쓸려면 static을 모든 메서드에 붙여줘야 한다 왜???



class = 빵틀

Instance = 빵

this를 언제 쓰는지?

public private 에서 왜 굳이 setter를 쓰ㅡㄴ건가? 그냥 바꾸는거랑 뭔 차이가 있는건지?

-> 조건을 줄 수 있어서 그런가...?



REST : 서버의 응답이 json형태인 것. HTML CSS가 아닌 데이터만 주고받음

browser에서 요청하는 것을 GET



Gradle 배포할 때와 라이브러리 가져올 때 쓴다
