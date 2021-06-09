---
layout: post
title: "네트워크 - 3"
author: "xi-jjun"
tags: Network

---

# KOCW 3강

KOCW 네트워크 강의(이석복 교수님)를 듣고 정리하여 쓴 글이다.

<br>

## 지난 주 복습

>Socket은 process가 데이터를 보내거나 받기 위한 창구 역할을 한다.

>client와 server는 request와 response의 반복으로 정보를 주고 받는다.

> DNS는 도메인 이름을 실제 해당 public IP로 변환해주는 역할을 한다.

<br>

## Socket

Client process와 Server process 사이의 통신. 둘 사이의 통신을 위한 API가 socket인 것이다.

Application layer에는 많은 process가 있다.

* process : 컴퓨터에서 연속적으로 실행되고 있는 컴퓨터 program. program의 코드와 정적인 데이터가 메인 메모리에 load되면 process가 되는 것이다.
* program : HDD에 저장되어 있는 실행코드. 실행되기만을 기다리는 코드와 정적인 데이터의 묶음.

<br>

### Socket이란 무엇일까?

> Socket : network 상에서 동작하는 프로그램 간 통신의 종착점(End point).

End point : IP주소와 Port번호의 조합. 최종목적지.

socket은 데이터를 통신할 수 있도록 해주는 연결부 역할이다. => IP주소와 port번호를 갖고 있는 Interface.

두 프로그램 모두에 socket이 생성돼야 한다.











