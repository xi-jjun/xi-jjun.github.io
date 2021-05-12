---
layout: post
title: "네트워크 - 1"
author: "xi-jjun"
tags: Network
---

# 공부를 시작하며

비전공자로써 컴퓨터 네트워크에 대해 공부한 것들을 정리하기 위해 시작하였다. 

IP, netmask, LAN, WAN도 뭔지 모르는 심각한 상태이기에 공부를 시작했다.

### 	참고자료

​		KOCW 이석복 교수님 2015-2학기 강의, (갓)WIKIPEDIA

​		네트워크란? 출처 : https://hannut91.github.io/blogs/network/network



# 네트워크

### 	네트워크란? 

그 중에서도 내가 공부할 computer network 란 "net+work의 합성어로써..." 이런식으로 설명되어 있었다. 

공부를 처음 접해본 나에겐 와닿지 않는 말들이어서 계속 검색을 해본결과,

	"무언가와 무언가가 무언가에 연결되어 있는 것"

여기서 무언가와 무언가는 Node라고 부르고, 이는 Link를 통해 연결되어 있다. 

비유해서 설명하자면 "전화"는 "전화기"와 "전화기"가 "전선"으로 연결되어 있다. 여기서 전화기는 Node이고 전선은 link 라고 생각하면 어떤 느낌인지는 알 수 있었다. 

computer network 에서는 컴퓨터와 컴퓨터가 router에 의해 통신을 주고 받는다.



### 네트워크 구성

그렇다면 네트워크는 무엇으로 이뤄져 있을까? 아까 말한 컴퓨터와 Router??

<img src="https://github.com/xi-jjun/xi-jjun.github.io/tree/master/_posts/network/img/network-all.png" alt="network-all" style="zoom:67%;" />

네트워크는 위 사진처럼 core(내부)에는 Router가, edge에는 Host(우리 컴퓨터들)이 있다.

#### 	정리

​		Network edge : Host(우리 desktop, laptop)들이 위치. wep application이 돌아가는 곳.

​			=> 우리의 컴퓨터나 서버가 있다고 생각하면 된다.



##### 				Network edge 구성요소

​				Client : 본인(?)이 원할 때 link에 연결해서 서버로부터 정보를 가져오는 구성요소.

​					=> 그냥 "서버에 요청하는 컴퓨터(우리)" 라고 생각하니깐 받아드려졌다.

​				Server : client의 요청을 기다리는 구성요소. 

​					=> ex) 네x버 홈페이지에서 내가 "어떠한 행동(클릭 등등)"도 하지 않는다면 아무일도 안일어난다.

<hr>

​		Network core : router가 위치.

​			Q1) router가 도대체 무엇인가??

​				A1) 네트워크 중심부에서 Host(우리)가 보내는 메세지를 받아서 목적지까지 전달해주는 역할.



### 네트워크 기능

Network edge