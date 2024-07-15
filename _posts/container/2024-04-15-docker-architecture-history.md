---
layout: post
title: "Docker Architecture History"
author: "xi-jjun"
tags: Study Container Linux Docker


---

# Docker Architecture History

## Overview

[이전 포스팅](https://xi-jjun.github.io/2024-03-26/why-docker)에서 Docker가 기존 컨테이너 기술들에 비해 어떤 점들이 더 좋은지, 어떻게 사실상 컨테이너 기술의 표준이 됐는지를 알 수 있었다. 이번에는 Docker의 architecture가 어떻게 변화했는지에 대해 알아보며 현재의 구조가 탄생한 배경에 대해 기술적으로 이해해볼 것이다.

<br>

## 1. 초기 Docker Architecture (~v0.8)

Docker가 2013년도에 처음 나왔을 당시, docker engine은 2개의 주요 컴포넌트(`dockerd`, `LXC`)로 이뤄진 monolithic 아키텍처였다.

```shell
// Docker 초기 architecture (~v0.8)
+-------------------------------------+
|        Docker Daemon(dockerd)       |
|-------------------------------------|
| - Docker Client                     |
| - Docker API                        |
| - Container Runtime                 |
| - Image Build                       |
| - etc ...                           |
+-------------------------------------+
                 |
                 v
+-------------------------------------+
|       LXC(Execution Driver)         |
|-------------------------------------|
| - Provide fundamental container     |
|   build-blocks to docker daemon     |
+-------------------------------------+
                 |
                 v
+-------------------------------------+
|     Linux Kernel(Host Kernel)       |
|-------------------------------------|
| - Namespaces (Isolation)            |
| - Cgroups (Resource Management)     |
| - Capability (Process Permission)   |
| - etc ...                           |
+-------------------------------------+
```

(제가 직접 한땀한땀 그린 것이니.. 막 가져가지 말아주세요...)

- Docker daemon : 지금의 client, server 가 모두 daemon에 존재
- LXC : docker driver. 초기에는 docker가 리눅스 kernel에 직접 접근하는 것이 아닌, LXC를 통해서 kernel을 조작했다.
- Linux kernel : 실제 컨테이너가 생성되는 host kernel

<br>

### 1.1) 현 구조의 한계

위 구조가 초기에는 딱히 문제가 없었지만, 발전시켜나가는 과정에서 아래와 같은 애로사항이 존재했다. 특히 `LXC` 에 대한 내용인데,

1. 다른 플랫폼(운영체제)으로의 확장에 대한 어려움 (`LXC`는 리눅스 위에서만 실행됨)
2. Docker core 기술이 특정 소프트웨어(`LXC`)에 의존적이라는 점
3. `LXC`의 성능 이슈 (Docker를 위해 설계된 소프트웨어가 아닌 점에서 나오는 최적화의 한계)

Docker를 발전시켜감에 있어서 특정 소프트웨어에 의존적인 부분은 매우 부정적이었기에 `LXC`에서 벗어날 필요가 있었다.

<br>

## 2. `libcontainer`의 등장 (v0.9)

`LXC`에 종속되지 않기 위해 2014년 3월, Docker는 `libcontainer`라는 linux kernel에 접근할 수 있는 라이브러리를 개발했다. 이를 통해서 Docker는 host linux kernel에 `libcontainer`를 통한 직접 접근이 가능해졌다.

이로써 기존의 `LXC`를 더 이상 사용할 필요가 없어졌고, 이에 `libcontainer`가 Docker의 기본 execution driver가 됨에 따라 아래와 같이 아키텍처가 변화하였다.

```shell
// Docker v0.9 architecture
+---------------------------------------------------------------------------------+
|                          Docker Daemon(dockerd)                                 |
|---------------------------------------------------------------------------------|
| - Docker Client                                                                 |
| - Docker API                                                                    |
| - Container Runtime                                                             |
| - Image Build                                                                   |
| - etc ...                                                                       |
+---------------------------------------------------------------------------------+
                                        |
                                        v
+---------------------------------------------------------------------------------+
|                       Execution Driver API (interface)                          |
+---------------------------------------------------------------------------------+
                 |                                           |
                 | (default driver)                          | (optional driver)
                 v                                           v
+-------------------------------------+     +-------------------------------------+
|   libcontainer(Execution Driver)    |     |        LXC(Execution Driver)        |
|-------------------------------------|     |-------------------------------------|
| - default execution driver          |     | - Provide fundamental container     |
|   at docker v0.9                    |     |   build-blocks to docker daemon     |
| - access directly to host linux     |     | - Optional execution driver at      |
|   kernel (Go lang library)          |     |   docker v0.9                       |
+-------------------------------------+     +-------------------------------------+
                 |                                           |
                 v                                           v
+---------------------------------------------------------------------------------+
|                          Linux Kernel(Host Kernel)                              |
|---------------------------------------------------------------------------------|
| - Namespaces (Isolation)                                                        |
| - Cgroups (Resource Management)                                                 |
| - Capability (Process Permission)                                               |
| - etc ...                                                                       |
+---------------------------------------------------------------------------------+
```

(제가 직접 한땀한땀 그린 것이니.. 막 가져가지 말아주세요...)

- 기존에는 LXC driver만 있었는데, 이 시점을 기점으로 execution driver를 선택할 수 있도록 해야 했기에, driver interface가 구현됐다.
  - default driver : `libcontainer`

<br>

### 2.1) `libcontainer`가 뭔데요..?

- LXC를 사용할 때의 문제점인 '특정 플랫폼에 대한 종속성'으로부터 자유로워지기 위해 docker.io에서 개발
- linux의 `namespace`, `cgroups`, `capabilities`, `filesystem` 을 제어하여 컨테이너를 생성하기 위한 Go 라이브러리
- `native`라는 이름의 docker execution driver

#### 2.1.1) 엥 갑자기 무슨 `native`...?

이유는 알 수 없으나 driver name이 실제로 `native`가 맞다... [moby github repo](https://github.com/moby/moby)에 있는 코드 이력을 뒤져서... 0.9 버전 커밋 기준으로 확인해봤다.

![docker09_exec_driver_in_code](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/container/img/docker09_exec_driver_in_code.png?raw=True){: width="80%" }

<sub>`moby/execdriver/native/driver.go`</sub>

![changelog_v09](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/container/img/changelog_v09.png?raw=True){: width="80%" }

<sub>`moby/CHANGELOG.md`</sub>

코드를 면밀히 분석하지는 못했고 단순하게 남아있던 흔적... 정도만 확인해봤다.

<br>

### 2.2) 그래서 뭐가 좋아졌나요?

1. Docker에 최적화된 kernel 접근
2. 특정 소프트웨어에 대한 불필요한 의존성 제거
3. driver에 대한 인터페이스화로 인한 특정 driver에 종속되지 않는 구조로의 발전

아무래도 `LXC`는 Docker만을 위해 개발된게 아니다보니, 최적화 측면에서 그다지 좋지 않았을 것이다. 그리고 `LXC`의 특정 버전 또는 관련된 의존성들에 영향을 받을 수 밖에 없었을 것이기에 0.9버전에서 `libcontainer`의 도입은 상당히 긍정적이라고 볼 수 있겠다.

<br>

### 2.3) 그래도 존재하는 한계점...

- 개발할수록 점점 더 커지는 docker daemon
  - 더 많은 책임을 지게 되고
  - 이로 인해 성능도 떨어지게 됨

이와 같은 문제를 어떻게 해결하는지 보도록 하자.

<br>

## 3. Client-Server 구조로의 변경 (v1.11~)

![docker_v1_11_0_changelog](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/container/img/docker_v1_11_0_changelog.png?raw=True){: width="80%" }

<sub>2016년도 docker 1.11.0 버전의 `CHANGELOG.md`</sub>

2016년도에 Docker에 있어서 큰 변화가 찾아왔다. 바로 docker daemon를 여러 컴포넌트로 쪼개어 monolithic 구조에서 탈출한 것이다. 이로써 Docker daemon에 있던 container execution, container runtime들은 모두 컴포넌트화되어 분리된 구조를 가지게 됐다.

```shell
// Docker v1.11.0 architecture
+---------------------------------------------------------------------------------+
|                             Docker Client (CLI)                                 |
|---------------------------------------------------------------------------------|
| - docker build command                                                          |
| - docker image pull command                                                     |
| - etc CLI command ...                                                           |
+---------------------------------------------------------------------------------+
                                        |
                                        v
+---------------------------------------------------------------------------------+
|                          Docker Daemon(dockerd)                                 |
|---------------------------------------------------------------------------------|
| - Docker API                                                                    |
| - etc ...                                                                       |
+---------------------------------------------------------------------------------+
                                        |
                                        v
+---------------------------------------------------------------------------------+
|                                  containerd                                     |
|---------------------------------------------------------------------------------|
| - Manage container lifecycle                                                    |
| - etc ...                                                                       |
+---------------------------------------------------------------------------------+
                       /                 |                \
                     /                   v                  \
+-------------------+          +-------------------+          +-------------------+
|  containerd-shim  |          |  containerd-shim  |          |  containerd-shim  |
+-------------------+          +-------------------+          +-------------------+
          |                              |                              |
          v                              v                              v
+-------------------+          +-------------------+          +-------------------+
|        runc       |          |        runc       |          |        runc       |
+-------------------+          +-------------------+          +-------------------+
          |                              |                              |
          v                              v                              v
+-------------------+          +-------------------+          +-------------------+
|     container     |          |     container     |          |     container     |
+-------------------+          +-------------------+          +-------------------+
```

한눈에 봐도 많이 바뀐게 보일 정도다. 위 사진의 `CHANGELOG.md` 에서도 확인할 수 있듯이 기존 docker daemon의 기능을 컴포넌트화하여 나누게 됐고, 이에 위와 같은 아키텍처를 가지게 된 것이다.

- Docker Client : docker를 사용하기 위해 유저와 상호작용하는 컴포넌트
- Docker daemon : client의 Docker API 요청을 처리. 실제 컨테이너 생성과 같은 동작은 `containerd`에게 맡긴다.
- containerd : 컨테이너 생명주기 관리가 최초 containerd의 탄생 목적이었으나, docker image pulling과 같은 부가적인 기능도 추가됨. 마찬가지로 실제 컨테이너 생성과 같은 동작은 `runc`에게 맡긴다.
- runc : 실제 컨테이너를 생성할 수 있는 기능을 제공하는 구현체
- container-shim : runc가 컨테이너 실행 시 새로운 runc 인스턴스를 fork하고, 컨테이너가 실행되면 parent runc process가 종료된다. 이 때 `container-shim`이 컨테이너의 새로운 parent process가 되어 컨테이너를 관리한다.

<br>

### 3.1) 뭐가 많이 바뀌었는데, 좋아진게 있나요..?

- 그 동안 daemon에게 집중되던 책임을 컴포넌트로 분리하면서, 개발 용이성 및 성능이 좋아졌다고 한다.
- 기존에는 불가능했던 컨테이너의 병렬 실행이 가능해지면서, 여러개의 컨테이너를 빠르게 실행 가능해졌다. ([Docker피셜](https://www.docker.com/blog/docker-containerd-integration/))

이 어려운 리팩터링 작업을 한 docker 팀이 새삼 대단하다고 느껴진다...

<br>

## 4. 현재의 Docker Architecture

자, 이제 현재 [docker 공식 홈페이지](https://docs.docker.com/guides/docker-overview/#docker-architecture)에서 제공하는 이미지를 보도록 하자.

![docker-architecture](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/container/img/docker-architecture.webp?raw=True){: width="90%" }

그림의 Docker Host가 바로 위에서 알아본 docker daemon, containerd, runc... 등등을 포함한 요소로 보인다.

<br>

## 마치며... 정리하며 느낀점

대략 Docker공부를 시작할 즈음, 많은 자료들을 찾고 있었는데, 당시

> 처음 : Docker daemon에서 컨테이너 만들어주는거구나~
>
> 조금 더 읽고 : 어? `containerd`? 아 얘가 해주는거구나~
>
> 마저 읽고 : 어..? `runc`...? 왜 굳이...?
>
> 추가 검색을 하며 : `OCI`..? 

이런식으로 내가 모르는 내용이 너무 많았고, 이를 정리하기가 매우 힘들 것이라는 것이 본능적으로 느껴졌다. 

개인적으로 기술의 역사를 알아야 지금 왜 이렇게 발전했는지를 알 수 있다고 생각하기에, 시간이 걸리고 좀 더 힘들더라도 성장하는 시간이 되었다고 생각한다. (~~그리고 그저 그냥 좋아서 블로그글 복붙해서 의미없는 글 싸지르는 블로그 볼 때 마다 화가 나서 열심히 적은 것도 있다.~~)

<br>

## Next

뭔가 작별할 것 같은 뉘앙스로 적었으나, 이걸로 컨테이너에 대한 얘기를 끝낸다는 아니고, 역사를 알았으니 가장 중요한 그 기술을 공부해야 한다. 그리고 알아야할게 아직 산더미 같이 남았다는....

containerd와 runc와 같이 인터페이스 느낌의 컴포넌트가 나타난 이유와 OCI에 대해서 다음에 알아보도록 할 것이고, 이게 끝난 후 본격적으로 기술에 대한 공부를 시작할 예정이다.

참고로 `References`에 남긴 링크들은 한번씩 들어가서 확인하면 좋을 듯 하니 혹시라도 나의 부족한 글이 참조링크들에 의한 것이 아님을 알아주길 바란다. (내가 못 쓴 것이다.)

<br>

## References

- [Docker architecture - docker.docs](https://docs.docker.com/guides/docker-overview/#docker-architecture)
- [docker deep dive - Poulton, Nigel](https://search.kyobobook.co.kr/search?keyword=Poulton%2C%20Nigel&gbCode=&target=total&chrcCode=2004037501)
  - 책은 이미 품절되었으나... [O'Reilly](https://learning.oreilly.com/library/view/docker-deep-dive/9781800565135/)라는 사이트에서 10일 무료체험을 통해 책의 내용을 읽어봤으니, 다른 분들도 참고하시면 좋을 듯 합니다...
  - 정확한 자료를 확인할 수 있도록 도와준 [블로그 글(ENFJ.dev - tistory)](https://gngsn.tistory.com/128)

- [LXC vs libcontainer - educative](https://www.educative.io/answers/lxc-vs-libcontainer)
- [libcontainer - Github](https://github.com/opencontainers/runc/blob/main/libcontainer/README.md)
- [difference between lxc and libcontainer - stackoverflow](https://stackoverflow.com/questions/34152365/difference-between-lxc-and-libcontainer)
- [libcontainer overview - jancorg github blog](http://jancorg.github.io/blog/2015/01/03/libcontainer-overview/)
- [moby - Github](https://github.com/moby/moby)
- [Docker engine architecture under the hood - Medium](https://medium.com/@yeldos/docker-engine-architecture-under-the-hood-741512b340d5)
- [docker containerd integration - Docker](https://www.docker.com/blog/docker-containerd-integration/)
- [Docker architecture - InvolveInInnovation Youtube](https://www.youtube.com/watch?v=253o0hxwxm8)
