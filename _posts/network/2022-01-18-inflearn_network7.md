---
layout: post
title: "HTTP Client → Server 데이터 전송"
author: "xi-jjun"
tags: Network

---

# HTTP

Inflearn `김영한` 강사님의 [모든 개발자를 위한 HTTP 웹 기본 지식](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/) 을 듣고 정리하는 글입니다.

<br>

# Client → Server 데이터 전송

데이터 전달 방식은 크게 2가지가 있다.

1. query parameter 로 데이터 전송.
   - GET
   - 주로 정렬 필터(검색)에 사용
2. message body 를 통한 데이터 전송.
   - POST, PUT, PATCH
   - 회원 가입, 상품 주문, 정보 수정 등에 사용

<br>

## Client → Server data 전송 상황

1. static 데이터 조회
2. dynamic 데이터 조회
3. HTML Form 으로 데이터 전송
4. HTTP API 로 데이터 전송

<br>

## Static 데이터 조회

> 상황 : `/static` 이라는 리소스 경로에서 `star.jpg` 라는 정적 데이터를 조회하려고 하는 상황.

```http
GET /static/star.jpg HTTP/1.1
Host: localhost:8080

```

Client에서는 위와 같은 HTTP GET request message 를 만들어서 서버에 전송한다.

<br>

서버에서는 해당 request message 를 받은 다음, `/static/star.jpg` 를 찾아서 response message 를 다시 client 에게 보낸다.

```http
HTTP/1.1 200 OK
Content-Type: image/jpeg
Content-Length: 303249

lknl23k2nl3k42l3knl3glrkgn4.....(대충 별 모양 그림의 info)
```

위와 같은 response message를 client에게 보내게 되는 것이다.

<br>

### 정리 : 정적 데이터 조회

- 이미지, 정적 데이터를 얻을 때 쓴다
- 조회는 GET 사용
- query parameter 없이 리소스 경로로 단순하게 조회가 가능하다

<br>

## Dynamic 데이터 조회

> 상황 : 구글에 `hello` 라는 검색어를 입력하는 상황

```http
GET /search?q=hello&hl=ko HTTP/1.1
Host: www.google.com
```

Client 위와 같은 요청 메세지를 만들어서 구글 서버에 보낸다.

지금 보다시피 리소스 경로 뒤에 다른 무언가가 더 붙어있는 것을 볼 수 있다.

```http
/search?q=hello&hl=ko
```

이 부분에 대한 http1.1 spec을 한번 보도록 하자.

> request-line   = method SP `request-target` SP HTTP-version CRLF

이전의 포스팅에서도 다뤘지만, 요청 메세지이기에 `start-line` 은 `request-line`이 된다. 그리고 현재 위 리소스 경로에 대한 설명은 `request-target` 을 보면 알 수 있을 것이다. 한번 보도록 하자.

> The most common form of request-target is the origin-form.
>
> ```
> origin-form    = absolute-path [ "?" query ]
> ```

`request-target`에 대해 [http1.1 spec(rfc7230)](https://datatracker.ietf.org/doc/html/rfc7230#section-5.3.1)에서 찾은 결과이다. Query parameter를 보낼 때는 resource path뒤에 ?를 붙인 뒤 query parameter를 쓰면 된다고 한다.

> When making a request directly to an origin server, other than a CONNECT or server-wide OPTIONS request (as detailed below), a client MUST send only the absolute path and query components of the target URI as the request-target.
>
> (대충 해석)
>
> Origin server에 요청할 때 CONNECT, OPTIONS에 대한 요청을 제외하고 client는 **반드시** `request-target` 으로 target-URI의 절대경로와 query 구성요소만을 보내야 한다.

그리고 예시도 찾을 수 있었다.

>For example, a client wishing to retrieve a representation of the resource identified as
>
>```
>http://www.example.org/where?q=now
>```
>
>directly from the origin server would open (or reuse) a TCP connection to port 80 of the host "www.example.org" and send the lines:
>
>```http
>GET /where?q=now HTTP/1.1
>Host: www.example.org
>
>```

라고 한다... 이것을 우리에 상황에 알맞게 바꾼다면

```
http://www.google.com/search?q=hello&hl=ko
```

의 리소스를 검색하려는 상황이다. 따라서 origin server(우리는 google서버)에서 직접 "www.google.com"의 port 80(https는 생각 안하기로 하자) 에 대한 TCP 연결을 한 뒤에 아래에 해당하는 내용을 전송한다.

```http
GET /search?q=hello&hl=ko HTTP/1.1
Host: www.google.com

```

<br>

구글 서버에서는 해당 요청 메세지에서 query parameter를 기반으로 정렬 필터링해서 결과를 동적으로 생성한 뒤 response message로 Client에게 응답한다.

<br>

<br>

### 정리 : 동적 데이터 조회

- 정렬 조건, 검색, 게시판 목록 조회에서 주로 사용된다
- 조회는 GET을 사용한다
- GET은 query parameter를 사용하여 data를 전달한다

<br>

## HTML Form tag를 이용한 데이터 전송

![inflearn7_1](img/inflearn7_1.png?raw=True){: width="70%"}

```html
<form action="/save" method="post">
  <input type="text" name="username">
  <input type="text" name="age">
  <button type="submit">전송</button>
</form>
```

위와 같은 html form이 있다고 하자. ([사진출처:**모든 개발자를 위한 HTTP 웹 기본 지식**(김영한 강사님)](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/dashboard))

Client는 위와 같은 POST 요청으로 아래와 같은 request message를 생성한다. (localhost:8080에 요청 메세지를 보낸다고 가정)

```http
POST /save HTTP/1.1
Host: localhost:8080
Content-Type: application/x-www-form-urlencoded

username=kim&age=20
```

여기서 `application/x-www-form-urlencoded`는 `username=kim&age=20`와 같은 형식의 정보를 보낼 때 쓰인다고 한다.

<br>

### GET에서 query parameter로 보내는거랑 비슷한데...?

라는 생각이 들 수 있다. 그러면 드는 의문이 'HTML form tag에서 method="get"으로 하면 전송이 안되나...?' 라는 생각도 들 수 있다. 

일단 가능은 하다. 그러나 그렇게 하지 않는다고 한다. 왜냐하면 `GET Method`는 **정보를 조회**할 때만 사용해야 하기 때문이다. 따라서 **리소스의 변경이 발생하는 곳에 사용하면 안된다!!** 

만약 GET으로 전송하게 되면, 아래와 같이 정보가 전송된다고 한다.

```http
GET /save?username=kim&age=20 HTTP/1.1
Host: localhost:8080

```

<br>

### 정리 : HTML Form tag를 이용한 데이터 전송

- POST 전송 ex) 회원가입, 상품 주문, 정보 수정 등등

- 기본 Content-Type은 `application/x-www-form-urlencoded`이다.

  - `<form>`의 내용을 message body를 통해서 전송한다(key-value)

  - 전송 데이터를 url encoded 처리를 한다.

    Ex) `abc김` → `abc%EA%B9%80`

- HTML Form은 GET 요청도 가능하다. **그러나!! 절대 리소스의 변경이 발생하는 곳에서 쓰면 안된다!!**

  - GET으로 요청하면 query parameter형식으로 전송된다.

- Content-Type: mulipart/form-data

  - file과 같은 binary data를 전송할 때 쓴다.
  - 여러 종류의 format과 함께 전송이 가능하다.

*HTML Form전송은 GET, POST만 지원된다*

<br>

## HTTP API로 데이터 전송

Client에서 서버로 바로 데이터를 전송할 때 `HTTP API로 전송`이라고 한다. Client가 직접 다 만들어서 서버로 넘기면 된다. 아래 예시를 보자.

> 상황 : username이 kim, age가 21인 회원 정보를 서버에 등록하고 싶을 때

```http
POST /members HTTP/1.1
Content-Type: application/json

{
  "username": "kim",
  "age": 21
}
```

이전에 봤던 `HTML Form으로 데이터 전송`과 차이점이 있다면, `Host: HOST_ADDRESS`가 없다는 것이다. 

<br>

### 정리 : HTTP API로 데이터 전송

- 서버 to 서버 통신을 위해 사용한다. 서버끼리 통신을 할 때에는 HTML이 없으니깐...!
- App client에서도 해당 방식을 쓴다.
- Web client
  - HTML에서 Form전송 대신에 JS를 통한 통신에 사용한다.(AJAX)
- POST, PUT, PATCH 사용가능.
- GET은 조회할 때 쓰인다. 마찬가지로 query parameter로 데이터의 전달이 가능하다
- Content-Type: application/json 을 주로 사용한다.
  - Text, XML, JSON 등등을 쓸 수 있다.



