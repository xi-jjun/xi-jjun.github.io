---
layout: post
title: "네트워크 - 3"
author: "xi-jjun"
tags: Network

---

# KOCW 3강

KOCW 네트워크 강의(이석복 교수님)를 듣고 정리하여 쓴 글이다.

<br>

## 지난 주 복습

>Socket은 process가 데이터를 보내거나 받기 위한 창구 역할을 한다.

>client와 server는 request와 response의 반복으로 정보를 주고 받는다.

> DNS는 도메인 이름을 실제 해당 public IP로 변환해주는 역할을 한다.

<br>

## Socket

Client process와 Server process 사이의 통신. 둘 사이의 통신을 위한 API가 socket인 것이다.

Application layer에는 많은 process가 있다.

* process : 컴퓨터에서 연속적으로 실행되고 있는 컴퓨터 program. program의 코드와 정적인 데이터가 메인 메모리에 load되면 process가 되는 것이다.
* program : HDD에 저장되어 있는 실행코드. 실행되기만을 기다리는 코드와 정적인 데이터의 묶음.

<br>

### Socket이란 무엇일까?

> Socket : network 상에서 동작하는 프로그램 간 통신의 종착점(End point).

End point : IP주소와 Port번호의 조합. 최종목적지.

socket은 데이터를 통신할 수 있도록 해주는 연결부 역할이다. => IP주소와 port번호를 갖고 있는 Interface.

두 프로그램 모두에 socket이 생성돼야 통신이 가능하다.

<br>

### Client-Server socket 생성 & 연결

![network3_3](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/network3_3.png?raw=True){: width="60%" height="60%"}

![network3_4](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/network3_4.png?raw=True){: width="60%" height="35%"}

1. TCP server는 socket 생성 후 client에서 connect요청 올 때까지 기다린다.
2. client에서 socket생성 후 server에 connect요청. Three-way Handshake을 하여 두 컴퓨터 사이의 TCP연결을 성공적으로 진행할 수 있다.
3. 연결이된 이후, client가 request를 보내면 server에서는 request를 처리하여 reply해준다.
4. client가 socket통신을 끝낼 때 server도 socket을 닫는다.

<br>

### Multiplexing & Demultiplexing

Transport layer라면 기본적으로 제공해야 하는 기능이다.

* Multiplexing : Application layer에서 process의 메세지가 socket으로 나갈 때 전달하는 기능.
* Demultiplexing : 도착한 segment를 알맞은 socket에 전달하는 기능.

![network3_1](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/network3_1.png?raw=True){: width="75%" height="35%"}

(1) : 여러개의 process 메세지를 transport layer로 보낼 때 multiplexing.

Transport layer에서 데이터의 단위는 segment형태이다. 

(2) :  Sender의 모든 layer를 거치고, router를 지나 Receiver 측의 layer를 거쳐서 목적지(dest port)에 도달한다. Transport layer에 수많은 socket 중 알맞은 곳에 segment들을 보내기 위해 Demultiplexing을 한다.

* 여기서 알맞은 목적지를 아는 방법  : Segment의 HEADER영역을 보면, 어떤 socket으로 갈지 알 수 있다.

<br>

![network3_2](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/network3_2.png?raw=True){: width="70%" height="40%"}

<sub>Segment</sub>

* HEADER
  * Source Port # : 현재 데이터를 보내는 측의 Port 번호
  * Dest Port # : 데이터를 보낼 목적지 측의 Port 번호
* Data : process(application)의 메세지가 저장돼있다. Segment의 대부분을 차지한다.

HEADER의 정보를 가지고 receiver에서 Demultiplexing하는 것이다.

**Segment**가 network layer로 갈 때 network layer의 데이터 전송 단위인 **packet**의 Data영역으로 들어간다.

**Packet**은 다시 link layer의 데이터전송 단위인 **Frame**의 Data부분으로 들어가게 된다.

<br>

~~사실 여기서 IP address등 더 많은 정보가 있어야 하지만 하나씩 배워나가기로 했다.~~

<br>

## Connectionless Demux example : UDP

![network3_6](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/network3_6.png?raw=True){: width="85%" height="38%"}

P3에서 보낸 데이터와 P4에서 보낸 데이터가 같은 socket으로 간다.

그 이유는 Dest IP, Port번호 2개만을 사용해서 어떤 socket으로 올릴지 demultiplexing이 이뤄지기 때문이다.

따라서 source(보내는 측)의 IP, Port번호에는 상관없이 목적지의 IP, Port가 packet이 어디로 갈지 정한다.

> 해당 socket(P1)과 연결된 많은 client들이 있다. 이건 누구와도 연결되어 있는 상태가 아니므로 "Connectionless"라 한다.

아무나 다 올 수 있는 것은 연결이 아니다.

<br>

## Connection-Oriented Demux example : TCP

![network3_5](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/network3_5.png?raw=True){: width="85%" height="42%"}

P4에 P1~3이 접근한다. 각각 다 다른 source IP, source Port, dest IP, dest Port를 가졌기에 각각의 socket끼리는 유일한 연결을 가진다.

따라서 src-IP, src-Port, dest-IP, dest-Port 4가지로 어느 socket으로 전달할지 결정한다.

<br>

### 구체적 예시

네이버 웹 서버를 경우로 예시를 들자면, 하나의 web server에 각 사용자 thread가 각각의 socket을 담당한다.

이는 모두 다른 src-IP,src-Port를 가졌을 것이기 때문에(dest IP=naver, dest Port=80으로 동일) 각각의 연결은 모두 유일하다.

따라서 네이버는 접속하는 사용자들만을 위한 socket이 있는 것이다.

> TCP는 client를 위해 socket을 생성하고 관리하기 때문에 자원을 많이 소모한다.

<br>

## UDP(User Datagram Protocol)

![network3_7](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/network3_7.png?raw=True){: width="75%" height="45%"}

<sub>UDP : Segment format</sub>

* Source port, Dest port : 16bit. 어떤 socket을 통해 프로세스로 갈지 알려주는 지표이다. 0~2^16 - 1 개 만큼의 port를 할당할 수 있다. Port로 mux와 demux를 한다.
* Checksum : application data가 전송도중 error가 있었는지 판단해주는 field. 
  * Error가 없다면 demux해서 해당 socket으로 통과시킨다. 
  * Error가 있으면 drop.
* Length : HEADER를 포함한 UDP segment의 길이 정보가 있는 field.

UDP가 아무것도 안하는 것처럼 보이지만, Multiplexing과 Demultiplexing 해준다. 그리고 checksum field로 error검사도 해준다. 적어도 우리가 UDP로 받는 데이터에는 모두 error가 없다는 것을 뜻한다.

<br>

# 정리

> Socket은 process와 process사이의 통신을 위한 API이다.

> 어느 socket으로 가야하는지는 segment의 HEADER 부분을 보면 된다.

> 유일한 연결이 아닌, 누구라도 그 socket에 접근한다면 그것은 연결된 것이 아니다. -> Connectionless : UDP
>
> socket 과 socket 사이의 연결이 유일하다면 그게 바로 연결된 것. -> Connection-Oriented : TCP

> TCP는 Client를 위해 socket을 생성하고 관리하기 때문에 자원을 더 많이 소모.









