---
layout: post
title: "LostArk Calendar를 만들어보자 - 1"
author: "xi-jjun"
tags: LostArk-Calendar

---

# Lost Ark Calendar를 만들어보자!

공부하다가 머리를 식힐 겸 + JS와 HTML, CSS 연습할 겸 lost ark 라는 게임을 위한 달력을 만들어보고 싶어졌다.

<br>

## 문제 인식 배경

난 게임을 잘 하지는 않지만 주변사람들이 게임을 많이 한다. 요즘 `Lost Ark`라는 게임은 RPG 온라인 게임이고 많은 사람들이 게임을 즐기고 있다. 해당 게임에는 `숙제` 라고 표현되는.. 마치 게임에서 내주는 "과제..?" 느낌의 뭔가가 있다. 말은 `숙제`지만 사실 게임 속에서 주는 퀘스트의 묶음 정도라고 생각하면 될 것 같다. `숙제`라고 표현하는 이유는 이 퀘스트를 매일 하루에 몇번씩만 할 수 있기에 높은 레벨의 유저들은 캐릭터를 몇개씩 번갈아가며 해당 일일 퀘스트를 수행하고 그에 따른 보상을 받는다. 

"캐릭터의 수가 많아지면" 당연하게도 나의 어떤 캐릭터가 퀘스트를 수행했는지 헷갈릴 수 있을 것이다. 엑셀에 본인의 캐릭터의 수에 맞게 칸을 만들어서 직접 체크하는 주변사람들을 보면서 내가 한번 만들어보고 싶어졌다.

<br>

# 기획

아직 매우매우매우 초보지만 뭔가를 만들려고 할 때에는 제일 먼저 "어떻게" 만들 것인지 생각을 하고, 그것에 필요한 것들이 무엇인지 생각해가면서 코딩을 해야한다고 생각한다. 그래서 오늘 할 것은 기획 + 간단한 코딩 정도를 계획하고 실행했다.

<br>

## 기존의 program

기존에 `BynnArk`라는 프로그램이 있다. [해당 사이트](https://ark.bynn.kr/home)에 들어가면 진짜로 잘 만든 것을 볼 수 있었다... 

이렇게 하면 따라하는 것이 되기 때문에 난 여기에 없는 기능인, "일정표" 기능을 넣어보도록 할 것이다. 여기서 내가 말하는 일정표란, 애플 캘린더와 같은 것을 뜻하는 것이고, 이러한 일정표와 일일 퀘스트를 했는지 확인해주는 check 기능만을 구현해볼려고 한다.

<br>

## 필요한 것들

일단 먼저 당연하게도 "달력"을 만들 것이다. `Lost Ark`의 일정도 중요하지만, 현실의 일도 빠트리면 안되기에 중점은 `Lost Ark`에 두면서 그만큼이나 중요한 현실의 일정도 기록할 수 있게 할 것이다. 그러기 위해서는 달력이 필요했다.

그 다음으로는 만든 달력에 아래의 그림(mac의 달력) 처럼 세부사항을 preview할 수 있게 해줄 것이다.

![lostA1_1](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/projects/lostArkCalendar/img/lostA1_1.png?raw=True){: width="75%"}

<br>

그리고 해당 preview를 클릭하면 더 자세히 볼 수 있게 할 것이다. 

매일 하는 일일퀘스트에 대한 check 표시도 할 수 있게 해야한다. 각 표시는 추가한 캐릭터의 수만큼 되게한다.

일정표를 클릭하면 그 날에 해당하는 모든 정보들이 나타나게 할 것이다.

<br>

## Calendar 구현하기

![lostA1_2](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/projects/lostArkCalendar/img/lostA1_2.png?raw=True){: width="75%"}

맨 첫 화면을 위와 같이 하려고 한다. 디자인은 우선순위가 아니라고 생각돼서 기능을 중점적으로 하되 마지막에 집중해서 꾸며볼려고 한다. 좀 많이 비어 보이지만 코딩을 해가면서 생각나는 것이 있다면 추가하려고 했어서 큰 틀만 잡았다.

JavaScript와 HTML, CSS로 달력을 구현하려고 하는데 공부를 위해서 직접 스스로 될 때까지 만들어 보려고 한다.

<br>

먼저 달력은 년도, 해당 월, 날짜 등등을 가져와야 하는데 JS에는 `Date()`라는 class가 해당 정보를 return해줄 수 있었다. [해당 사이트](https://www.tutorialspoint.com/javascript/javascript_date_object.htm)를 참고했다.

```javascript
const dt = new Date(); // Date Object 생성.
const year = dt.getFullYear(); // 년도 반환. : 2021
const month = dt.getMonth(); // 해당 월 반환. : 7
```

`getMonth()`라는 method는 "0~11"의 값을 반환한다. 따라서 7을 return했다고 해서 7월이라는 뜻이 아니라 0부터 시작하기 때문에 `8월`임을 알 수 있다. 이제 날짜를 가져오는 방법을 알았으니 날짜라는 살을 붙일 뼈대를 만들어보자.

<br>

## index.html

첫 화면의 뼈대가 될 HTML이다. 생활코딩에서 어설프게 배운 HTML과 CSS로 뼈대를 만들어보자.

`div`로 구역을 나눠주고, 각 구역마다 `id`값을 다르게 부과했다. 그리고 CSS `grid`를 사용했다. 예전에 `python tkinter`로 코딩을 한적이 잠깐 있었는데 grid를 써서 메뉴 구성을 했었다. 그 때의 경험이 이번에 layout을 만드는데 도움이 약간은 됐다.

![lostA1_3](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/projects/lostArkCalendar/img/lostA1_3.png?raw=True){: width="75%"}

이 사진은 오늘 한 결과이다. 아직은 갈 길이 많이 먼 것 같다... 아직 좌-우 버튼을 구현 못했다. ~~이것은 내일의 나에게 맡길 것이다.~~

오늘을 기준으로 년도와 월, 일 정보를 가져와서 달력을 만든 것이다. 9월이 되면 당연히 `9/1(수)`로 시작한다. 8월은 31일까지 있지만 9월은 30일까지 있는 것도 구현이 됐다. 당연히 윤달도 구현이 됐다.

<br>

## 다음에 할 일

1. Next, before 버튼을 구현하여 이전/다음 달의 일정을 볼 수 있게 할 것
2. 일요일과 토요일의 색을 다르게 할 것
3. 오늘의 날짜는 다른 색갈로 표현하여 알 수 있게 할 것











