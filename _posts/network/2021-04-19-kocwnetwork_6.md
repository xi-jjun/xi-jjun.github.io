---
layout: post
title: "네트워크 - 6"
author: "xi-jjun"
tags: Network

---

# KOCW 6강

KOCW 네트워크 강의(이석복 교수님)를 듣고 정리하여 쓴 글이다.

<br>

## 5강 복습

TransPort Layer에서 중요한 기능인 **Reliable Data Transfer**의 원리와 RDT를 구현하기 위해 필요한 메커니즘을 배웠다. 그리고 실제로 RDT network를 사용하기 위해서는 pipeline 방식이 전송이 필요하다는 것도 알게되었다.

Pipeline 방식으로 동작하기 위해 어떤 방식으로 생각해야 할지에 대한 2가지 approach를 했었다. (Go-Back-N, Selective Repeat)

<br>

# 6강 시작

## TCP(Transmission Control Protocol)

- Point-to-point 통신. 하나의 sender에 여러개의 receiver는 불가능하다. 한 쌍의 process 사이의 통신만 다룬다.

  → 더 엄밀히 말하면 **한 쌍의 socket 사이의 통신**만 다룬다. (process 하나에 여러개의 socket을 열 수 있다)

- Reliable in-order byte : loss없이 byte단위로 순서대로 전송된다.

- pipelined : window size 만큼 한번에 보낸다.

- Full duplex data : 데어터가 양방향으로 진행한다.

  이때까지 우리는 Sender/Receiver로 나눠서 생각했다. 그러나 실제로는 그러한 구분이 없다. HTTP protocol만 생각해봐도 client가 `request`를 Server에 보내고, server는 받은 요청을 HTML이라는 형식으로 `response`하여 client에게 준다. 여기서 client, server 둘다 데이터를 일방적으로 보내거나 받기만 한 것이 아니라 데이터를 주고 받았다. → sender인 동시에 receiver라는 것.

  ![network6_1](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/network6_1.png?raw=True){: width="85%" height="40%"}

  따라서 위 그림과 같이 그려야 정확하다. `Receiver receiving buffer`가 있는 이유는 buffer에 도착한 데이터를 저장해서 loss된 데이터만 받는 `Selective Repeat`를 pipelining방식의 RDT에서 쓰기 때문이다.

- Connection-Oriented : 실제로 데이터를 주고 받으려면 `Connection`이라는 것을 맺어야 하는데 맺기위한 방법으로 `Handshaking`이 있다. 나중에 자세히 배울 예정이다.

- **Flow controlled** : sender는 절대로 receiver가 현재 받을 수 있는 능력 그 이상으로 보내지 않는다.

- **Congestion controlled** : sender는 network의 상황을 고려하여 전송한다.

<br>

## TCP Segment Structure

![network6_2](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/network6_2.png?raw=True){: width="85%" height="45%"}

Application의 message가 **socket이라는 interface를 통해** Transport layer로 내려가서 TCP의 전송단위인 Segment에 들어간다. 그림에서는 message의 HEADER가 없는 것 같지만 실제로 message도 HEADER가 있다. 이번 단원은 Transport Layer에 관해 다루는 것이라서 무시하고 넘어간 것.

<br>

![network6_3](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/network6_3.png?raw=True){: width="85%" height="50%"}

- `Source Port #, Dest Port #` : 16bit. 따라서 port번호의 범위는 0 ~ 2<sup>16</sup>-1 이다. 
- `Sequence number` : TCP에서는 data의 시작 byte번호이다.
- `Acknowledgement number` : TCP의 `ACK #`은 **"(#-1)번까지 모두 잘 받았으니 #번째 segment부터 보내줘"**.
- `Receive window` : receiver buffer의 빈 공간을 feedback.
- `checksum` : error detection. 해당 segment가 network을 거쳐 오면서 error가 있는지 확인해주는 bit.

<br>

## TCP Seq #'s and ACKs

### * 다시!! 정리하고 들어갈 내용 - ACK 의미

- 4강에서 얘기했던 ACK # : 나 packet #번을 잘 받았어!!
- 5강에서 얘기했던 Go-Back-N의 ACK # : 나 #번 packet까지 잘 받았어!!
- 5강에서 얘기했던 Selective Repeat의 ACK # : 나 packet #번을 잘 받았어!!
- **이번 강의에서 얘기할 TCP의 ACK # : (#-1)번까지 모두 잘 받았으니 #번째 segment부터 보내줘** ← cumulative ACK

<br>

![network6_4](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/network6_4.png?raw=True){: width="60%" height="80%"}

- `#1` : `Host A`가 "C"라는 데이터를 Host B에게 보냈다. "C"는 1개 byte단위이다. 그리고 데이터 "C"의 시작 번호는 "42"라고 하자. 이전에 Host B로 부터 받은 데이터의 마지막 번호는 "78"이라고 하자.

  ![network6_5](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/network6_5.png?raw=True){: width="80%" height="40%"}

  위 그림이 현재 상황이라고 할 수 있다. 분석을 해보자면, 

  - Seq 42 : "C"라는 데이터의 시작번호는 42번이기에 Seq #가 "42"로 설정된 것이다.
  - ACK 79 : Host B로부터 데이터를 받았을 때 마지막으로 받은 데이터의 끝번호가 78번이었다. 따라서 `Host A`는 Host B에게 "78번까지 잘 받았으니 79번부터 주세요!"라고 말하는 것.
  - data "C" : 1개 byte짜리 데이터를 전송한 것. "C"라는 문자 1개를 전달하려는 목적. 

  <br>

- `#2` : `Host B`는 Host A로부터 segment를 받았고, 다시 "C"라는 데이터를 보내려고 한다.

  ![network6_6](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/network6_6.png?raw=True){: width="80%" height="43%"}

  - Seq 79 : 아까가지 A에게 78번까지 잘 받았다는 ACK신호 받았기에 그 다음 데이터 번호부터 전송하려고 하는 중.
  - ACK 43 : Host A로부터 받은 데이터는 "C" 1개이다. 따라서 42번까지의 데이터를 잘 받았기에 43번 데이터 부터 주면 된다고 하는 것.
  - data "C" : 현재 강의에서는 `Host B`를 echo만 하는 서버라고 가정했다. 따라서 데이터는 그냥 Host A가 주는거랑 같은 데이터를 전송하게 되는 것이다.

  <br>

- `#3` : Host B로부터 데이터를 받았기에 ACK신호를 보내는 것.

<br>

## TCP에서 쓰는 Seq # #

![network6_7](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/network6_7.png?raw=True){: width="80%" height="35%"}

위 그림과 같이 Seq #을 설정해주는 것이다.

<br>

## Timeout - function of RTT(Round Trip Time)

segment가 중간에 Loss되면 RDT에서는 timer를 이용해서 timeout이 되면 탐지를 할 수 있다. 근데 과연 timeout의 기준은 어떻게 할까??

- timeout 작게 한다면, recovery가 빠른 대신 network에 overload를 준다.
- timeout 크게 한다면, recovery가 느리지만 network에는 부담을 덜 준다.

5강에서는 timeout 시간의 관계를 위와 같이 설명했었다. 과연 어떤 값이 가장 효율적일까?

<br>

### timeout을 RTT로 한다면?

> RTT : segment의 왕복 시간.

그러나 RTT는 network 상황(queueing delay 등등), 모든 segment가 같은 경로를 지나가는 것이 아니라는 것 등등 다 달랐다. 밑에 그림의 파란색 그래프가 RTT의 큰 편차를 보여준다.

<br>

### timeout을 Estimated RTT로 한다면?

$$
EstimatedRTT = (1-\alpha)*EstimatedRTT + \alpha*sampleRTT
$$

일반적으로 `a=0.125` 로 하여 편차가 큰 현재 측정값(RTT)을 덜 심각하게 반영하려고 했었다. 따라서 편차는 줄어들었다. 근데 너무 촉박하다는 느낌이 든다. 따라서 여유시간이 필요해 보였다.

아래 그림은 sample RTT와 timeout을 Estimated RTT로 했을 때의 그래프이다. 편차가 큰 RTT에 대해서는 무조건 timeout이 일어날 것이다.

![network6_8](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/network6_8.png?raw=True){: width="80%" height="50%"}

<br>

### timeout 최종 결정

timeout이라는 것은 loss가 확실할 때 일어나야 하는게 아닐까? 하는 생각이 적용됐다.
$$
DevRTT = (1-\beta)*DevRTT + \beta*|SampleRTT - EstimatedRTT|
$$
Estimated RTT에 safety margin을 추가하고 싶었다. 그 safety margin이 바로 `DevRTT`. 따라서 최종 timeout은
$$
TimeoutInterval=EstimatedRTT+4*DevRTT
$$
가 된다.

<br>

## TCP Reliable Data Transfer

- TCP는 single transmission timer 쓴다. → timer가 1개.
- Pipeline 방식.
- Cumulative ACKs.
- ACK는 마지막거만 주면 된다.

<br>

## Fast retransmit

TCP에서는 ACK를 받은 후 동일한 ACK가 3개 이상 받게되면 timeout으로 하라고 **권고**한다. 이렇게 하면 timeout이 일어나기 전에 빠르게 재전송을 할 수 있다.
