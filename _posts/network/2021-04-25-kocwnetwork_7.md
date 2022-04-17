---
layout: post
title: "TCP - Flow control, 3-way handshake"
author: "xi-jjun"
tags: Network

---

# KOCW 7강

KOCW 네트워크 강의(이석복 교수님)를 듣고 정리하여 쓴 글이다.

<br>

# Flow Control

TCP는 reliable data transfer를 보장해준다고 계속 말했었다. 이를 보장하기위한 기능이 바로 `Flow control`이다. 간단히 말해서

> data 보내는 속도를 조절해주는 것.

왜?? 그냥 때려박고 loss되면 재전송 하면 되는거 아닌가?...라는 생각 전에 상식적으로 생각해야한다.

> 만약 `receiver buffer`에 더 이상 받을 수 있는 빈 공간이 없다면??

빈 공간 이상의 데이터 크기를 전송 하는 것은 overflow다. 따라서 **'받을 수 있는 만큼만 주는 것'**이 필요하고 이 때문에 `Flow control`이 있는 것이다. TCP segment HEADER에 `receive buffer`의 남은 공간에 대한 정보를 보냄으로써 구현된다. 기능은 이게 끝이다. 매우 중요한 기능이지만 단순하다는 특징이 있다.

![network7_1](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/network7_1.png?raw=True){: width="70%"}

<sub>[사진 출처](https://electronicspost.com/tcp-segment-format-with-diagram/)</sub>

`receive window` field에 해당 정보가 있다.

<br>

## 보내는 데이터 양 vs 보내는 속도

`Flow control`을 해준다고 했다. 그렇다면 둘 중 무엇을 조절해줘야 하는 것일까? 사실 둘은 관련이 있기에 뭐 하나를 고를 수가 없다.

> `transfer velocity` = `bit` / `sec`

이기 때문에 보내는 속도를 조절한다는 뜻은 단위 시간당 데이터의 양을 조절하겠다는 뜻과 같아지는 것이다.

<br>

이대로 끝나면 아쉬우니깐 **'만약 `recevier buffer`에 빈 공간이 0이라고 보냈을 때 sender가 취해야할 바람직한 자세는?'**에 관해 생각해보자.

<br>

## If sender nothing to do

만약 sender가 기다리기만 하는 상황을 생각해보자. A, B가 서로 통신 중이라고 한다면,

1. B의 receive buffer가 full됨. 따라서 A에게 B의 receive buffer가 가득찼다는 사실을 알려준다.
2. A가 B의 segment를 받음. 공간이 없음을 알게되고, **A는 기다림(If sender nothing to do)**
3. B의 receive buffer가 application layer로 data보냄. 따라서 공간이 생겼다!!
4. 하지만 A는 B가 공간이 생긴 것을 몰라서 가만히 있는다... B도 A가 아무런 요청을 안보내기에 가만히 있는다.

영원히 가만히 있으면 안되기에 

>**'누군가는 B의 receive buffer에 빈 공간이 있음을 알려줘야 한다!!'** 

사실 위 2번째 단계에서 A는 가만히 있는게 아니라 DATA영역이 텅 빈 의미없는 segment를 주기적으로 B에게 보내서 빈 공간에 대한 정보를 계속 추적해줘야 하는 것이다.

<br>

# TCP 3-Way Handshake

[지난 강의](https://xi-jjun.github.io/2021-04-19/kocwnetwork_6)에서 우리는 sender와 receiver사이에 buffer가 있음을 배웠다. 근데 생각해보면 너무 뜬금없지 않는가? 갑자기 buffer는 어디서 튀어나왔으며, buffer는 계속 존재하는 것인지 의문이 들었다.

결국엔 위와 같이 TCP 통신을 하기 위해서는 `buffer` 구조체, `seq #` 과 같은 resource들이 필요하다. 그리고 이러한 resource를 만드는 준비작업이 바로 connection management중에서 `TCP 3-Way Handshake`이다.

![network7_2](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/network7_2.png?raw=True){: width="80%"}

1. client가 server에게 conntection을 갖기 위한 의도를 전달하기 위해 `SYN` bit를 1로 보낸다. 이 때 DATA는 없다. x는 client에서 정한 초기 `seq number`이다. `receive window` size나 `Maximun segment` size에 관한 정보도 있다.

   - `SYN flag` : 1로 설정됨.

   - `ACK flag` : 0으로 설정됨.

2. server가 init `seq #` 고르고, `SYN`에 대한 SYNACK를 client에게 전송.

   - `ACK #` : client의 seq number를 `x`번까지 잘 받았으니, 그 다음인 `x+1`번 부터 주세요. 라는 뜻. random으로 설정된다.

     - 왜?? 그냥 0부터 주면 안되는건가?? - [답변 링크](https://www.quora.com/Why-in-a-TCP-sequence-is-a-number-taken-as-a-random-number-and-what-is-the-actual-number-at-the-start?awc=15748_1574583388_8da30461914e738de7495e39d9ab34b1&uiv=6&txtv=8&source=awin&medium=ad&campaign=uad_mkt_en_acq_us_awin&set=awin&pub_id=85386)

       > The Initial Sequence Number (ISN) used in TCP/IP sessions should be as **random as possible** in order to prevent attacks such as IP address spoofing and session hijacking.

       sequence number는 **'데이터의 순서를 보장'**해주는 역할을 하기에 예측이 가능하게 된다면 보안에 위험이 생길 수 있다고 한다.

   - `ACK flag` : server에서 1로 설정. ACK bit. packet이 수신 되었는지 확인하는데 사용하고, 시작 요청을 확인하거나 요청을 해제할 때도 사용.

   - `SYN flag` : client와 연결을 원한다면 1로 설정된다.

   client에서 server로 연결이 생성된다.

3. server로 부터 `SYNACK(x)`이 왔기에 server에 정상적으로 client의 packet/segment가 도착했음을 알게됨. → server is alive라는 뜻.

   따라서 client는 SYNACK에 대한 ACK를 server에게 전송한다. 이 때 부터 DATA도 전송할 수 있다.

   - `ACK flag` : client에서 1로 설정.
   - `SYN flag` : 0으로 유지시킨다.

   - `ACK #` : server가 `y`번까지 잘 보냈으니, `y + 1`번째 부터 보내달라는 뜻.
   - 이 때에 아까 말한 `buffer` 구조체 등등이 생긴다.

   server에서 client로도 연결이 생성되고 실질적인 데이터를 서로 주고 받게 된다.

나중에 연결을 끊을 때 생성했던 resource들을(buffer, bandwidth)를 free시켜줘야 한다.

<br>

## 굳이 이렇게 복잡하게 3번이나 handshake하는 이유는...?

2번만 한다고 가정해보자. client, server입장에서 생각해보면, 과연 나의 ACK가 상대방에게 '잘 전달됐는지' 알 수 있을까??

 - 시나리오 (2-way handshake라고 가정)

   1. client가 server로 SYN 보냄
   2. server가 client에세 SYNACK 보냄. 동작 끝

   - 이 때 server 입장 : client는 내가 보낸 SYNACK를 잘 받았겠지...? 연결 잘 된건가?

     → client는 server에 ACK보내고, ACK #까지 확인했다. 그러나 server는 본인이 보낸 ACK #에 대해서는 응답을 못받았기에 연결이 잘 됐는지 확신할 수가 없는 것.

<br>

## 4-Way Handshake : closing TCP connection

![network7_3](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/network7_3.png?raw=True){: width="65%"}

> `CLOSE` : "I have no more data to send." 이라는 뜻의 operation이라고 한다. - [출처 : RFC973](https://datatracker.ietf.org/doc/html/rfc793#section-3.5)

TCP connection이 해제 될 때는 `FIN`, `ACK` flag를 담아서 packet를 주고 받는다.

1. client는 server와 연결 해제를 위해서 `FIN`을 보낸다. 이 때 client는 `FIN-WAIT-1` 상태가 된다.
   - `FIN` flag : 1이면 연결을 해제하기 위해 packet을 보낸다는 뜻.
2. client의 `FIN`을 받은 server는 `CLOSED-WAIT`상태가 된다. 그리고 server는 client의 `FIN`에 대한 ACK와 함께 server가 보내야할 남은 데이터 모두 보낸다.
3. server의 `FIN ACK`를 받은 Client는 server가 본인이 보낸 `FIN`을 잘 받았다라는 것을 알게되어 `FIN-WAIT-2`상태가 된다.
4. server가 남은 데이터를 모두 다 보냈기에 이제 close할거라고 `FIN` 을 client에게 전송. 이 때 server는 `LAST-ACK`상태가 된다.
5. client는 server의 `FIN`을 받고 `TIME-WAIT`상태가 된다. 그리고 그에 대한 `ACK`를 server에게 보낸다. 
6. client의 `ACK`를 받은 server는 `CLOSED`가 된다.
7. client도 시간이 경과하면, socket을 `CLOSED`상태로 바꾼다.

<br>

### Timed-Wait는 왜 있는가?

위 그림을 보면 마지막에 client가 `timed-wait`이후에 socket을 closed하는것을 볼 수 있다. 왜 기다려줄까???

우리는 다음과 같은 corner case를 생각해보면 된다.

> client가 server에게 보낸 ACK가 loss된 상황. 

만약 저런 상황에서 client가 server의 `FIN`을 받자마자 closed해버린다면?? server에서는 client의 FIN ACK를 기다리다가 timeout때문에 다시 ACK를 달라고 요청하게 될 것이다. 근데 client는 closed해버렸기 때문에 `buffer`등등 연결에 필요한 자원들을 해제시켜준 상태...

따라서 loss될 때를 대비하여 기다리는 시간이 필요한 것이고, 그 기다리는 시간이 바로 `timed-wait`이다

<br>

다음엔 TCP의 reliable data transfer를 보장해주는 또 하나의 기술, `Congestion control`에 대해 알아보도록 하자.



## Reference

[kocw network - 이석복 교수님 2015 강의 7, 8강](http://www.kocw.net/home/cview.do?cid=6166c077e545b736)

[TCP HEADER](https://electronicspost.com/tcp-segment-format-with-diagram/)

[TCP HEADER flag list](https://www.howtouselinux.com/post/tcp-flags)

[TCP 3-Way Handshake process](https://afteracademy.com/blog/what-is-a-tcp-3-way-handshake-process)

[Reason why TCP `ACK #` is random](https://www.quora.com/Why-in-a-TCP-sequence-is-a-number-taken-as-a-random-number-and-what-is-the-actual-number-at-the-start?awc=15748_1574583388_8da30461914e738de7495e39d9ab34b1&uiv=6&txtv=8&source=awin&medium=ad&campaign=uad_mkt_en_acq_us_awin&set=awin&pub_id=85386)

[TCP 4-Way Handshake](https://unordinarydays.tistory.com/172)

[TCP closing the connection - rfc793](https://datatracker.ietf.org/doc/html/rfc793#section-3.5)

