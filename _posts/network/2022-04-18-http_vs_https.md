---
layout: post
title: "HTTP vs HTTPS"
author: "xi-jjun"
tags: Network

---

# HTTP vs HTTPS

> `HTTP(HyperText Transfer Protocol)` : hypermedia 환경에서 빠르고 간편하게 데이터를 전송하는 protocol.

80 port를 사용하도록 정의된다. 그렇기에 HTTP server는 80 port를 열고 대기한다. HTTP 명령에 해당하는 HTTP method를 이용해 client가 서버에 요청을 전송, 서버는 client에게 요청에 대한 response한다. 

[이전 포스팅](https://xi-jjun.github.io/2022-01-07/inflearn_network1)들을 읽으면 이해가 더 잘 될것이다.

<br>

## Stateless protocol

Client - Server 간의 요청과 응답이 전송되는 과정은, TCP연결이 설정되면서 진행된다. 그리고 할 일이 다 끝나게 되면 연결을 끊게 된다. 따라서 `stateless` protocol로 분류되는데 예전의 단순한 html file만을 요청하고 받아오는 것이라면 문제가 없겠지만, 웹 문서 하나에 이미지나 다양한 데이터 파일이 연관되면 각각에 대한 요청을 보내기 위해 또 TCP연결을 얻고, 끊고를 반복해야만 한다. 따라서 이러한 경우에는 성능이슈가 발생한다.

<br>

## HTTP/0.9

최초의 HTTP이다. 처음에는 1개의 method `GET`만 지원했다.

```http
GET /example.html
```

요청 message.

```http
<HTML>
example.html file body
</HTML>
```

응답 message.

TCP를 사용했다

<br>

## HTTP/1.0

```http
GET /example.html HTTP/1.0
User-Agent: NCSA_Mosaic/2.0
```

요청 message. 0.9에 비해 버전도 명시하게 됐고, 가장 큰 변화는 'HEADER'부분이 생겼다는 것.

```html
200 OK
Date: Tue...
Content-Type: text/html

<HTML>
  ...
</HTML>
```

응답 message에서도 `200 OK`와 같은 status를 알 수 있게 해줬고, `Content-Type`을 명시할 수 있게 하여 다양한 type의 file도 전송할 수 있게 됐다. 이 때에는 connection 1개당 1번의 request, response가 각각 있었고, 끝나면 연결이 끊겼다. 따라서 web page에 이미지와 같은 다른 file도 있다면, 매번 resource를 요청할 때 마다 TCP connection을 얻어야했다.

<br>

## HTTP/1.1

- `Persistence Connection` 이라는 개념이 도입됐다.

  > 지정한 time out 동안 TCP connection을 닫지 않는 방식

  이로써 아까 위에서 문제였던, 매 resource를 요청하기 위해 TCP 3-way handshake를 할 필요가 없어진 것이다.

- `Pipelining` 기법 도입

  





## Reference
