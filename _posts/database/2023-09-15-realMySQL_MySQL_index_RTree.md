---
layout: post
title: "MySQL Index R-tree"
author: "xi-jjun"
tags: Database
---

# MySQL Index R-tree

- 2차원의 데이터를 인덱싱하고 검색하기 위한 목적
  - 좌표 시스템에 기반을 둔 정보에 대해서는 모두 적용 가능
- 위치 기반 서비스의 구현을 위해 MySQL의 Spatial Extension을 이용할 때 사용하는 인덱스
- `WGS84`(GPS) 기준의 위도, 경도 좌표 저장에 주로 사용

<br>

## 구조 및 특성

MySQL에서는 공간 정보의 저장 및 검색을 위해 아래 데이터 타입을 제공.

- `POINT`
- `LINE`
- `POLYGON`
- `GEOMETRY` → super type (모든 객체 저장 가능)

<br>

### MBR (Minimum Bounding Rectangle)

> 도형을 감싸는 최소크기의 사각형

MBR의 포함관계를 B-Tree 형태로 구현한 인덱스가 `R-Tree` 인덱스인 것.

<br>

## 사용

`ST_Contains()`, `ST_Within()` 과 같은 포함관계를 비교하는 함수로 검색해야만 인덱스를 사용할 수 있다. 

<br>

## 느낀점

공간에 관한 인덱스도 있구나...

