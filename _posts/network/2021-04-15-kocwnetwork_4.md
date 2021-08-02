---
layout: post
title: "네트워크 - 4"
author: "xi-jjun"
tags: Network

---

# KOCW 4강

KOCW 네트워크 강의(이석복 교수님)를 듣고 정리하여 쓴 글이다.

<br>

# Principle of Reliable Data Transfer : TCP

> Application layer에서 data가 하나도 loss나 error 없이 receiver의 application layer로 가는 것. 

<br>

![network4_1](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/network4_1.png?raw=True){: width="85%" height="35%"}

이전까지는 이상적인 상황에서의 packet(data)의 이동을 생각했지만, 실제로는 packet loss와 error가 일어날 수 있다. 이러한 상황이 일어나면 안되기 때문에 우리는 신뢰성이 있는 data를 전송해야 한다.

<br>

# RDT protocol(Reliable Data Transfer protocol)

data의 loss나 error가 없는 것이 신뢰성 있는 data를 전송하는 것이다. 

- 가정
  1. 한번에 하나의 packet만 보낸다.
  2. packet을 하나 보내고 확인한 후 다시 하나를 보낸다.

따라서 우리는 packet을 전송하는 과정 중에 loss, error가 일어날 상황에 대해 다음 3가지 케이스로 생각해볼 수 있다.

<br>

## Case 1 : Perfect Channel - RDT 1.0

이 경우에는 channel에 문제가 없어서 packet의 loss나 error없이 잘 전송된 케이스이다. 따라서 따로 고려 해줄 것이 없다.

<br>

## Case 2 : Packet Error(But no Loss) - RDT 2.0

packet error에 대해 필요한 것들이 있다. 먼저 packet에 error가 일어났는지 확인할 방법(error detection), error가 일어났다면 sender측에게 다시 보내달라고 알려줄 방법(feedback) 이 필요하다.

- Error detection : checksum bit를 packet에 같이 보내서 error의 유무를 판단.

- Feedback : receiver 측에서 packet을 받을 때 error없이 잘 받았는지 계속 feedback을 sender측에게 줘야 한다.

  1. Acknowledgement(ACKs) : receiver가 sender에게 packet을 **error 없이 잘 받았다**라고 알림을 주는 것.

  2. Negative Acknowledgement(NAKs) : receiver가 sender에게 packet을 **error가 발생**했다고 알림을 주는 것.

- Retransmission : sender가 receiver로부터 **NAKs**를 받게되면 재전송 한다.

<br>

### 비유적 예시 - RDT 2.0

전화 통화를 할 때 상대방 말을 들으면서 우리는 `어, 응, 그래`와 같은 **잘 알아들었다는 신호(ACKs)**를 상대방에게 보낸다. 그러다 음질이 안좋았거나 발음이 안좋아서 상대방이 무슨 말을 하는지 못 알아 듣는 경우 우리는 `뭐라고?, 응?, 다시 말해줄래?`등의 **못 알아 들었다는 신호(NAKs)**를 보낸다.

<br>

### RDT 2.0의 문제점

1. 만약 ACK, NAK 신호에 error가 있다면? checksum bit 같이 보내면 된다. 그리고 error라면 재전송 하면 되는 것이다. 그러나 receiver측에서는 같은 packet을 2번 받은 것이 된다. 따라서 error때문에 **받은 data가 새로운 data인지 다시 보낸 data인지** 구분이 안가게 된다. 다음 그림을 보면서 이해를 해보자.

![network4_2](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/network4_2.png?raw=True){: width="65%" height="60%"}

- #1 : `hello`라는 data를 receiver에게 보내려고 한다.
- #2 : receiver는 `hello`라는 data를 잘 받아서 ACK 신호를 보내려고 하지만 ACK신호에 Error가 발생했다.
- #3 : checksum bit로 ACK신호의 error를 감지한 sender는 이 신호가 ACK인지 NAK인지 판단이 안되기 때문에 Retransmission한다.
- #4 : receiver는 #2에서 받았던 data와 현재 받은 data가 서로 다른 새로운 것으로써 이어지는 data(`hello hello`)인지, 아니면 재전송으로써 받은 data(`hello`)인지 구분을 못하게 되는 문제가 발생한다.

<br>

### 문제점을 해결하기 위한 RDT 2.1

`RDT 2.0`에서의 문제점을 해결하기 위해 등장했다.

> 각각의 packet에 `Sequence #(Seq #)` 를 추가하는 것이다.

`Seq #`을 packet에 붙이게 된다면, 

![network4_3](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/network4_3.png?raw=True){: width="65%" height="60%"}

- #1 : `hello`라는 data를 receiver에게 보내려고 한다. `seq 0`번이다.
- #2 : receiver는 `hello`라는 data를 잘 받았다. 그래서 `seq 1`의 packet을 받기 위해 대기한다. packet을 잘 받았기에 ACK 신호를 보내려고 하지만 ACK신호에 Error가 발생했다.
- #3 : checksum bit로 ACK신호의 error를 감지한 sender는 이 신호가 ACK인지 NAK인지 판단이 안되기 때문에 Retransmission한다.
- #4 : receiver는 `seq 0`의 packet을 받게되고, `seq 1`의 packet만 기다리고 있으므로 버린다. packet 자체는 error없이 왔기 때문에 ACK신호를 sender에게 보내준다.
- #이후 : sender는 `seq 1` packet을 보낸다...

현재 가정은 **한번에 하나의 packet만을 보낸다**이기 때문에 `Seq #`은 2개가 필요하다.

<br>

### RDT 2.2 : a Nak-free protocol

기능은 `RDT 2.1`과 똑같지만 `NAK`대신 `ACK(seq #)`을 사용하여 중복된 packet을 받았는지 알려준다.

![network4_4](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/network4_4.png?raw=True){: width="65%" height="60%"}

- #1 : `Hello`라는 packet을 `seq 0`으로 전송.
- #2 : receiver는 해당 packet을 잘 수신했기에 ACK신호를 보냄. ACK신호는 **가장 마지막 번째의 잘 받은 packet seq #**과 같이 보낸다. 따라서 마지막으로 받은 `seq 0`을 ACK신호와 같이 전송.
- #3 : receiver가 잘 받았음을 확인한 sender는 두번째 packet을 보내려고 한다. `World`라는 내용이 담긴 packet을 `seq 1`로 보냄.
- #3~4 : packet이 전송되는 도중 error발생.
- #4 : 전송도중 error가 발생했기에 packet을 못 받음. 원래라면 NAK를 보냈겠지만 `RDT 2.2`에서는 **가장 마지막으로 받은 packet의 seq #**을 ACK에 실어 보낸다. `ACK(0)` 전송.
- #5 : `ACK(0)`을 받은 sender는 다시 `seq 1 packet`을 재전송(Retransmission).
- #6 : packet을 잘 받은 receiver는 `ACK(1)`신호를 준다. 
- #이후 : sender는 `seq 0 packet`을 보낸다...(#1의 packet과 다름)

> `ACK(seq #)` : 가장 마지막으로 sender가 receiver에게 준 packet이 `seq #`번째 packet이다. 그러니깐 이 다음 packet을 receiver에게 달라는 신호.

<br>

## Case 3 : Channel with Loss & Packet Errors - RDT 3.0

sender가 보낸 packet이 Loss되면 sender에게 아무것도 돌아오지 않는다. 계속 기다리게 되기 때문에 `Timer`를 설정해놓는다.

`Timer` : 일정시간 이상 지났는데도 receiver에서 feedback이 없다면 Loss라고 판단 후 재전송. 

- `Timer`가 짧으면 Loss가 일어났을 때 recovery가 빠르다. 그러나 *중복 packe*t이 많이 발생할 수 있고 이는 network overhead를 야기한다.
  - 중복 packet : feedback이 조금 늦거나 packet이 가는데 오래걸려서 `Timer`의 시간이 다 된 경우, Loss가 아니지만 재전송을 하게 되기 때문에 생길 수 있다.
- `Timer`가 길면 Loss가 일어났을 때 확실히 알고 재전송을 할 수 있게 된다. 이는 network overhead가 적게간다. 그러나 Loss가 일어났을 때 recovery가 느리다.

<br>

## 정리

1. Unreliable channel에서 일어날 수 있는 일
   1. Packet error
   2. Packet loss
2. Packet error 해결을 위한 mechanism
   1. Error detection -> checksum bit
   2. Feedback -> ACK or NAK
   3. Retransmission -> NAK면 재전송
   4. Sequence # -> ACK/NAK가 error일 경우 고려.
3. Packet loss 해결을 위한 mechanism
   - `Timeout` : 일정시간 지나면 sender에서 receiver에게 packet 재전송(Loss로 판단했기 때문)

> TCP Layer가 이러한 기능들을 제공하기 때문에 `segment HEADER`부분에는 `ACK, Seq#, checksum bit`등등이 있다. flow control과 같은 다른 기능도 제공하기에 `segment HEADER`부분에는 많은 field가 있는 것이다.

<br>

## 다음 시간에 할 것

이번시간에 배운 이 RDT protocl은 매우 단순해서 실제로 쓸 수는 없다. 왜냐하면 우리는 **한번에 하나의 packet만을 전송**한다고 가정했기 때문이다. 따라서 다음 시간에는 'Pipelined protocols'에 대해 배워볼 것이다.





