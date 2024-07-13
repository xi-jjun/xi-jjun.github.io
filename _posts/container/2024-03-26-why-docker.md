---
layout: post
title: "Docker 좀 치나요?"
author: "xi-jjun"
tags: Study Container Linux


---

# Why Docker?

## Overview

이전 포스팅에서는 Docker의 등장까지의 역사에 대해서 알아봤는데, 그래서 왜 Docker가 컨테이너 기술의 표준이 되었는가에 대해서는 의문이다. 따라서 이번 포스팅에서는 의문을 해결해보도록 할 것이다.

<br>

## Docker가 뭐 좀 되나요...?

> Docker가 기존 컨테이너 기술들에 비해 뭐가 더 좋은가요?

라는 질문을 나에게 해보자. 답변은.... '대중성'? 개발자로서 매우 실망스러운 답변이 아닐수가 없다. (~~나는 쓰레기였다.~~) 아무튼 늦었지만 이제서라도 공부하는게 중요하다고 생각한다.

그래서 Docker가 뭐가 더 좋길래 빠르게 컨테이너 시장을 장악할 수 있었는가? 에 대해서는 아래 관점에서 얘기해볼 수 있을 것 같다.

1. 기술적 측면
2. 접근성 측면

### 1) 기술적 측면

그 동안 컨테이너 기술들은 거의 다 '특정 운영체제의 kernel에 종속'되어 만들어졌다. 바로 이 점이 docker와의 차이점을 만들지 않았나 싶다.

- LXC, FreeBSD Jails 등등... 
- Docker는 초기에 구현체로 LXC를 사용했기에, 리눅스 운영체제에 종속된다고 볼 수 있으나, '운영체제의 kernel'에 종속되도록 만들지는 않았다. (kernel에 종속된 lxc를 사용하는 것일뿐...)
- `Dockerfile`을 통한 일관된 컨테이너 환경 구성

따라서 이런 기술들이 docker가 '사실상 컨테이너의 표준'으로 자리잡기 위한 발판을 마련해줬다고 생각한다.

### 2) 접근성 측면

- 간편한 사용
- dockerhub를 통한 이미지 간편저장 및 공유
- 오픈소스 프로젝트였기에 뛰어난 접근성(?) 제공
  - 부제 : 답답하면 너희가 코드를 봐라


간편한 사용에 대해서는... 사실 lxc를 이번 포스팅을 위해 실제로 한번 간단하게 사용해본게 다여서 단순 'ubuntu22.04 컨테이너 실행에 소요되는 명령어 수'를 기준으로 말한 것이다.

```shell
# LXC : 열심히 차근차근 컨테이너 실행!
sudo lxc-create -n practice-container -t ubuntu -- -r jammy # 이미지 pull
sudo lxc-start practice-container # 컨테이너 실행
sudo lxc-attach -n practice-container # 컨테이너 내부 접속
```

LXC로는 3개의 명령어가 필요한 것을 볼 수 있다. 그러나 docker 명령어를 보면, '딸깍' 그 자체다.

```shell
# docker : '딸깍'
docker run -it ubuntu:22.04 /bin/bash 
```

기술적 측면으로도 볼 수 있겠으나, 이러한 편의성이 docker에 대한 접근성을 올려줬기에 docker가 표준으로 자리잡는데 도움을 줬다고 생각하여 접근성 측면에서 기술하였다.

<br>

### 여담) docker는 아직도 리눅스 운영체제에 종속되지 않나요?

맞는 말이다. 초기 docker는 LXC를 사용했으나, 점차 LXC에 대한 의존성을 `libcontainer` 로 대체하며 줄여나갔다. 줄여나간 이유로는

1. LXC의 버전에 종속되지 않기 위함
2. 보다 docker에 더 최적화된 linux kernel 제어로 인한 성능 향상

2가지 정도가 있는데, `libcontainer`를 사용해도 결국 'linux kernel' 기술들을 사용하고 있기에 의존적인 것은 사실이다. (현재는 `libcontainer`가 RunC에 포함되어 있음)

그래서 MacOS/WindowOS에서도 Docker를 실행하기 위해 `Docker Desktop For Mac/Window` 를 Docker에서 만들었고, 이를 설치하면 자동으로 각 운영체제에 맞는 경량 가상머신이 설치되기에 다른 운영체제에서도 사용할 수 있는 것이다.

- MacOS : [HyperKit](https://github.com/moby/hyperkit)
- Window : [WSL 2](https://docs.docker.com/desktop/wsl/)

<br>

### 결론

결론적으로 Docker는 LXC를 쉽고 간단하게 사용할 수 있도록 해주는 인터페이스의 역할을 하면서, 쉽고 일관된 컨테이너 환경을 구성할 수 있도록해줬고, open source 프로젝트라는 특징이 개발자들에 대한 접근성을 높여줬기에 현재 컨테이너 기술의 근본이 된게 아닐까 싶다.

<br>

## Next

Docker가 어떤 점이 뛰어났는지에 대해서는 알았다. 다음에는 Docker의 아키텍처에 대해 공부해보려고 한다.

<br>

## References

- [Open container 공식 사이트](https://opencontainers.org/)
- [Docker Tutorial - What is Docker & Docker Containers, Images, etc? - Youtube ](https://www.youtube.com/watch?v=pGYAg7TMmp0&list=PLkA60AVN3hh9Sh-5OENT8RsFHjwtk2Z_l&index=17)
- [Linux Containers LXC Getting Started](https://linuxcontainers.org/lxc/getting-started/)
- [The futrue of Linux Containers - Youtube](https://www.youtube.com/watch?v=wW9CAH9nSLs)
- [Docker 입문편 - 44bits](https://www.44bits.io/ko/post/easy-deploy-with-docker)
- [DOCKER 0.9: INTRODUCING EXECUTION DRIVERS AND LIBCONTAINER - devops.com](https://devops.com/docker-0-9-introducing-execution-drivers-and-libcontainer/)
