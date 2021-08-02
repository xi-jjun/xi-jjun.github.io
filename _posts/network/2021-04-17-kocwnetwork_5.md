---
layout: post
title: "네트워크 - 5"
author: "xi-jjun"
tags: Network

---

# KOCW 5강

KOCW 네트워크 강의(이석복 교수님)를 듣고 정리하여 쓴 글이다.

<br>

## 4강 복습

TransPort Layer에서 **Reliable Data Transfer**를 위한 기본원리에 대해 말을 했었다. 왜냐하면 underline channel이 unreliable하기 때문에 극복하고자 하기 위함이었다.

- Unreliable channel → packet lost/error 의 원인.

- packet error mechanism

  Error detection, feedback, retransmission, sequence #

- Packet loss mechanism

  Time out

<br>

# 5강 시작

## Utilization

과연 우리가 4장에서 배운 RDT protocol은 과연 효율적일까? 4강에서 우리는 packet 1개를 보낸 다음, ACK 신호가 올때까지 기다린 후에 다시 다음 packet을 전송하는 모델을 생각하였다. 하지만 이는 매우 비효율적이다.

<br>
$$
U_{sender}={L/R \over RTT+L/R}
$$

> Utilization : 전체 시간 중에서 sender가 network를 사용하는 비율. 크면 클수록 좋다.

우리가 4강에서 고려했던 RDT 모델은 Utilization이 매우 안좋았다. 그 이유는 아래 그림을 보도록 하자.

<br>

### 4강에서 배운 RDT protocol 모델

![network5_1](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/network5_1.png?raw=True){: width="78%" height="80%"}

- #1.1 : first packet의 첫번째 bit 전송시작.
- #1.2 : first packet의 마지막 bit 전송시작. #1.1~1.2까지가 `Transmission time(delay)`이라고 1강에서 배웠다.
- #2.1 : first packet 첫번째 bit 도착.
- #2.2 : first packet 마지막 bit 도착. 1개의 packet이 잘 도착(No loss, No error)했으니 ACK 보냄.
- #3 : ACK 도착. 다음 packet 전송....

4강에서 우리는 위와 같이 packet을 보냈다. 효율이 안좋은 이유는 **sender가 packet을 하나 보내고 도착을 확인할동안 아무것도 안하고 쉰다**는 것이다. 위의 utilization식에서 RTT값이 매우 커지므로 **packet을 한번에 쏟아버리고 한번에 feedback을 받는게** 더 효율적이다.

> 결론 : 4강에서 생각했던 RDT protocol은 성능이 굉장히 안좋다. 1개 packet을 보내고 도착할 때까지 아무것도 안하고 기다리기만 하기 때문이다.

<br>

## Pipelining : Increased Utilization

![network5_2](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/network5_2.png?raw=True){: width="85%" height="60%"}

<sub> [사진출처](http://contents2.kocw.or.kr/KOCW/document/2015/hanyang/leesukbok0326/5.pdf) </sub>

`pipelining`을 사용해서 packet을 한번에 주고 feedback을 한번에 받게 되면 위 그림에 나왔듯이 Utilization이 증가하는 것을 알 수 있다.

> 한꺼번에 보내는 data가 많을수록 Utilization이 증가한다. 더 효율적이게 된다.

<br>

이번에 5강에서는 **이러한 `pipelining`방식의 RDT protocol을 동작하게 만드는 2가지 apporach**에 대해 배울 것이다.

1. Go-Back-N
2. Selective Repeat

<br>

# Go-Back-N

이제 우리가 먼저 생각해야 할 것은 `pipelining`으로 data를 보낼 때 **얼만큼** 많이 보낼지에 대해서이다. → window size만큼!!

* Window Size : 이 만큼의 packet은 feedback 받지않고 한번에 보낼 수 있다.

<br>

### * 정리하고 들어갈 내용 - ACK 의미

- 4강에서 얘기했던 ACK# : 나 packet #번을 잘 받았어!!
- **Go-Back-N에서의 ACK# : 나 packet #번까지 잘 받았어!!** → 지금 얘기할 ACK의미

<br>

## Go-Back-N in action

- `Go-Back-N` : packet loss되면 N개만큼 돌아와서 해당 packet부터 window 안의 모든 packet을 재전송.

아래에 `window size=4`인 `Go-back-N` 예시를 보도록 하자.

<br>

![network5_3](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/network5_3.png?raw=True){: width="75%" height="90%"}

<br>

<보내기 시작할 때 t=0><br>

![network5_4](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/network5_4.png?raw=True){: width="100%" height="15%"}<br>

숫자 : packet번호, window size = 4인 경우,<br>

- `packet 0` : sender에서 receiver에게 packet 0 전송. [RDT 3.0](https://xi-jjun.github.io/2021-04-15/kocwnetwork_4)이므로 packet 전송하자마자 `timer 0` 이 설정된다.<br>

  - `packet #` : packet #이 전송되자마자 `timer #`이 설정되고, timer가 끝나기 전에 `ACK #` 받게되면 해제된다.<br>

  <packet 0을 보내고 ACK 0을 받고 난 후 상황><br>

  ![network5_5](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/network5_5.png?raw=True){: width="100%" height="15%"}

  <br>

- `packet 1` : packet 1 전송.<br>

  <packet 1을 보내고 ACK 1을 받고 난 후 상황><br>

  ![network5_6](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/network5_6.png?raw=True){: width="100%" height="15%"}<br>

  <br>

- `packet 2` : packet loss 발생!!<br>

  <pacekt 2가 loss된 후 상황><br>

  ![network5_7](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/network5_7.png?raw=True){: width="100%" height="15%"}<br>

  window가 움직이지 않는다. receiver에서는 계속 `ACK 1`만을 보내는 중.<br>

  <br>

- `packet 3~5` : receiver에서 다 버려버린다.<br>

  <packet 3~5는 모두 잘 도착했으나 receiver가 원하는 packet이 아니기에 모두 버려진 상황><br>

  ![network5_8](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/network5_8.png?raw=True){: width="100%" height="15%"}

  <br>

- `packet 2 Time out!!` : receiver에서 `ACK 1`을 계속 보냈기에 `ACK 2`를 못 받게 돼서 packet 2의 timer가 시간초과 됐다.<br>

  → `Go-Back-N` 실행.<br>

- `packet 2~5` : loss가 발생했던 packet으로 돌아가서 해당 packet부터 window의 마지막 packet까지 재전송.<br>

  <packet 2 재전송. `ACK 2` 못 받은 상황 : 한꺼번에 보내는 중...><br>

  ![network5_9](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/network5_9.png?raw=True){: width="100%" height="20%"}<br>

  `ACK 2`를 받기 전 packet loss없이 window안의 packet을 전부 재전송 중이다. `ACK 2`를 받았다면, window는 슬라이드하여 `3, 4, 5, 6`를 포함할 것이다.<br>

  <br>

  <packet 3 재전송. `ACK 3` 못 받은 상황 : 한꺼번에 보내는 중...><br>

  ![network5_10](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/network5_10.png?raw=True){: width="100%" height="20%"}<br>

  `ACK 3`를 받기 전 packet loss없이 window안의 packet을 전부 재전송 중이다. `ACK 3`를 받았다면, window는 슬라이드하여 `4, 5, 6, 7`를 포함할 것이다.<br>

  <br>

  <... packet 5까지 재전송. `ACK 5`받은 후 상황><br>

  ![network5_11](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/network5_11.png?raw=True){: width="100%" height="20%"}<br>

  <br>

`Go-Back-N`에서는 receiver를 아무것도 못하는 바보라고 생각한다. 따라서 receiver에는 buffer도 없고, 할 줄 아는게 ACK 주는거랑 원하는 packet이 아니면 버리는 것 밖에 없다. 그리고 sender에서는 packet loss에 대비하기 위해서 window 안에 있는 packet을 저장하는 buffer가 존재해야 한다. 따라서 window는 보낼 packet들을 임시저장하는 buffer라고 생각할 수 있다.<br>

<br>

근데 의문이 있다. 아직 배울게 더 남아있다는 사실을 알긴 하다만 `Go-Back-N`방법은 너무 비효율적이라는 생각이 들었다. loss된 packet만 다시 보내면 되는데 굳이 window안에 있는 전부를 재전송하는 것일까? 바로 위에서 설명했듯이 receiver측에는 buffer가 없다고 가정했기 때문이다. 강의에서 나의 궁금증을 바로 해결해 줬다.<br>

<br>

# Selective Repeat<br>

실제 window size는 아까 위에서 가정했던 것 처럼 작지가 않다. 따라서 1개가 loss됐다고 전부 다 다시 보내기엔 너무 비효율적일 것이다. 따라서 `Selective Repeat`이 나왔다.<br>

<br>

### * 다시!! 정리하고 들어갈 내용 - ACK 의미<br>

- 4강에서 얘기했던 ACK # : 나 packet #번 잘 받았어!!
- Go-Back-N의 ACK # : 나 #번 packet까지 잘 받았어!!
- **Selective Repeat의 ACK # : 나 packet #번 잘 받았어!!**

<br>

## Selective Repeat in action

- `Selective Repeat` : loss된 packet만 재전송.

아래에 `window size=4`인 `Selective Repeat` 예시를 보도록 하자.

<br>

![network5_12](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/network5_12.png?raw=True){: width="80%" height="75%"}

아까 `Go-Back-N`이랑 비슷한 맥락이지만, 차이점은 제대로 도착한 packet에 대해서는 receiver의 buffer에 저장한다는 것이다. error없이 받은 packet을 버리기 않기 때문에 loss가 발생했을 때, 해당 loss가 발생한 packet에 대해서만 재전송을 한다. 그리고 window는 다음으로 한번에 넘어가게 된다.
