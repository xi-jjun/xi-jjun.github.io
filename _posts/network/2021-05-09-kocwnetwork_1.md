---
layout: post
title: "네트워크 - 1"
author: "xi-jjun"
tags: Network
---

# 공부를 시작하며

비전공자로써 컴퓨터 네트워크에 대해 공부한 것들을 정리하기 위해 시작하였다. 

IP, netmask, LAN, WAN, Protocol, packet도 뭔지 모르는 심각한 상태로 공부를 시작했다.

### 	참고자료

​		KOCW 이석복 교수님 2015-2학기 강의, (갓)WIKIPEDIA

​		네트워크란? 출처 : https://hannut91.github.io/blogs/network/network



# 네트워크

### 	네트워크란? 

그 중에서도 내가 공부할 computer network 란 "net+work의 합성어로써..." 이런식으로 설명되어 있었다. 

공부를 처음 접해본 나에겐 와닿지 않는 말들이어서 계속 검색을 해본결과,

	"무언가와 무언가가 무언가에 연결되어 있는 것"

여기서 무언가와 무언가는 Node라고 부르고, 이는 Link를 통해 연결되어 있다. 

비유해서 설명하자면 "전화"는 "전화기"와 "전화기"가 "전선"으로 연결되어 있다. 여기서 전화기는 Node이고 전선은 link 라고 생각하면 어떤 느낌인지는 알 수 있었다. 

computer network 에서는 컴퓨터와 컴퓨터가 router에 의해 통신을 주고 받는다.



### 네트워크 구성

그렇다면 네트워크는 무엇으로 이뤄져 있을까? 아까 말한 컴퓨터와 Router??

![network-all](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/network-all.png?raw=True)

네트워크는 위 사진처럼 core(내부)에는 Router가, edge에는 Host(우리 컴퓨터들)이 있다.



#### Network edge : connection-oriented service

Network edge : Host(우리 desktop, laptop)들이 위치. wep application이 돌아가는 곳.

=> 우리의 컴퓨터나 서버가 있다고 생각하면 된다.



##### - Network edge 구성요소

Client : 본인(?)이 원할 때 link에 연결해서 서버로부터 정보를 가져오는 구성요소.

=> 그냥 "서버에 요청하는 컴퓨터(우리)" 라고 생각하니깐 받아드려졌다.

Server : client의 요청을 기다리는 구성요소. 

=> ex) 네x버 홈페이지에서 내가 "어떠한 행동(클릭 등등)"도 하지 않는다면 아무일도 안일어난다.



##### - Network edge : connection-oriented service

모든 기능적 메커니즘이 network edge에 있다. TCP가 connection-oriented service를 제공한다.



###### TCP(Transmission Control Protocol)

1) Reliable, in-order byte stream data transfer

Client가 Server에게 메세지를 보낼 때, 메세지를 "**보낸 순서대로**" + "**손실되지 않게**" 보낸다.

2) Flow control

Sender가 **receiver의 받는 속도에 맞춰서** 전송속도를 조절한다.

3) Congestion control

Sender와 receiver 사이 중간 **네트워크 상황에 맞춰** 조절하며 전송한다.



###### UDP(User Datagram Protocol)

1) Connectionless

2) Unreliable data transfer

3) No flow control

4) No congestion control



Q1) 신뢰성도 없고, 어떠한 control 기능도 제공 안하는 UDP는 왜 있는가?

A1) "굳이" reliable 할 필요가 없을 때 쓴다. Ex) 음성통화같은 경우, data가 몇개 유실 되어도 모르기 때문이다.



Q2) 둘의 차이점은?

A2) UDP가 TCP에 비해 빠르다. 하지만 data가 손실된다는 점에서 그 속도는 무의미하다. TCP는 UDP에 비해 **비용**이 더 많이 든다.



Q3) "TCP는 UDP에 비해 **비용**이 더 많이 든다" <- 비용이 정확하게 무엇인가?

A3) 컴퓨팅 리소스와 네트워킹 리소스이다.



Q4) 근데 Protocol이 무엇인가...?

A4) **통신 규약**. 컴퓨터나 원거리 통신 장비 사이에서 메시지를 주고 받는 양식과 규칙의 체계.(출처:위키피디아)

=> 비유를 하자면 소통을 하기위한 하나의 '약속' 이라고 할 수 있다. 말하는 방식? 정도. 두 사람이 서로 말이 안통하면 소통이 안된다. 따라서 두 장비 사이에 protocol이 맞지 않으면 통신이 안된다.

Ex1) 잘못된 규약

A: 안녕하세요.

B: 죽어ㅓㅓ!!

Ex2) 올바른 규약

A: 안녕하세요.

B: 네 안녕하세요. 반갑습니다.

A: 그럼 이제 우리 통신할까요?

B: 네.

<hr>

#### Network core

Router들의 집합. Data는 2가지 방식으로 network를 통해 전달이 된다.

Q5) router가 도대체 무엇인가??

A5) 네트워크 중심부에서 Host(우리)가 보내는 메세지를 받아서 목적지까지 전달해주는 역할.



##### - Circuit switching

출발지~목적지까지 data가 갈 길을 미리 정하고, 특정 사용자만이 사용할 수 있게 만들어 놓은 것.(예전 유선전화망)

![circuit1](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/circuit1.png?raw=True)

Router의 일부분을 사용자들에게 열어놓은 것이다.

*가정 : 각 사용자가 100 [Kbps/s] 의 속도로 data를 전송*

최대 10명의 사용자를 가질 수 있다.

1명의 사용자가 사용하더라도 100 [Kbps/s] 의 속도로 data를 주고 받을 수 있다.



##### - Packet switching

Router에 들어오는 packet의 정보(목적지)를 본 후, 정보에 알맞은 방향으로 전송하는 방법.(인터넷에서 사용하는 방식)

*가정 : 각 사용자가 100 [Kbps/s] 의 속도로 data를 전송*

최대 ??명 이라는 개념이 없다. **한번에** 10명이 active하지 않으면 된다.

Ex) 네x버 기사를 클릭하고 한참 본 뒤에 네x버 만화 클릭. 

=> data의 전송은 전체 시간에 비해 매우 적은 비율이다. **Packet switching이 인터넷에 더 적합한 방식인 이유**. 더욱 유동적인 사용이 가능하다.

단점 : 인원의 제약이 없지만 너무 많으면 문제가 생길 가능성이 높아진다.

=> 왜? 정해져 있는 bandwidth에서 사용자가 많아질 수록 한번에 active하는 사용자의 수가 증가할 확률이 크기 때문이다.



###### - Packet switching  : Delay

![delay1](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/delay1.png?raw=True)

1. Processing delay : Router 1에서 packet을 받으면 제일먼저 제대로 됐는지, 목적지가 어딘지 검사해야한다.

2. Queueing delay : outgoing edge에 보내야 하는데 queue에 뭐가 있으면 기다려야 한다.

3. Transmission delay : packet의 첫 bit가 전송되는 순간~마지막 bit가 전송되기 시작할 때 걸리는 시간이다.

   => packet의 길이가 짧고, link의 bandwidth가 클수록 짧아진다.

4. Propagation delay : 다음 router 2로 전송하는데 걸리는 시간이다.

   

Q6) Packet이 무엇인가?

A6) data, bit의 집합. data의 형식화된 블록. 보내고자 하는 메시지가 담긴 편지봉투. Network layer에서 data의 전송단위.



Q7) Queue에 packet으로 가득하면 어떻게 되냐?

A7) Packet loss가 일어난다. 그리고 그러한 loss가 일어날 때 TCP가 다시 packet을 재전송한다.