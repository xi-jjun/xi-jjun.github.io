---
layout: post
title: "네트워크 - 2"
author: "xi-jjun"
tags: Network

---

# KOCW 2강

KOCW 네트워크 강의(이석복 교수님)를 듣고 정리하여 쓴 글이다.

<br>

## 지난 주 복습

>Router에서 packet을 받아 알맞은 방향으로 보내준다.

>Packet은 묶음 단위로 이동한다.

이렇다는 것은 이해가 된다. 하지만 알맞은 방향은 과연 무엇인지, 어떤 단위로 묶여서 이동하는건지 의문이 든다.

<br>

## Network 7 Layers

Network layers : 각 layer에는 다양한 protocol들이 존재한다.

![network2_1](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/network2_1.png?raw=True){: width="50%" height="53%"}

<sub>본 강의에서는 Upper layers를 Application layer로 통틀어 봤다.</sub>

Layers protocol

* Application layer : HTTP, FTP
* Transport layer : TCP, UDP
* Network layer : IP
* Link layer : WiFi, Ethernet

<sub>Router에는 이런 layer가 전부 존재하지 않는다. Router에는 Network, Link, Physical layer만이 있다.</sub>

*오늘 강의에서 7계층에 대한 모든 것을 배우는 것이 아닌, Top-down approach 방식으로 배울 것임을 설명하셨다.*

<br>

## Network 통신

![network2_2](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/network2_2.png?raw=True){: width="95%" height="33%"}

Network 통신 : client와 server process 사이의 통신

그러한 process의 interface가 바로 socket의 역할이다.

> Client : web browser. (구글 크롬 브라우저를 생각하면 쉽다)
>
> Server : web server (네이버 서버라고 생각하면 쉽다)

### Client-Server Architecture

* Server
  1. 24시간 항상 동작해야함(Always on host)
  2. Permanent IP address가 있어야 한다.

우리가 쓰는 구글 크롬과 같은 인터넷 브라우저도 하나의 process이다. 

<br>

Client의 process의 요청(=browser의 요청)이 network의 7 layers를 지나서 router들을 거쳐 server의 7 layers로 간다. 그 끝에 server의 process로 가는 것이다.

그렇다면 연결을 해야할텐데 어떻게 하는 것일까?

1. 사전에 연결을 해야 요청이 가능하다.

2. 연결 또한 요청을 해야한다. 따라서 server socket의 주소를 알아야 한다.

   => socket의 주소를 indexing하는 것이 바로 IP address, Port # 이다.

   <br>

Q1) Port가 과연 무엇인가?

A1) 컴퓨터에는 수많은 process가 실행 중이다. 따라서 process 사이의 통신을 하려면 '어떤' process에 통신을 할 것인지 '특정'을 지을 수 있어야 하는데, 이 때 port 번호로 특정할 수 있는 것이다. 쉽게 말하자면, 어떤 process인지 알려주는 번호이다.

<br>

간단한 예시를 들어보자.

상황 : 네이버에 접속(일단 trasnport, application layer에서만 생각)

1. naver의 socket의 IP, Port번호를 브라우저에 입력한다.

   => 하지만 실제로 naver의 IP를 아는 사람은 거의 없다.

2. 우리가 아는 "www.naver.com"을 브라우저에 입력한다.

3. DNS가 "www.naver.com"을 보고 실제 naver의 IP와 Port번호로 바꿔준 다음 우리는 접속할 수 있는 것이다.

   => Port 번호는 모든 웹페이지가 80을 사용한다. 

   * Q2) 왜 80번 port를 사용하는가? 다르면 안되는가?

     A2) IP는 DNS가 해석을 해준다. 그러나 해당 naver 웹 페이지가 동작하는 process가 아니면 의미가 없다. 그리고 process를 특정하는 index가 port이기 때문에 port번호를 80으로 고정함으로써 웹페이지는 '80번 port'로만 접속할 수 있게한 것이다. Port가 다 다르면 헷갈리고 복잡해지기 때문에 통일한 것이다.

<br>

## Layers

Network layers는 하위 layer가 상위 layer에게 서비스를 제공한다.

* Data integrity : 보낸 데이터가 loss안되고 온전히 갔으면 하는 것.

  => Transport layer에서 제공하는 유일한 서비스(TCP)

* Timing : 내가 보낸 데이터가 10ms 이내에 도착했으면 하는 것.

* Throughput : 내가 보낸 데이터가 1Gbps정도 나왔으면 하는 것.

* Security : 내가 보낸 데이터가 안전했으면 하는 것.

<br>

## HTTP(Hyper Text Transfer Protocol)

링크가 있는 text를 전송하는 protocol. -> 단순하다

request와 response 2개의 메세지밖에 없다. TCP를 사용한다.

Application layer 밑에 transport layer에서 TCP connection을 먼저 생성해줘야 client, server process끼리의 request와 responserk 가능하다.

"STATELESS" : 기억을 안한다. -> server가 client의 이전 request에 대하여 정보를 유지하지 않는다.

<br>

HTTP가 TCP connection을 사용하는 방법에 따라 2가지로 나뉜다.

* Non-persistent HTTP : TCP connection 생성 후 request와 response가 끝나면 연결을 끊는다. 그리고 다시 연결하는 방식.

* Persistent HTTP : TCP connection이 생성되면 계속 유지하는 방식

![network2_3](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/network2_3.png?raw=True){: width="95%" height="30%"}

1. HTTP Client HTTP Server(`process`)에게 TCP connection 을 시작한다. 어디서? 네이버IP, port=80에서.
2. HTTP Server는 네이버IP, port=80에서 TCP connection을 기다린다. 'accept' connection이면 client에게 알려준다.
3. HTTP Client는 HTTP request 메세지를 TCP connection socket에 보낸다.
4. HTTP Server는 request를 받고, 그에 해당하는 요청을 response 메세지 형태로 socket에 보낸다.
5. (만약 뉴스기사 버튼을 클릭했다면)news.index라는 html 파일을 parsing하여 안에 포함된 이미지 파일을 받기위해 다시 4번 과정 수행...

<br>

Q3) parsing이 뭔가요?

A3) parsing : 어떤 페이지(문서, html 등)에서 원하는 데이터를 특정 패턴이나 순서로 추출하여 정보로 가공하는 것. 

<br>

## 2강 수업 후기

아직 2번째 강의지만 뭔가 네트워크에 대해서 배운다는 느낌을 받기 시작해서 공부할 방향이 잘 잡혔다는 느낌을 받았다.

아직은 많이 생소하지만 꾸준히 들어보겠다.
