---
layout: post
title: "HTTP Method 속성"
author: "xi-jjun"
tags: Network

---

# HTTP

Inflearn `김영한` 강사님의 [모든 개발자를 위한 HTTP 웹 기본 지식](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/) 을 듣고 정리하는 글입니다.

<br>

# HTTP Method 속성

## 안전 (Safe Methods)

> 호출해도 리소스를 변경하지 않는다.

- GET, HEAD : 조회는 리소스를 변경하지 않기 때문에 안전하다.

<br>

## 멱등 (Idempotent Methods)

> 한 번 호출하든 2번 호출하든 100번 호출하든 결과가 똑같다.

- GET : 몇번 조회해도 같은 조회에 대해서는 같은 결과가 조회된다.
- PUT : 결과를 대체하기 때문에 같은 데이터를 대체한다면 결과도 같다.
- DELETE : 결과를 삭제. 같은 요청을 여러번 해도 삭제된 결과는 같다.

*외부 요인으로 중간에 리소스가 변경되는 것 까지는 고려하지 않는다*

<br>

### 아니 그래서 멱등은 어디서 쓸 수 있는가?

- 자동 복구 메커니즘 : 몇번을 다시 실행해도 같은 결과가 나오기 때문에!!

  → 서버가 TIME OUT 등으로 정상 응답을 못 주었을 때, 클라이언트가 같은 요청을 다시 해도 되는가? 에 대한 판단 근거로 쓸 수 있다.

<br>

## 캐시 가능 (Cacheable Methods)

> 응답 결과 리소스를 캐시해서 사용해도 되는가?

GET, HEAD, POST, PATCH 는 캐시가 가능하다. 실제로는 GET, HEAD 정도만 캐시로 사용한다.

→ POST, PATCH 는 본문 내용까지 캐시 키로 고려해야 하는데, 구현이 쉽지 않다.

