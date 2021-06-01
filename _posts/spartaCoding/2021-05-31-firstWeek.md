---
layout: post
title: "Sparta Coding(왕초보시작반) - 1"
author: "xi-jjun"
tags: spartaCoding
---

# 공부를 시작하며

"한이음ICT" 라는 공모전을 준비하게 되면서 무료로 스파르타코딩클럽의 "왕초보시작 웹개발종합반 30기" 를 듣게되었다. 백엔드 개발자가 되고 싶어서 공부 중이었지만, 프론트도 어느정도 알아놔야 된다고 생각하였다. 그리고 "공개 sw 대회"에 나가기 위해 혼자서 웹개발을 할 줄 알아야 하는 상황이 올 것 같아서 신청했었다.

<br>

~~사실 숙제로 '개발일지'를 작성하라고하여 적게 되었다.~~

<br>

## 1주차 강의 다 듣고 느낀점

~~JAVA만 하고 있었는데 Javascript, HTML, CSS 를 하게 될 줄은 몰랐다.~~

Javascript같은 경우에 python이랑 비슷한 느낌을 많이 받았다. 

list, dictionary 등등이 많이 비슷했고 나머지 문법들도 유사한 부분이 많았어서 쉽게 넘어갔던 것 같다.

HTML이랑 CSS는 들어만 봤었는데, 이번에 예제를 따라해보면서 어떻게 쓰는지 대략적으로 알게 되었다.

bootstrap이라는 framework를 사용했었는데 CSS를 되게 쉽게 할 수 있어서 인상깊었다. 나중에 나갈 공모전에서 사용할 생각이다.

<br>

## Homework

![sparta1_1](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/spartaCoding/img/sparta1_1.png?raw=True)

HTML과 CSS로 만들어본 숙제이다. 

강의를 너무 대충들어서  `<span>`의 역할을 몰랐다. 그래서 "IMAC m1"과 "가격 1,690,000 / 개" 2개의 comment를 한 줄에 적느라 30분을 보냈었다. 

* 해결방법
  * 처음에는 "html 줄바꿈 방지" 라고 검색했는데 자꾸 `white-space: nowrap;` 만 나오고, 원하는 결과는 안나와서 당혹스러웠다.
  * 결국 다른 웹사이트를 랜덤으로 inspect해서 `<span>`이 줄바꿈 없이 문자를 출력하고 있음을 발견했었다.

```html
<span class="product1">IMAC m1</span>
<span class="product2"> &emsp; 가격 : 1,690,000 / 개</span>
```
`product1` `product2` 에는 `font-size`에 관한 내용이 들어가 있다.

<br>

```html
&emsp;
```

`&emsp`는 "큰 공간(space bar)"를 나타내어 준다. 한마디로 띄어쓰기 하겠다는 것이다. 

<br>

이러한 HTML format느낌은 지금 쓰고 있는 `MarkDown(.md)`이랑 느낌이 비슷해서 배우기 시작하는데 큰 어려움이 없었다.

그리고 그 이외는 모두 다 강의영상에서 직접 같이 해봤던 내용들이어서 초보자인 나에게는 딱 간단하게 도전할 수 있을만한 난이도였다.
