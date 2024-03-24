---
layout: post
title: "Container 란?"
author: "xi-jjun"
tags: Study, Linux, Container


---

# Container 공부 시작!

배포를 할 때에 AWS ECS서비스를 자주 이용하는 편인데, 이유는 로컬에서 매번 docker로 container환경에서 빌드를 하고 있기 때문이다. 평소 사용하는 기술에 대한 deep dive를 하기 위해서 시작하는 바이다.

<br>

## 개요

container 환경에서 어플리케이션을 실행하고 있으나, 관련 지식이 부족한 듯하여 container deep dive가 필요하다고 생각이 됐고, 
회사 내에서 필요에 의해 특정 서비스의 로컬 개발환경을 container로 구성하려고 하기에 관련된 리서치를 하기 위한 목적이다.

[docker roadmap](https://roadmap.sh/docker)을 따라서 하나씩 정리할 예정이다.

<br>

## 컨테이너란?

> 소프트웨어 공학에서 컨테이너화란 SW 유형이나 vendor 혹은 클라우드 환경인지도 상관없이 **`container`** 라고 불리는 독립된 유저 공간에서 실행될 수 있도록 여러 네트워크 리소스에 대한 OS level에서의 가상화 또는 application level에서의 가상화이다. - [출처: wikipedia](https://en.wikipedia.org/wiki/Containerization_(computing))

컨테이너화에 대한 정의를 위키를 통해 알아봤는데, container에 대해서는 아래와 같이 설명하고 있다.

> container는 어플리케이션을 둘러싸고 있으며, 다른 환경에 영향받지 않고 독립적으로 유지될 수 있는 이식성 높은 클라우드 또는 비클라우드 환경이다. - [출처: wikipedia](https://en.wikipedia.org/wiki/Containerization_(computing))

containerization, container 둘 다 중요한 부분은, "다른 환경에 영향받지 않는 독립된 환경" 이라는 말이 들어가는데 이 점이 바로 핵심이라고 생각한다.

음.. 그래도 조금 더 와닿는 설명을 아틀라시안 블로그에서 찾았다.

> 소프트웨어 실행에 필요한 모든 의존성들을 포함하고 있는 가벼운 소프트웨어 패키지를 뜻한다. - [출처 - atlassian 블로그](https://www.atlassian.com/microservices/cloud-computing/containers-vs-vms)

다른 환경에 영향을 받지 않으면서 우리의 어플리케이션을 실행하기 위해 '실행에 필요한 모든 의존성을 갖고있는 소프트웨어 패키지'라는 설명까지 추가되니, container에 대해 더욱 명확히 알 수 있는 느낌이 든다.

<br>

## 왜 독립된 환경이 필요할까? - 개인적인 경험

그렇다면 왜 우리는 다른 환경에 영향받지 않는 독립된 환경이 필요했던 것일까?

사실 이 질문에 대한 대답은 굳이 검색을 하지 않더라도, 개발을 해본 사람들이라면 어느정도 답변을 할 수 있으리라 생각한다. 나도 이러한 환경이 필요했을 때가 여럿 있었는데, **MySQL 로컬 개발환경 셋팅** 때가 가장 절실했고 이 때 가장 많이 컨테이너 덕을 본 것 같다.

현재 내 컴퓨터에는 사이드 프로젝트에 의해 MySQL 서버 2개가 필요했다. database를 나눠서 진행해도 상관없으나 기본적으로 모든 곳에서 동일하게 셋팅되어야할 개발환경인데, 어떤 사람은 database만 나눠서 셋팅하고, 어떤 사람은 새로 MySQL 설치해서 사용하고... 하는 방식이 좋아보이지는 않았아서 3307, 3308, 3309... 등 port 번호를 바꿔가며 여러개의 MySQL서버 container 로컬에서 띄워서 해결했었다.

잘못셋팅해도 그냥 container를 날리고 다시 이미지를 빌드하여 container로 띄우면 초기화도 쉽다. 평소에 쓰면서도 아주 강력한 기능인 것이 느껴졌는데, 과연 내가 이러한 기술에 대해서는 잘 모른다는게 느껴져서... 깊게 공부할 필요가 있다고 느껴졌던 것이다.

<br>

## Virtual Machine vs Container

그렇다면 독립된 환경을 제공할 수 있는게 오직 container 하나일까?라는 물음에는 '그렇지 않다'이다. 아직까지도 사랑받고 잘 사용되고 있는 Virtual Machine 또한 독립된 환경을 제공할 수 있는데, 이러한 점에서 Virtual Machine과 Container의 비교에 대한 이야기는 항상 가상화 관련된 글에서 나오는 것 같다.

### Virtual Machine이란?

> VM이란 컴퓨터 시스템의 가상화 또는 에뮬레이션이다. VM은 컴퓨터 구조에 기반하고 컴퓨터의 물리적인 기능들을 제공한다. 따라서 특수한 하드웨어, 소프트웨어 또는 2가지의 조합으로 구성된 케이스로 구현되기도 한다. - [출처 - wikipedis](https://en.wikipedia.org/wiki/Virtual_machine)

container와 다른 부분이 있다면, VM은 '하드웨어' 레벨까지의 독립된 환경을 구성할 수 있다는 점이다. 따라서 `Hypervisor`라는 하드웨어 에뮬레이터의 유무가 container와 VM을 구분짓는 가장 큰 요인이라 할 수 있을 것이다.

![container_vs_vms](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/study/img/container_vs_vms.png?raw=True){: width="65%" height="80%"}

<br>

## 번외) 어떤 상황에서 무엇을 사용하면 좋을지?

redhat에서 가져온 내용이다.

### container를 사용하면 좋을 상황

- cloud-native app을 빌드할 때
  - [cloud-native app이란?](https://aws.amazon.com/ko/what-is/cloud-native/)
- microservice를 패키징 할 때
- 어플리케이션을 Devops 또는 CI/CD에 통합시킬 때
- 다른 확장가능한 IT프로젝트로 이동할 때

### Virtual Machine을 사용하면 좋을 상황

- 기존의 온프레미스, 레거시, 모놀리식 workload들이 존재할 때
- 개발 주기에서 위험도가 있는 부분들을 격리하고 싶을 때
- 네트워크, 서버, 데이터와 같은 인프라 자원들을 provisioning할 때
  - [provisioning 이란?](https://www.redhat.com/en/topics/automation/what-is-provisioning) : infra 구조를 설정하여 생성해주는 하나의 프로세스
- 다른 OS에서 또 다른 OS를 실행하고 싶을 때

<br>

## Container를 사용하면 뭐가 좋을까?

아까 개인적인 경험을 통하여, '독립적인 환경'의 필요성에 대해 언급했는데, 그렇다면 그 외의 장점에는 뭐가 있을지 찾아보자. - [출처 - google cloud](https://cloud.google.com/learn/what-are-containers)

<br>

### 책임의 분리

개발자는 어플리케이션 의존성 관리에만 집중이 가능하고, Devops와 같은 팀들은 소프트웨어 버전이나 배포관리 등에 온전히 집중할 수 있도록 해준다.

### workload의 protablilty

container는 리눅스, 윈도우, Mac, 가상 시스템 등 어디서든 실행할 수 있다.

### 어플리케이션 분리

container는 OS level에서 CPU, disk, network 자원들을 가상화해주고, 다른 어플리케이션들과 논리적으로 격리된 OS를 개발자에게 보여준다.

### VM에 비해 빠른 실행

container는 OS자체를 실행시킬 필요가 없어서, VM보다 더 가볍고 빠르게 실행될 수 있다.

<br>

## Linux Container 역사

나는 container == docker 로만 이해하고 있었다. 그러나 좀 더 공부하게 되면서 생각보다 container의 역사는 오래됐음을 알게 됐는데, 짧게 요약해보도록 하겠다.

[해당 글](http://www.opennaru.com/openshift/container/history-of-the-container/)에 설명이 상세하게 잘 되어 있으니 참고하자.

<br>

### 1979년 `UNIX V7`

`chroot` system call이 소개됐던 시기. 각 프로세스별 파일 접근을 분리함으로써 Process 격리의 시작이 됨.

- chroot : 하나의 filesystem에서 프로세스와 그 자식 프로세스의 root directory를 바꿀 수 있도록 해주는 system call
- chroot는 1982년에 `BSD` 에도 추가됨

### 2000년 `FreeBSD Jails`

- FreeBSD(Free Berkeley Software Distribution) : BSD UNIX로 알려진 캘리포니아 버클리 대학교에서 만들어진 오픈소스 운영체제. kernel만 개발하는 linux와는 다르게 windows, MacOS처럼 응용 소프트웨어도 포함하여 개발 및 배포를 진행한다.
- `FreeBSD` 시스템의 보안을 강화하기 위해 사용된 툴이 바로 `FreeBSD Jail` 이다. 
- `Jail` 이라고 불리는 공간에 프로세스를 격리한다. 그리고 이는 한 프로세스들의 집합의 root directory를 바꾸기 위해 사용되는 `chroot` 개념에서 만들어졌다. 그래서 당연하게도 격리된 밖의 다른 files이나 자원들에 접근이 불가능하기 때문에, 안정적인 환경을 구성할 수 있는 것이다.

현재 포스팅에서는 `FreeBSD Jail` 에 대해서만 다룰 것이 아니기에, [해당 문서](https://docs.freebsd.org/en/books/handbook/jails/)를 참고하여 자세한 부분들은 나중에 확인해보자.

### 2001년 Linux VServer

`FreeBSD Jail`과 마찬가지로 컴퓨터 시스템에서 file systems, network addresses, memory과 같은 자원들을 구분해줄 수 있는 `Jail` 매커니즘이다. 

### 2004년 Solaris Container

- OS level의 가상화 기술을 제공
- Solaris OS 에서는 기본적으로 `Solaris container`에 대한 manual page를 제공
- `Zone` 이라는 것을 통해 하나의 OS 시스템 내에서 완벽히 분리된 가상의 서버들처럼 동작

### 2005년 Open VZ (Open Virtuzzo)

- OS level의 가상화 기술을 제공
- 수정된 linux kernel로 가상화, 격리, 자원 관리, checkpointing 기술을 제공

### 2006년 Process Containers

- 프로세스들의 자원사용(CPU, memory, Disk I/O, 네트워크 등)을 제한, 분배, 격리하기 위해 고안됨
- 1년 후 **`Control Groups (CGroups)` 라는 이름으로 변경**되었고, 이는 linux kernel 2.6.24에 합쳐짐
  - 향후 다룰 내용 매우 중요한 내용의 선조격
  - 해당 기능으로 Linux에서 가상화라는 기능을 구현할 수 있도록 해주는 것

### 2008년 LXC

- 최초의 Linux container 
- Linux kernel을 따로 수정할 필요없이 `CGroups` 와 리눅스 `namespace`로 구현됨. 

### 2011년 Warden

- `LXC`를 사용하여 만들었으나 나중에는 자체적으로 구현한 것으로 대체.
- daemon처럼 실행되어 어떤 OS에서라도 환경을 격리할 수 있고, Container 관리를 위한 API를 제공.
- 전반적으로 여러 host들에 대한 container들을 관리하기 위해 client-server 모델로 개발되었다.
- `Warden`은 `CGroups`, `namespaces`, 프로세스 life cycle 을 관리하는 기능까지 갖고 있다.

### 2013년 LMCTFY

- Let Me Contain That For You
- Linux application container 기능을 제공하는 Google의 container 기술의 오픈소스 버전
- `container aware` 라는 것으로 하위 컨테이너를 생성하고 관리를 할 수 있음.
- Google이 LMCTFY 코어 개념을 Open Container Foundation의 일부인 `libcontainer`로 기여하기 시작하며 2015년에 배포가 종료됨.

### 2013년 Docker

- 초기에는 LXC 사용했으나 향후 변경됨.
- docker가 나오면서 컨테이너가 폭발적으로 인기많아짐.



그 외에도 엄청 많은데.. 일단은 여기까지 보도록 하겠다. 궁금한 경우 위에서 소개한 링크를 확인하면 된다.

일단 Docker에 대해서는 향후 깊게 다룰 것이고... 여기서 얻어가야할 부분이란 컨테이너는 역사가 나름 있었고, 어떤 식으로 발전하였는지를 확인하는 것이다.



## Next

컨테이너가 무엇이고 어떤 장점을 가지며 VM과는 어떤 차이가 있는지, 그리고 그 발전의 역사에 대해 아주 조금 살펴보았다.

다음번에는 Docker에 대해서 자세히 알아보도록 하자.



<br>

## references

- [wikipedia - container definition](https://en.wikipedia.org/wiki/Containerization_(computing))
- [atlassian - container vs VMs](https://www.atlassian.com/microservices/cloud-computing/containers-vs-vms)
- [redhat - container vs VMs](https://www.redhat.com/en/topics/containers/containers-vs-vms)
- [wikipedia - virtual machine](https://en.wikipedia.org/wiki/Virtual_machine)
- [aws - what is cloud native app?](https://aws.amazon.com/ko/what-is/cloud-native/)
- [redhat - what is provisioning?](https://www.redhat.com/en/topics/automation/what-is-provisioning)
- [google - what are containers?](https://cloud.google.com/learn/what-are-containers)
- [aquasec - linux container histories](https://www.aquasec.com/blog/a-brief-history-of-containers-from-1970s-chroot-to-docker-2016/)
- [wikipedia - FreeBSD Jail](https://ko.wikipedia.org/wiki/FreeBSD)
- [redhat - container short histories](https://www.redhat.com/ko/topics/containers/whats-a-linux-container)
- [FreeBSD - what is FreeBSD Jail](https://docs.freebsd.org/en/books/handbook/jails/)



