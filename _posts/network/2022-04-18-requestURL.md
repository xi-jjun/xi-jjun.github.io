---
layout: post
title: "What happening when enter the 'www.google.com'"
author: "xi-jjun"
tags: Network

---

# What happening when enter the 'www.google.com'

자 이번에는 만약 '구글에 접속할 때 네트워크에서는 어떤일이 일어나는가?'에 대해 알아보도록 할 것이다.

> 상황 : www.google.com 을 브라우저에 입력하고 엔터를 누른 상황!

<br>

## Application Layer

먼저 사용자의 요청은 브라우저라는 application process를 통해서 google서버에 전달될 것이다. 요청 message는 `Transport layer`로 보내질 것이다.

그리고 구글의 IP를 알아야 하기에 DNS Server에서 실제 IP address를 가져온다. 실제 IP를 구해야 하는데, 구하는 곳이 바로 `DNS(Domain Name Server)`이다.

## DNS(Domain Name Server)

> host의 domain이름을 host의 네트워크 IP주소로 바꾸거나 그 반대의 변환을 수행하기 위해 개발됨.

인터넷 보급이 미미하던 시절에는 특정 컴퓨터 시스템이 전체 host의 정보를 모두 관리했다고 한다. 따라서 domain name과 ip address의 매핑 정보 모두를 관리한 것이다. Unix system에서는 다음과 같은 예시처럼 `/etc/hosts` 파일과 같은 특정 파일에 모든 host들의 정보를 관리했다고 한다.

```text
domain1.korea.co.kr 231.13.245.9
domain2.korea.co.kr 231.130.201.84
domain3.korea.co.kr 241.93.175.39
domain4.korea.co.kr 235.53.43.99
```

마침 Mac OS를 사용했기에 `/etc/hosts`file을 cat명령어로 조회해봤다.

```sh
$ cat /etc/hosts
##
# Host Database
#
# localhost is used to configure the loopback interface
# when the system is booting.  Do not change this entry.
##
127.0.0.1	localhost
255.255.255.255	broadcasthost
::1             localhost
# Added by Docker Desktop
# To allow the same kube context to work on the host and the container:
127.0.0.1 kubernetes.docker.internal
# End of section
```

우리가 맨날 intelliJ로 Spring 띄우고 `localhost:8080` 을 써도 됐지만, `127.0.0.1:8080`을 써도 되는 이유를 확실히 알게된 것 같다.

그러나 이런식으로 관리하는 것에는 한계가 있었다. 컴퓨터 보급이 늘어나고, mapping해야할 domain name과 ip address가 많아지자 수작업으로 관리하기가 어려워졌다. 따라서 이러한 점을 해결하기 위해서 `DNS` service가 등장한 것이다.

IP주소를 원하는 application program은 domain name을 매개변수로 하여 `Resolver`를 호출한다. `Resolver`는 UDP를 이용해 자신이 위치한 지역의 DNS name server에 변환을 요청하여 host의 IP를 얻는다. Unix system에서는 `nslookup` 라는 DNS를 이용해 주소 변환 요구를 수행하는 명령어가 있다. 다음은 `www.google.com`에 대한 IP address를 요청하는 명령어이다.

```console
~ $ nslookup www.google.com 
Server:		168.126.63.1
Address:	168.126.63.1#53

Non-authoritative answer:
Name:	www.google.com
Address: 142.251.42.196
```

- `Server` : 사용자가 로그인한 Unix system의 환경설정에서의 DNS server domain name
- `Address` : DNS server ip address

더 자세히는 나중에 DNS로 포스팅을 하도록 하겠다.

<br>

따라서 결국엔 www.google.com라면, com → google → www 순서로 domain name server에서 탐색을 하여 실제 정보를 가져오는 것이다.

이제 IP를 알았으니 TCP연결을 해줄 차례이다 

<br>

## Transport layer

아니, 이전에 `OSI 7 layers`했을 때 `presentation, session layer`들은 다 빼먹고 어디로 갔는가?? 라는 생각이 들 수 있다. 

> TCP/IP is the most widely used model as compared to the OSI model for providing communication between computers over the internet. 

Internet 통신에서는 `TCP/IP` model이 `OSI` model에 비해 더 많이 사용되기 때문에 나는 `TCP/IP` model로 설명을 할 것이다.

다시 돌아와서, goolge server와의 통신을 위해 `3-way handshake`로 client-server간 연결을 해야한다. 이렇게 socket이 연결된다.

그리고 `Application layer`로부터 온 message를 TCP segment의 `DATA`영역에 저장한다. 그리고 segment의 HEADER에는 현재 본인의 port번호, 목적지(구글)의 port번호를 기입하게 된다. 

- source port # : 9157 이라고 하자(가정)
- Destination port # : https 로 요청했기에 443번이다.

이렇게 만들어진 segment를 `Network layer`에 전달한다.

<br>

## Network Layer

network layer의 역할인, 목적지 서버 IP에 전달을 해야한다. 그러기 위해서는 목적지(현재:구글)의 IP를 알아야 하는데 아까 DNS로부터 google의 IP address를 받았기에 packet HEADER에 추가해준다.

그리고 `Data Link Layer`에 전달한다.

<br>

## Data Link Layer

네트워크 환경에서 client가 server에게 데이터를 전송하려면 server IP address뿐만 아니라 MAC address도 알아야 한다. 우리는 google server IP address를 DNS에 요청하여 알게됐다. 따라서 이제 `Data link layer`에서 google의 MAC address와 본인의 MAC address만 알게된다면 데이터를 전송할 수 있는 것이다!

Sender 의 MAC address는 자신의 LAN card에 내장되어 있어서 이 값을 읽으면 된다. Receiver의 MAC address는 어떻게 구할 수 있을까? client내부 정보로는 구할 수가 없다. 따라서 server IP address를 매개변수로 하여 `ARP`기능을 통해 Receiver의 MAC address를 얻어야한다. 

그렇게 Gateway router의 MAC 주소를 얻고, 그 다음부턴, 각각의 router의 `Network layer`에서 forwarding table과 ARP table을 참고하여 구글 server까지 보내게 된다.

<br>

## Result

구글에서는 우리의 요청을 받게되고, source port/IP와 동일하게 destination port/IP를 설정하여 response해줌으로써 HTTP 통신이 되어 구글 홈페이지를 볼 수 있게 된다.

<br>

## Reference

[쉽게 배우는 데이터 통신과 컴퓨터 네트워크 - 책](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9791156643043)

[TCP/IP](https://www.javatpoint.com/osi-vs-tcp-ip)

[DNS](https://ko.wikipedia.org/wiki/%EB%8F%84%EB%A9%94%EC%9D%B8_%EB%84%A4%EC%9E%84_%EC%8B%9C%EC%8A%A4%ED%85%9C)

