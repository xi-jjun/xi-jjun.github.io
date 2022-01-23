---
layout: post
title: "HTTP API 설계"
author: "xi-jjun"
tags: Network

---

# HTTP

Inflearn `김영한` 강사님의 [모든 개발자를 위한 HTTP 웹 기본 지식](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/) 을 듣고 정리하는 글입니다.

<br>

# HTTP API 를 만들어보자!

> 목표 : 회원 정보 관리 API를 만드는 것.

- 요구 사항
  - 회원 등록
  - 회원 조회
  - 회원 목록 조회
  - 회원 수정
  - 회원 삭제

<br>

위 5가지 사항을 API URI로 설계해보자.

- 회원 등록 - `/join-member`
- 회원 조회 - `/search-member-by-id`
- 회원 목록 조회 - `/search-members`
- ...

이름도 명확하게!! 어떤 동작인지도 명확하게!! 설계한 것 같아 보인다...

<br>

## BUT! URI는 **Resource를 기준으로 식별**해야 한다.

따라서 '회원을 조회하는 것' 은 resource가 아니다. **'회원'이 resource**이다. '조회'는 동작을 의미할 뿐이다.

- **회원** 등록 - `/members/{member_id}`
- **회원** 조회 - `/members/{member_id}`
- **회원** 목록 조회 - `/members`
- **회원** 수정 - `/members/{member_id}`
- **회원** 삭제 - `/members/{member_id}`

근데 뭔가 이상하다. 아까보다 더 단순해진 것 같은데.. **구분을 어떻게 하는거지...?** 라는 생각이 든다.

<br>

## 그러면 조회, 등록 과 같은 동작은 어떻게 표현할거냐?

> 우리가 배울 **HTTP Method**로 동작을 표현할 수 있다.

resource를 기준으로 식별해서 HTTP Method로 동작을 구부함으로써 HTTP API를 설계할 수 있다.

- **회원** 등록 - POST `/members/{member_id}`
- **회원** 조회 - GET `/members/{member_id}`
- **회원** 목록 조회 - GET `/members`
- **회원** 수정 - PUT, PATCH, POST `/members/{member_id}`
- **회원** 삭제 - DELETE `/members/{member_id}`

이러한 HTTP Method에 대해 다음 포스팅 부터 하나하나 알아보도록 하자.

<br>

## 정리 : 중요한 것

>가장 중요한 것은 **resource를 식별**하는 것!!

- URI는 resource만 식별하는 것이다!!
- 리소스와 해당 리소스를 대상으로 하는 행위를 분리
  - 리소스 : 회원 - (명사)
  - 행위 : 등록, 수정, 삭제, 조회 - (동사)
- 행위는 **HTTP Method**로 구분!!

