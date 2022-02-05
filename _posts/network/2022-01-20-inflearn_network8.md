---
layout: post
title: "HTTP API 설계"
author: "xi-jjun"
tags: Network

---

# HTTP

Inflearn `김영한` 강사님의 [모든 개발자를 위한 HTTP 웹 기본 지식](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/) 을 듣고 정리하는 글입니다.

<br>

# HTTP API 설계 - 회원 관리 API

강의에서 3가지 방식으로 만들었다.

- HTTP API - Collection

  - POST 기반 등록

    Ex) 회원 관리 API

- HTTP API - Store

  - PUT 기반 등록

    Ex) 정적 컨텐츠 관리, 원격 파일 관리

- HTTP Form 사용

  - GET, POST만 지원된다

    Ex) 웹 페이지 회원 관리

<br>

# HTTP API - Collection

POST기반 등록의 회원관리 시스템 API를 설계해볼 것이다.

- 회원 목록 `/members` → GET
- 회원 등록 `/members` → **POST**
- 회원 단일 조회 `/members/{memberId}` → GET
- 회원 정보 수정 `/members/{memberId}` → PATCH, PUT, POST
- 회원 삭제 `/members/{memberId}` → DELETE

<br>

## POST - 신규 자원 등록 특징

- Client는 **등록된 리소스의 URI를 모른다**

  - 회원 등록 : `/members` - POST, "username : kim, age : 21"이라는 데이터를 가진 회원을 등록한다고 가정하자. 해당 요청을 서버에 보냈다.

    → 서버는 `100번`으로 신규 리소스 식별자를 생성한다고 가정하자. 서버가 만든 response message는 아래와 같다.

    ```http
    HTTP/1.1 201 Created
    Content-Type: application/json
    Content-Length: 34
    Location: /members/100
    
    {
      "username": "kim",
      "age": 21
    }
    ```

    보다시피 `Location: /members/100` 이라는 것을 볼 수 있다. 분명히 Client에서 보낼 때에는 `100`이라는 리소스 식별자가 없었다. 

    → **서버에서 리소스 URI를 결정하고 만들어 준 것이다.** 서버가 알아서 만들었으니깐 Client는 등록된 리소스의 URI를 모르는 것이다.

<br>

## Collection

- 서버가 관리하는 리소스 directory
- 서버가 리소스의 URI를 생성하고 관리
- 위 예시에서 `/members`가 collection에 해당한다

<br>

# HTTP API - Store

PUT기반 등록의 File 관리 System API를 설계해볼 것이다.

- file 목록 `/files` - GET
- file 조회 `/files/{fileName}` - GET
- file 등록 `/files/{fileName}` - **PUT**
- file 삭제 `/files/{fileName}` - DELETE
- file 대량 등록 `/files` - POST

<br>

## PUT - 신규 자원 등록 특징

- Client가 **리소스 URI를 알고 있어야 한다**

  - 파일 등록 : `/file/star.jpg` - PUT, 현재 "star.jpg"라는 파일을 서버에 등록하려는 상황.

    → Client가 직접 리소스의 URI를 지정해서 서버에 보낸다. (현재 star.jpg라는 리소스 URI를 지정했다.)

<br>

## Store

- Client가 관리하는 리소스 저장소
- Client가 리소스의 URI를 알고 관리
- 여기서 Store는 `/files` 이다

<br>

## POST 등록 vs PUT 등록

**Client가 리소스의 URI를 알고 있으냐 아니냐의 차이점이 있다**

<br>

# HTTP API - HTML Form

여기서 우리는 AJAX같은 기술을 쓰는게 아닌 **순수 HTML, HTML form tag**로만 회원관리 시스템 API를 설계해볼 것이다.

- 회원 목록 `/members` - GET

- 회원 등록 Form `/members/new` - GET

- 회원 등록 `/members/new` - POST

- 회원 수정 Form `/members/{id}/edit` - GET

- 회원 수정 `/members/{id}/edit` - POST

- 회원 조회 `/members/{id}` - GET

- 회원 삭제 `/members/{id}/delete` - POST

  DELETE와 같은 HTTP method를 못 쓰는 제약이 있기에 이와 같이 처리한다. 이렇게 처리하는 방법을 `Control URI`라고 한다.

<br>

## Control URI

- HTML Form이 GET, POST만 지원하기에 제약이 있다.

  → 제약을 해결하기 위해 **동사로 된 리소스 경로**를 사용한다.

- POST의 `/new`, `/edit`, `/delete`가 control URI이다.

- HTTP method로 해결하기 애매한 경우 사용한다. (HTTP API도 포함해서)

<br>

<br>

## 정리

- HTTP API - Collection

  - POST 기반 등록
  - **서버가 리소스 URI 결정**

- HTTP API - Store

  - PUT 기반 등록
  - **Client가 리소스 URI 결정**

- HTTP Form 사용

  - GET, POST만 지원된다

    → control URI를 사용하여 제약을 극복

<br>

## Control URI를 사용할 때

실무에서는 깔끔하게 HTTP method만으로 설계를 못하는 경우가 많다고 한다. 따라서 Control URI를 많이 사용하게 된다고 하는데 그 예시를 적어보겠다.

- 회원의 주문의 상태를 다음으로 진행 ← 보기만 해도 애매하다.

  Ex) 주문을 하여 '배달 중'이라는 상태로 만들어 보자.

  1. 회원1이 주문을 하기 위해 주문 Form을 요청. 주문번호는 서버가 1324번으로 정해줬다고 가정. `/orders/new` - GET
  2. 주문 Form에 필요한 정보를 적고 주문 버튼을 클릭. `/orders/new` - POST
  3. 해당 주문을 받은 업체에서 택배기사님에게 물품을 전달했음을 알림. 따라서 주문상태는 '배달 중'이 되어야 한다. `/orders/1324/delivery` - POST

저렇게 써야한다!! 는 아니지만 강의를 듣고 저런 느낌으로 쓸 수 있다고 생각했다.

Control URI는 필수적이지만 처음부터 쓰는 것은 안좋다고 한다. **최대한 리소스로 식별 한 다음** 더 이상 해결이 안되는 상황에서 써야 한다 말씀하셨다.

