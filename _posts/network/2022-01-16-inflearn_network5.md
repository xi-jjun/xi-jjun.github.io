---
layout: post
title: "HTTP Method2"
author: "xi-jjun"
tags: Network

---

# HTTP

Inflearn `김영한` 강사님의 [모든 개발자를 위한 HTTP 웹 기본 지식](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/) 을 듣고 정리하는 글입니다.

<br>

# HTTP Method 2 - PUT, PATCH, DELETE

## PUT

- 리소스를 **완전 대체**

  - 리소스가 있으면 **대체**
  - 리소스 없으면 생성

  → 덮어쓰기랑 비슷

- **Client가 리소스를 식별**

  Client가 리소스 위치를 알고 URI를 지정한다. → `POST` 와의 큰 차이점

<br>

### Example

1. Client가 서버로 `PUT` 요청을 보낸다.

   ```http
   PUT /members/100 HTTP/1.1
   Content-Type: application/json
   
   {
     "username": "old name",
     "age": 40
   }
   ```

   - `/members/100` : Client가 리소스의 위치를 알고 URI를 지정했다. → `POST` 와의 큰 차이점

2. 서버에는 `{ "username": "young", "age": 10 }` 이라는 리소스가 존재했다고 하자. Client가 보낸 PUT요청에 의해 리소스가 대체된다.

   → 이제부터 `/members/100` 은 1번에서 Client가 요청한 데이터로 **대체된다**. (만약 원래 리소스가 없었다면 새로 생성한다)

<br>

## PATCH

- 리소스 **부분 변경**

데이터를 부분적으로 바꾸고 싶다면 `PATCH` 를 쓰면 된다. `PATCH` 가 지원이 안된다면, `POST` 를 쓰면 된다.

<br>

## DELETE

- 리소스 제거

리소스를 제거하고 싶다면 쓰면 된다.
