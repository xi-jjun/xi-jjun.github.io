---
layout: post
title: "TCP - Congestion control"
author: "xi-jjun"
tags: Network

---

# KOCW 8강

KOCW 네트워크 강의(이석복 교수님)를 듣고 정리하여 쓴 글이다.

<br>

# Congestion Control

이전 시간에는 `Flow control`에 대해서 배웠었다. 우리가 통신을 할 때 receiver를 기준으로 data를 보내는 속도를 조절해야 하고, 조절을 위해서는 buffer와 같은 자원들이 필요했기에, 그 자원을 준비하기 위해서 `3-way-handshake`와 같은 것들을 공부했었다.

이번에는 client, server의 상황이 아닌, `network`를 고려한 reliable data transfer 에 대해 알아볼 것이다. 이전에는 그냥 `receiver buffer`의 빈 공간에 대한 정보를 TCP HEADER의 `receive window`에 적어줌으로써 sender에게 알려줄 수 있었다. 그런데 network의 상황이라는 것은... 그 누구도 정확히 알 수가 없다. 그렇기 때문에 network상황을 추측하여 data를 보내주는 역할을 하는게 바로 `Congestion control`이다.

<br>

## 굳이? 그냥 실패하면 ACK안갈거고, 그러면 time out 나서 재전송 하면 되는거 아니냐?

다음과 같은 과정을 보도록 하자.

> A가 10MB의 data를 B에게 보내려는 상황

1. A가 data전송.
2. network상황 때문에 중간에서 실패.
3. A가 data 재전송... → 점점 이렇게 보내는 양이 많아진다. 그리고 network는 모두가 이용하는 것이기에 다른 곳에서도 이렇게 재전송을 해버리면 결국 network는 무너지고 말 것이다.

따라서 network는 '모두가 사용하는 것'이기에 안 무너지게 'network상황에 맞게 데이터를 보내는 속도를 조절'해줘야한다. 

>Indicating a large window encourages transmissions.  If more data arrives than can be accepted, it will be discarded.  This will result in excessive retransmissions, adding unnecessarily to the load on the network and the TCPs.  Indicating a small window may restrict the transmission of data to the point of introducing a round trip delay between each new segment transmitted.

받아드릴 수 있는 그 이상의 데이터양이 들어온다면, loss 된다. 이러한 결과는 과도한 재전송을 만들게 될거고, 불필요하게 TCP와 network의 load가 필요할 것이다.

<br>

## 역사

잠시 이러한 기술에 대한 역사를 보고 가도록 하겠다. 자료는 [여기](https://www.scs.stanford.edu/10au-cs144/notes/l4.pdf)서 찾았다. 1974년도, `3-way handshake`가 나왔다. 그리고 점차 인터넷이 발전함에 따라 `network congestion collapse`문제가 생기기 시작하자 1987년도에 congestion control 알고리즘인 `Tehoe`가 나오게 됐다. (데이터의 loss가 발생했을 때 보내는 데이터의 양을 처음에 보냈던 양으로 줄여서 보내기) 이에 더 빠른 데이터 전송률의 복구가 가능한 알고리즘인 `Reno`가 등장하게 됐다.

<br>

## 어떻게 알고 조절하는건데..?

- Network-assisted congestion control : network에서 본인의 상태정보를 주겠다는 뜻.

  → 구현은 안돼있다. router는 바쁘다고 한다...

- End to end congestion control : sender와 receiver가 알아서 network의 상황을 유추해서 조절하는 것.

  → TCP segment HEADER로 ACK가 어떻게 동작하는지로 유추한다. 따라서 아주 정확하지는 않다. 

우리는 위와 같은 이상적인 상황 말고 현재 쓰이는 `End-end congestion control`에 대해 알아볼 것이다.

<br>

## MMS(Maximum Segment Size)

실제로 보내는 데이터의 양은 `window size`와 관련있다. 통신을 하기 전에는 network에 대한 정보가 하나도 없다. 따라서 window size를 잡기가 애매하다. 따라서 등장하는 개념이 `MMS(Maximum Segment Size)`이다.

> MMS : 1개의 Segment에서 보낼 수 있는 data의 양. 

여기서 data 양이란 IP, TCP HEADER부분을 제외한 순수하게 data를 담을 수 있는 공간을 뜻한다.

> window size : ACK를 받지 않고 한번에 보낼 수 있는 데이터 양

따라서 처음에 window size = 1MMS로 되어있다. (제일 처음에는 1개의 segment만 보낼 수 있게)

- slow start : 1MMS, 2MMS, 4MMS, 8MMS... exp 증가
- additive increase : aMMS, (a + 1)MMS, (a + 2)MMS... linear 증가



<br>

## 구현

TCP가 congestion control을 구현하는 방법

> 상대방의 feedback으로 부터 network의 상황을 유추한다.

TCP Congestion control 3 Phases

1. Slow start : bottle neck의 bandwidth를 모르기에 보내는 양을 빠르게 늘린다. 1, 2, 4, 8....
2. Additive increase : 어느정도 양이 늘어나면, linear하게 보내는 양을 늘린다. 1, 2, 3, 4, 5...
3. Multiplicative decrease : loss가 발생하면 보내는 양 줄인다.

network상황은 모르기에 조심스러울 수 밖에 없다. 이전에는 `AIMD(Additive Increase/Multiplicative decrease)`방법을 사용했었는데 network상황이 좋을 때에도 보내는 양이 너무 느리게 올라가서 `slow start`라는 개념이 등장하게 됐다.

근데 `slow start`처럼 너무 빠르게 증가해버리면 network혼잡이 빈번하게 일어나게 된다. 따라서 `threshold`라는 임계점을 정해서 그 이후에는 `additvie increase`처럼 동작하도록 했다.

그러다가 데이터의 Loss가 발생하게 되면, 

- `Tehoe` : 1MMS부터 다시 보내는 데이터를 `slow start`로 증가시킨다.
- `Reno` : 보내던 양의 반부터 `additive increase`시킨다.

<br>

## TCP Fairness

1개의 라우터를 공유하는 2개의 host가 있다고 해보자. 과연 2개의 host는 **'공평하게 bandwidth를 사용'** 할까? 결론은 결국엔 공평하게 사용이 된다고 한다. loss되면 보내는 데이터의 양을 반으로 줄이는 과정에서 host들은 공평하게 bandwidth를 사용한다.

<br>

## Reference

[kocw network - 이석복 교수님 2015 강의 7, 8강](http://www.kocw.net/home/cview.do?cid=6166c077e545b736)

[History of TCP/IP](https://www.scs.stanford.edu/10au-cs144/notes/l4.pdf)

[TCP congestion control](https://datatracker.ietf.org/doc/html/rfc793#section-3.7)

[TCP congestion control 참고2](https://github.com/im-d-team/Dev-Docs/blob/master/Network/congestion%20control.md)



