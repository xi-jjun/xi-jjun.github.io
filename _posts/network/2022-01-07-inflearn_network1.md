---
layout: post
title: "HTTP(Inflearn)"
author: "xi-jjun"
tags: Network

---

# HTTP

Inflearn `김영한` 강사님의 [모든 개발자를 위한 HTTP 웹 기본 지식](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/) 을 듣고 정리하는 글입니다.

<br>

# 모든 것이 HTTP

HTTP(HyperText Transfer Protocol) 메세지에 모든 것을 전송한다.

- HTML, TEXT
- IMG, 음성, 영상, 파일
- Json, XML 등등.. 거의 모든 형태의 데이터를 전송할 수 있다.

<br>

## 역사

1. HTTP/0.9 (1991년) : GET method만 지원. HTTP header X.
2. HTTP/1.0 (1996년) : method, header 추가.
3. **HTTP/1.1** (1997년) : 가장 많이 사용. 
4. HTTP/2 (2015년) : 성능 개선
5. HTTP/3 : TCP 대신 UDP 사용. 성능 개선.

강의에서는 HTTP/1.1 을 기준으로 공부를 하라고 한다.

<br>

## 기반 protocol

- TCP : HTTP/1.1, HTTP/2. 
- UDP : HTTP/3
- 현재는 1.1을 주로 사용하지만, 2와 3도 증가하는 추세. UDP로 하면 `3-way handshake` 를 안하기에 속도가 더 빠르다는 장점이 있다. 그러나 1.1 spec을 아는게 중요하다.

<br>

# HTTP 특징

- Client-Server 구조
- stateless, conectionless
- Http message 통해 통신
- 단순함, 확장가능

<br>

## Client-Server 구조

- `request, response` 구조
- Client는 서버에 `request`을 보내고 응답을 기다린다. 
- 서버는 `request`을 받고 그에 대한 결과를 만들어서 `response`한다.

예전에는 이러한 분리없이 하나였다고 한다. 이렇게 분리하면 

1. Business logic, data 를 서버에 위임할 수 있다. 그 행위에만 집중하면 된다.
2. UI, UX와 같은 화면구성은 Client에 위임하여 거기에만 집중할 수 있다.

라는 장점이 있다. 따라서 **각각 독립적인 진화**가 가능하다.

<br>

## Stateless Protocol

- Server가 Client의 상태를 보존하지 않는다.
- 장점 : 서버 확장성이 좋아진다. (Scale out)
- 단점 : Client가 추가 데이터를 전송해야 한다.

<br>

### 만약 stateful 하다면?

```text
# 노트북 매장에서 손님과 점원의 대화
고객 : 노트북 얼마에요?
점원 A : 100만원 입니다.

고객 : 2개 구매할게요.
점원 A : 200만원 입니다. 현금, 카드 어떤 것으로 결제하실건가요? 
→ 이전 '100만원짜리 노트북'이라는 정보가 유지. 개수 정보와 결제 방법도 유지된다.

고객 : 카드로 할게요.
점원 A : 200만원 결제 완료 됐습니다!
```

만약 여기서 점원이 중간에 바뀐다면? 결국 고객은 처음부터 무엇을 살것인지, 몇개을 살것인지, 어떤 방식으로 결제할 것인지 다시 다 말해줘야 한다.

<br>

### stateless의 경우는?

```text
# 노트북 매장에서 손님과 점원의 대화
고객 : 노트북 얼마에요?
점원 A : 100만원 입니다.

고객 : 노트북 2개 구매할게요.
점원 B : 노트북 2개 200만원 입니다. 현금, 카드 어떤 것으로 결제하실건가요? 
→ 이전 '100만원짜리 노트북'이라는 정보가 유지. 개수 정보와 결제 방법도 유지된다.

고객 : 노트북 2개 구매를 카드로 할게요.
점원 C : 200만원 결제 완료 됐습니다!
```

이런식으로 점원에게 처음부터 다시 설명해줘야 한다. → Client가 보내는 데이터가 많아진다는 것.

<br>

### stateful, stateless 정리

- Stateful : 중간에 다른 점원으로 바뀌면 안됨. (바뀐 정보를 다른 점원에게 미리 알려줘야 함)

- Stateless : 중간에 다른 점원으로 상관없음.

  - 갑자기 고객이 증가해도 점원을 많이 투입하면 됨.

    == 클라이언트 요청이 증가하면 서버를 많이 투입하면 됨.

- stateless는 응답하는 서버를 쉽게 바꿀 수 있다. → 서버 확장이 쉬운 이유

<br>

### 예시

![inflearn1_1](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/inflearn1_1.png?raw=True){: width="75%"}

![inflearn1_2](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/inflearn1_2.png?raw=True){: width="75%"}

![inflearn1_3](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/inflearn1_3.png?raw=True){: width="75%"}

이런식의 차이가 있다. (사진은 인프런 김영한 강사님의 **모든 개발자를 위한 HTTP 웹 기본 지식** 에서 제공하는 자료를 일부 캡쳐한 것입니다.)

<sub>[사진 출처](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/) - **모든 개발자를 위한 HTTP 웹 기본 지식**[김영한]</sub>

<br>

### 한계

- 모든 것을 무상태로 설계 할수도, 못할 수도 있다.
- 단순한 정적 화면 → stateless하게 설계 가능
- 로그인 화면 → stateful하게 설계해야 함. 일반적으로 브라우저 쿠키와 서버 세션 등을 이용해서 상태 유지.
- 상태 유지는 최소한만 사용!

<br>

## Conectionless

- 만약 Client-Server 연결을 계속 유지하는 model이라면?

  → 계속 유지하기 위해 서버의 자원이 무의미하게 소모된다.

- 유지하지 않는 model?

  → 요청에 대한 응답이 끝나면 연결을 끊는다. 따라서 서버가 최소한의 자원만 사용하게 한다.

  Http는 기본적으로 `conectionless`하다. 

난 처음엔 '그냥 계속 연결시켜 놓으면 안되나...? 어차피 버튼 누르고, 검색계속 할텐데...' 라고 생각했다. 그러나 실제로 '초 단위 이하의 빠른 응답' + '브라우저에서 연속으로 버튼을 클릭하는 행위는 거의 없음' 이라는 상황이기 때문에 *굳이 연결을 유지할 필요가 없다*라는 말을 이해하게 됐다. 비연결성이기 때문에 서버의 자원을 더욱 효율적으로 사용할 수 있는 것이다.

<br>

### 한계와 극복

그러나! 연결을 유지하지 않기 때문에 매번 연결이 있을 때마다 `3 way handshake`를 해줘야 한다. 그리고 요청을 하여 html을 가져오면, 그에 필요한 javascript를 가져오기 위해 다시 연결요청을 한다. 그리고 또 필요한 다른 미디어 파일을 가져오기 위해서 또 연결 요청을 하게 된다. 사용자는 1번의 요청을 한 것처럼 보이지만, 브라우저는 서버와 3번의 연결을 한 것이 된다

→ HTTP Persistence Conections로 문제를 해결.

![inflearn1_4](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/inflearn1_4.png?raw=True){: width="75%"}

![inflearn1_5](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/network/img/inflearn1_5.png?raw=True){: width="75%"}

<sub>[사진 출처](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/) - **모든 개발자를 위한 HTTP 웹 기본 지식**[김영한]</sub>

<br>

### Stateless하게 설계하는 것이 중요

대용량 트래픽이 드어올 때, scale out 하는 것이 가능하기 때문이다.

<br>

`http message` 부터는 다음 포스팅에서 이어 하겠다.
