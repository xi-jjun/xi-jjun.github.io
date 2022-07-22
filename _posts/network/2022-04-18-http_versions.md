---
layout: post
title: "HTTP 0.9/1.0/1.1/2.0"
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

  이로써 아까 위에서 문제였던, request를 할 때마다 TCP 3-way handshake를 할 필요가 없어진 것이다.

- `Pipelining` 기법 도입

  이전까지는 1개의 요청을 보내고 응답이 올 때까지 기다린 후 다시 요청을 보냈다. 이렇게 1개씩 데이터를 보내는 방법은 비효율적이기에 

  > 하나의 connection에서 응답을 기다리지 않고 순차적으로 여러 요청을 보냄

  이렇게 하여 latency를 줄였다. Pipeline protocol에 대해서는 [이전 포스팅](https://xi-jjun.github.io/2021-04-17/kocwnetwork_5) 에 자세히 나와있다.

  근데 이렇게 해서 2가지 문제가 발생했다.

  1. Head Of Line Blocking

     먼저 온 packet에 대한 요청을 처리하는데 걸리는 시간만큼 그 다음 요청들도 기다리게 되는 현상. 

     - 왜 굳이 기다리지? 먼저 처리 가능한 것들을 우선으로 처리하면 되는거 아닌가? `RFC2616` docs를 보면,

       >A server MUST send its responses to those requests in the same order that the requests were received.

       server는 **반드시 요청순서에 따라 같은 순서로 응답을 해야한다** 라고 명시되어 있다.

  2. HEADER의 불필요한 중복

     '연속된 요청'에서 잘 생각해보면, 그 연속된 데이터들은 같은 웹 페이지에 대한 정보를 같고 있을 것이다. 따라서 HEADER값에도 큰 차이가 없을 것이다. 근데 우리는 매번 HEADER를 다시 만들어서 보내기에 'HEADER의 불필요한 중복된 데이터가 발생'하게 되는 문제가 발생한다.

<br>

## HTTP/2.0

기본 `HTTP/1.X`버전에 대한 '성능 향상'에 초점을 둔 protocol.

- message 전송 방식이 변함 → `Binary Framing layer`를 사용하여 parsing, 전송 속도를 높였고, 오류발생 가능성을 낮췄다.

  - `frame` : frame이라는 단위로 HTTP message를 분할하였다

    - `HEADERS frame`
    - `DATA frame`

    이렇게 분할한 다음에 binary로 encoding하였다. 따라서 컴퓨터가 이해하기 좋았기에 parsing과 전송속도가 올라갔고, 오류발생 가능성이 적어졌다. 

  - `Multiplexed Streams` 

    > Connection 1개로 동시에 여러개의 메세지를 주고 받을 수 있으며 응답은 순서에 상관없이 Stream으로 주고 받는다. 

  따라서 `frame`단위의 데이터를 `Multiplexed Stream`을 통해 각각 데이터를 받게 하여 `Head Of Blocking` 문제를 해결하였다.

- `Stream Prioritization` : resource간 전송 우선 순위를 설정 가능하다.

- `Server Push` : Server는 Client가 요청하지 않은 resource를 미리 push를 통해 전송할 수 있다. 

  - 이런 기능이 있는 이유 : client가 server에 'index.html' page를 요청했다고 하자. 근데 'index.html'에는 이미지 파일과 추가적인 css 필수적이다. 왜? 웹 페이지를 볼 때 이미지의 위치가 어디인지 알아야 랜더링을 올바르게 할 수 있기 때문이다. 따라서 '어차피 가져와야할 데이터'이기에 이러한 기능이 추가된 것.

- `Header Compression` : header 정보를 압축하기 위해 `Header table`과 `Huffman Encoding` 기법을 사용하여 처리. `HTTP/1.1`의 중복되는 HEADER의 데이터를 해결했다. 크기를 줄여서 page load시간을 감소시켰다.



<br>

## Reference

[http 0.9/1.x/2.0/3.0](https://www.youtube.com/watch?v=xcrjamphIp4)

[public-key vs symmetric-key](https://www.youtube.com/watch?v=H6lpFRpyl14)

[rfc2616 - 8.1 pipelining](https://www.ietf.org/rfc/rfc2616.txt)

[Head Of Line Blocking](https://withbundo.blogspot.com/2021/02/httphol-head-of-line-blocking.html)

[Head Of Line Blocking in HTTP vs in TCP](https://seokbeomkim.github.io/posts/http1-http2/)

