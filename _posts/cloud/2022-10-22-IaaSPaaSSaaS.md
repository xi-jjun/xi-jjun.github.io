---
layout: post
title: "IaaS vs PaaS vs SaaS"
author: "xi-jjun"
tags: infra
published: true

---

# IaaS vs PaaS vs SaaS

기초적인 내용이고 다시 정리하기 위해 글을 작성하게 됐다.

<br>

## ~~ As a Service

일단 기본적으로 3개의 개념 모두 다 뒤에 '~aaS'가 똑같이 붙어있는 것을 볼 수 있다. 이걸 풀어서 보자면 

> ??? as a Service : ??? 를 서비스 처럼

이라는 말로 바꿔볼 수 있을 것 같다. Red hat 에서는 다음과 같이 얘기하고 있다.

> "As-a-service" generally means a cloud computing service that is provided by a third party so that you can focus on what’s more important to you, like your code and relationships with your customers. - [Red Hat](https://www.redhat.com/en/topics/cloud-computing/iaas-vs-paas-vs-saas)

'As-a-service'는 제 3자에 의해 제공되는 클라우드 컴퓨팅 서비스를 말한다. 이를 통해서 고객과 우리의 코드의 관계와 같이 더 중요하다고 생각되는 것에 집중을 할 수 있게 해주는 것이다. `IaaS`, `PaaS`, `SaaS` 와 같은 서비스들은 사용자가 관리해야 하는 수준에 따라 다음과 같이 나뉜다.

![iaas_paas_saas1](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/cloud/img/iaas_paas_saas1.png?raw=True){: height="50%"}

<sub>[사진 출처 - Red Hat](https://www.redhat.com/en/topics/cloud-computing/iaas-vs-paas-vs-saas)</sub>

사진으로 보다시피 우리가 관리해줘야할 영역에 대해서 보여주고 있다. 이를 토대로 위 4가지 요소에 대해 설명해보도록 하겠다

<br>

## On-premise

과거에는 서버 컴퓨터를 둘 공간을 마련하여 회사 내에서 직접 관리를 하였다. 쉽게 말해서 직접 컴퓨터를 구입하여 회사 내에서 서버들을 구축, 운영, 관리한 것이다. 이전에 [serverless 포스팅](https://xi-jjun.github.io/2022-10-17/whatIsServerless)에서 클라우드 컴퓨팅이 나타난 배경에 대해 설명할 때도 언급했지만 이와 같은 `on-premise` 방식은 하드웨어, 소프트웨어를 전부 관리해줘야하기 때문에 신경써줘야할게 많다는 것이다. 위 그림만 봐도 관리를 전부 다 해줘야함을 볼 수 있다. 

따라서 클라우드 컴퓨팅은 이러한 인프라에 대해서 제 3자에게 관리를 위임하여 **'다른 일에 더 집중'**할 수 있게 해주기에 나타난 것이다. 다음 3가지 서비스는 대표적인 클라우드 컴퓨팅 서비스이다.

<br>

## IaaS : Infrastructure as a Service

> 인프라를 서비스처럼

직역하면 위와 같이 되는데 나름 와닿았던 설명이었다. 말 그대로 '인프라를 서비스처럼 이용하겠다'라는 것인데 좀 헷갈렸던 부분이 기존 `on-premise`방식에서도 남는 서버 컴퓨터 자원에 대해서는 빌려준다거나... 이러한 일을 했을 것 같은데 그러한 것들도 `IaaS`와 같은건지 궁금했다. 

이 부분에 대해서는 아니라고 생각을 했던 이유가 '서비스'라는 단어에 어울리지 않는다고 생각했기 때문이다. 서비스란 

> 장사에서 손님을 접대하고 편의를 제공하는 것 

따라서 컴퓨터 자원을 잠시 빌려준다는 목적은 기본적으로 '서비스를 위한 행위'가 아닌 것이다. 남으니깐 빌려준다는 것이기에 애초에 고객의 편의성이 고려되지 않았을 가능성이 높다고 생각했다. 이러한 점이 `IaaS`와의 차이점인 것 같다.

<br>

`IaaS`는 사용자가 몇분만에 서버를 사용가능한 상태로 제공받을 수 있다는 점에서 '사용자의 편의성'과 '제공한다는 목적'을 갖췄기에 서비스이다. 사용자는 `on-premise` 방식 때 처럼 직접 컴퓨터를 사서 관리할 필요 없이 OS, middleware, Runtime, Data, Application 만 관리해주면 된다는 것이다. 그 외에는 서비스 제공측에서 관리해주기 때문이다.

- 장점
  - 필요에 따라 확장하고 싶다면 돈 더 지불하고 서버를 구입하면 된다. 
  - 하드웨어를 직접 관리할 필요가 없어진다. (온도 조절, scale-up 등등)
- 단점
  - 보안문제 : 서버 컴퓨터가 서비스 제공측의 보안에 의존한다.
  - 같은 인프라 자원을 여러명의 사용자가 쓸 때 발생할 수 있는 문제점이 생길 수 있다.

개념을 알기 위해서 글을 길게 썼지만 AWS EC2 서비스를 생각하면 이해가 더 빠를 것 같다. 우리가 원하는 컴퓨터 자원을 기반으로 하나의 클라우드 컴퓨터가 생기는 서비스인 EC2가 바로 `IaaS`의 대표적인 예시이다.

<br>

## PaaS : Platform as a Service

`IaaS`는 서비스 제공 측에서 하드웨어 자원만을 관리해주는 역할을 맡았다면, `PaaS`는 소프트웨어도 같이 호스팅하여 사용자는 그저 `Application`에만 집중할 수 있게 해주는 서비스이다. 주로 개발자를 대상으로하여 별도의 개발환경 세팅을 서비스 제공 측에게 위임하고 사용자는 코드만 배포하여 원하는 서비스를 구축할 수 있는 것이다.

`IaaS`는 기본적으로 WS, WAS와 같은 소프트웨어를 직접 관리해줘야 서버로서의 기능을 할 수 있다. `PaaS`에서는 이러한 부분을 

>서비스로서 'Platform'까지 제공하겠다 라는 것.

대표적인 예시로는 `AWS Lambda`가 있다. 이전 포스팅에서 아주 귀여운 API 서버를 개발했었는데 다음과 같은 장점을 갖고 있다고 느꼈다.

- 장점
  - 쉽고 빠른 배포가 가능하다.
  - 개발자는 인프라에 신경 안쓰고 오직 application에만 집중할 수 있다.
- 단점
  - 서비스를 제공하는 측의 플랫폼에 의존적이게 된다. => 이 같은 단점은 migration을 힘들게 한다

유투브를 보면서 공부했었는데 거기서 이런 말을 했다.

> `IaaS`가 빈 집이라면, `PaaS`는 built-in 옵션이 있는 집.

이 말을 보고 바로 이해해버렸다.

<br>

## SaaS : Software as a Service

> 인터넷 브라우저를 통해 최종 사용자에게 애플리케이션을 제공하는 클라우드 기반 소프트웨어 모델

사용자가 오직 고민할 것은 '어떻게 사용할까'이다. `PaaS`에서는 '어떻게 개발을 하지?' 라는 생각에 집중을 했다면 `SaaS`는 어떻게 비지니스에 사용할 수 있을까를 고민하면 된다.

대표적인 예시로는 google docs, office 365 등이 있다.

- 장점
  - 즉시 사용이 가능하다.
  - 설치하기 위한 용량이 필요없다. (인터넷 브라우저를 통해 사용하기 때문이다)
- 단점
  - 우리가 원하는대로 customizing하기가 어렵다. (당연히 우리가 만든게 아니라면 직접 바꿀 수 없기 때문)

<br>

## 정리

이렇게 평소에 대충만 알고 있던 내용에 대해서 정리를 하니깐 좀 마음이 편해지는 것 같다. 마지막으로 다른 유투버가 들었던 예시로 한번에 정리를 해보도록 하겠다.

- `On-premise` : 아무것도 없는 빈 방에서 카레 만들기
  - 서비스 제공 측 : 빈 방 제공
  - 사용자 : 직접 가스레인지 사고, 가스 설치하고 카레 재료사서 카레 만들기
- `IaaS` : 조리도구와 야채, 강황만 있는 방에서 카레 만들기
  - 서비스 제공 측 : 빈 방, 조리도구, 양파, 강황, 등등 원재료 제공
  - 사용자 : 주어진 도구와 재료로 카레 만들기
- `PaaS` : 조리도구과 3분카레 제품만 있는 방에서 카레 만들기
  - 서비스 제공 측 : 빈 방, 조리도구, 3분카레 제품 제공
  - 사용자 : 주어진 도구와 3분카레로 카레 만들기
- `SaaS` : 조리도구? 3분카레? 다 필요없고 카레 배달시키기
  - 서비스 제공 측 : 배달 서비스 제공
  - 사용자 : 카레 배달 시키기

<br>

## Reference

[IaaS PaaS SaaS - Red hat](https://www.redhat.com/en/topics/cloud-computing/iaas-vs-paas-vs-saas)

[IaaS PaaS SaaS](https://www.youtube.com/watch?v=Nq5JHAW8XYQ)

[IaaS PaaS SaaS pros and cons](https://www.whatap.io/ko/blog/9/)

[cloud computing 종류 - youtube](https://www.youtube.com/watch?v=oY8Tc5OQ-JI)

[what is SaaS - aws](https://aws.amazon.com/ko/what-is/saas/)
