---
layout: post
title: "HTTP Method"
author: "xi-jjun"
tags: Network

---

# HTTP

Inflearn `김영한` 강사님의 [모든 개발자를 위한 HTTP 웹 기본 지식](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/) 을 듣고 정리하는 글입니다.

<br>

# HTTP Method

- GET : 리소스 조회
- POST : 요청 데이터 처리. 주로 등록에 사용
- PUT : 리소스를 **대체**. 해당 리소스가 없으면 생성
- PATCH : 리소스의 **부분을 변경**
- DELETE : 리소스 삭제

<br>

## GET

- 리소스 조회

- 서버에 전달하고 싶은 데이터는 query를 통해 전달

  → 메세지 바디를 통해서 데이터 전달 가능. 그러나 지원하지 않는 곳이 많아서 권장하지 않는 방법

<br>

### Example

1. ```http
   GET /members/100 HTTP/1.1
   Host: localhost:8080
   ```

   라고 HTTP GET `request message` 를 서버에게 전달.

2. 서버에서는 `/members/100`에 해당하는 리소스를 생성

   ```json
   {
     "username": "Kim Jae Jun",
     "age": 26
   }
   ```

3. 서버에서 생성한 반환될 리소스를 `Response Message` 의 body 에 넣어서 Client에게 전달

   ```http
   GET /members/100 HTTP/1.1
   Content-Type: application/json
   Content-Length: 34
   
   {
     "username": "Kim Jae Jun",
     "age": 26
   }
   ```

<br>

## POST

- 요청 데이터 처리
- **메세지 바디를 통해 서버로 요청 데이터 전달**
- 서버는 요청 데이터를 처리
  - 메세지 바디를 통해 들어온 데이터를 처리하는 모든 기능을 수행
- 전달된 데이터로는 주로 신규 리소스 등록, 프로세스 처리에 사용한다

<br>

### Example

1. 회원으로 등록할 `username`, `age`의 정보를 http `request message`를 통해 전달

   ```http
   POST /members HTTP/1.1
   Content-Type: application.json
   
   {
     "username": "new Name",
     "age": 21
   }
   ```

   

2. 서버는 해당 데이터를 받고 등록할 때, 내부 로직에 따라 신규 리소스를 구별할 id라는 식별자를 생성.

   `/members` → `/members/100` 100이라는 신규 id 생성됨. 

3. `response message`에 해당 데이터를 담아서 client에게 전송.

<br>

## POST : 요청 데이터를 어떻게 처리하다는 뜻일까? 에 대한 예시

- spec : `POST method`는 대상 리소스가 고유 한 의미 체계에 따라 요청에 포함 된 표현을 처리하도록 요청한다.
- Example
  - HTML 양식에 입력 된 필드와 같은 데이터 블록을 데이터 처리 프로세스에 제공
  - 게시판, 뉴스 그룹, 블로그에 메시지 게시
  - 서버가 아직 식별하지 않은 새 리소스 생성
  - 기존 자원에 데이터 추가
- 정리 : 이 리소스 URI에 POST 요청이 오면 요청 데이터를 어떻게 처리할지 리소스마다 따로 정해야 함. → 정해진 것이 없음

<br>

## POST 정리

1. 새 리소스 생성(등록) : 서버가 아직 식별하지 않은 새 리소스 생성.

2. 요청 데이터 처리

   - 단순히 데이터를 생성하거나, 변경하는 것을 넘어서 프로세스를 처리해야 하는 경우

     Ex) `주문 → 결제완료 → 배달 시작 → 배달 완료` 처럼 단순히 값 변경을 넘어 프로세스의 상태가 변경되는 경우.

     *각각의 버튼을 누르는 경우에 있어 POST로 보내야 한다.*

   - 따라서 Post의 결과로 새로운 리소스가 생성되지 않을 수 있다.

     Ex) `POST /orders/{orderId}/start-delivery` (control URI)

     URI는 리소스 단위로 설계하면 좋지만 그렇지 못할 수도 있다. 그게 안될 때 위의 예시와 같이 동사의 URI가 나올 수 있다.

3. 다른 메서드로 처리하기 애매한 경우

   Ex) query string으로 말고 JSON으로 조회 데이터를 넘겨야 하는데, GET method를 사용하기 어려운 경우. GET은 body에 메세지 넣기 힘들기 때문에 POST를 쓰면 된다.

   - 그냥 애매하다? 거의 대부분 가능 → POST
   - 그러나 조회 데이터는 최대한 GET을 쓴다. → GET으로 오면 서버끼리는 '캐싱을 하겠다'라고 약속. 따라서 조회는 GET이 유리.
