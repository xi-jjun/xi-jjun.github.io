---
layout: post
title: "HTTP Message"
author: "xi-jjun"
tags: Network

---

# HTTP

Inflearn `김영한` 강사님의 [모든 개발자를 위한 HTTP 웹 기본 지식](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/) 을 듣고 정리하는 글입니다.

<br>

# HTTP 특징

- Client-Server 구조
- stateless, conectionless
- Http message 통해 통신
- 단순함, 확장가능

이번에는 Http Message 부터 다시 정리하도록 해보겠다.

<br>

<br>

# Http Message

![inflearn2_1](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/inflearn2_1.png?raw=True){: width="75%"}

<sub>[사진 출처](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/) - **모든 개발자를 위한 HTTP 웹 기본 지식**[김영한]</sub>

[rfc7230 http message spec](https://datatracker.ietf.org/doc/html/rfc7230#section-3)에서 위 그림에 해당하는 format에 대해 나와있다. 

<br>

## start-line

- `Start-line` = `request-line` / `status-line`
  - `request-line` = `method` SP `request-target` SP `HTTP-version` CRLF
    - `request-target` = `absolute-path[?query]`
    - SP : 공백
    - CRLF : 줄바꿈(엔터)
  - `status-line` = `HTTP-version` SP `status-code` SP `reason-phrase` CRLF
    - `statue-code` : 요청의 성공/실패를 나타냄.
    - `reason-phrase` : 사람이 이해할 수 있는 짧은 상태 코드 설명 글

<br>

### Request message

```http
GET /search?q=hello&hl=ko HTTP/1.1
Host: www.google.com

```

- `start-line` = GET /search?q=hello&hl=ko HTTP/1.1
  - `request-line` = GET /search?q=hello&hl=ko HTTP/1.1
    - `method` = GET(resource 조회 동작)
    - `request-target` = /search?q=hello&hl=ko
      - `absolute-path` = /search
      - `[?query]` = ?q=hello&hl=ko. 없어도 됨.
    - `HTTP-version` = HTTP/1.1

<br>

### Response message

```http
HTTP/1.1 200 OK
Content-Type: text/html;charset=UTF-8
Content-Length: 123

<html>
  <body> .... </body>
</html>
```

- `start-line` = HTTP/1.1 200 OK
  - `status-line` = HTTP/1.1 200 OK
    - `statue-code` : 200. 성공
    - `reason-phrase` : OK

<br>

## HTTP Header

- `header-field` = `field-name` ":" OWS `field-value` OWS
  - OWS : 띄어쓰기 허용

![inflearn2_2](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/inflearn2_2.png?raw=True){: width="75%"}

<sub>[사진 출처](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/) - **모든 개발자를 위한 HTTP 웹 기본 지식**[김영한]</sub>

- HTTP 전송에 필요한 모든 부가정보. 

  Ex) 메시지 바디의 내용, 메시지 바디의 크기, 압축, 인증, 요청 클라이언트(브라우저) 정보, 서버 애플리케이션 정보, 캐시 관리 정보...

- 표준헤더가 너무 많다. - [링크](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields)

- 필요시 임의의 header도 추가가 가능하다.

<br>

## HTTP Body

![inflearn2_3](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/inflearn2_3.png?raw=True){: width="75%"}

<sub>[사진 출처](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/) - **모든 개발자를 위한 HTTP 웹 기본 지식**[김영한]</sub>

- 실제 전송할 데이터
- HTML 문서, 이미지, 영상, JSON 등등 byte로 표현할 수 있는 모든 데이터 전송 가능

<br>

`HTTP`는 단순하다. message도 매우 단순하다. → 크게 성공하는 표준 기술은 단순하지만 확장 가능한 기술이다.

<br>

## 정리

- HTTP message에 모든 것을 전송
- HTTP/1.1 기준으로 학습
- Client - Server 구조
- Stateless protocol
- HTTP message
- 단순함. 확장 가능

<br>
