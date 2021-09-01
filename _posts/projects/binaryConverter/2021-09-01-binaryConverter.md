---
layout: post
title: "진법 변환기를 만들어보자"
author: "xi-jjun"
tags: binary-converter

---

# 진법 변환기를 만들어보자!!

요즘 그냥 막연하게 뭔가를 만들어보고 싶은데 실력이... 부족했다. 심심하던 와중에 얼마전에 생활코딩에서 google extension을 만드는 방법을 봤었는데 재밌어 보여서 평소 "있으면 좋지만 굳이 만들어야할까..?" 라는 것을 만들려고 했다. JS+HTML+CSS 연습한다는 느낌으로 간단한 2, 10, 16진수 변환기를 만들어봤다.

<br>

## 만들게된 배경

첫번째 이유로는 전자전기공학부는 이진수와 10진수, 16진수 변환이 시험에 나올 때가 아주아주 잠깐 있다. 그 이후로는 다시 볼 일이 없을 경우가 많지만, 아주아주 잠깐을 위해 진법 변환기를 만들어보려고 한다.

2번째 이유로는 '내가 어떤 가치를 만들 수 있는지' 알아보기 위해서이다. 얼마전에 EO라는 유투브 채널에서 배달의 민족 CEO이신 김범준 대표님이 하신 말씀을 들었는데 나의 생각과 약간 비슷했다. 가장 인상깊었던 말씀은

> 개발자라는 사람은 코딩하는 사람이라고 생각 않았으면 좋겠다. 결국은 우리에게 주어진 비지니스 문제를 해결하는 사람으로 생각하는 것이 좋다. 만든 코드가 천줄이든 만줄이든 중요한건 그렇게 작성한 코드로 **만들어낸 비지니스적인 가치가 무엇**이냐 이다.

나는 항상 개발자라는 직업을 보면서 '코딩보단 비지니스적인 이익을 이뤄낼 아이디어' 가 중요하다고 생각했다. 물론 위의 말과 비슷하지 않다고 생각할 수 있고, 코딩이 안중요하다는 것이 아니다. 그저 좋은 아이디어가 없다면 그 코딩실력을 쓸 곳이 없을 수도 있지 않을까라는 생각이 들었을 뿐이다.

마지막 이유로는, 지금의 나는 코딩실력이 걸음마 단계다. 하지만 만들고 싶은 아이디어가 있다. 따라서 조잡한 코드가 되겠지만 나의 아이디어를 실현시키고 싶고, 어떤 비지니스적인 가치를 지닐지 생각을 해보고 싶어서 만들게 됐다. 사실 진짜 만들고 싶은 google extension은 처음하는 나에겐 구현이 좀 힘들 것 같아서 이번에는 상대적으로 구현하기 쉬운 진법 변환기를 만들며 연습해보고 다음번에 구현하려고 한다.

<br>

# 기획

기획이라고 말하기도 힘들 정도로 간단하다. 

![bc1](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/projects/binaryConverter/img/bc1.png?raw=True){: width="75%"}

`Select-box`에는 2, 10, 16진수를 고를 수 있게 할 것이다. 그리고 아래 `text-box` 는 사용자가 변환하고 싶은 값을 입력하는 곳이다. 그리고 그 아래에 결과가 나오게 할 것이다. `select-box`는 결국 사용자가 입력할 값이 어떤 type인지를 나타내는 것이다.

<br>

## manifest.json

>manifest.json 파일은 json 포맷 파일로서, 모든 웹 익스텐션이 포함하고 있어야 하는 파일입니다. manifest.json을 사용함으로써, 당신은 당신의 익스텐션의 이름, 버젼과 같은 기본 정보를 명시하며, 또한 당신의 익스텐션의 기능, 예를 들어 기본 스크립트, 내용 스크립트, 브라우져 활동 등과 같은 측면을 명시합니다. - [출처](https://developer.mozilla.org/ko/docs/Mozilla/Add-ons/WebExtensions/manifest.json)

```json
{
    "name": "BINARY Converter",
    "version": "1.0",
    "description": "CONVERTER",
    "permissions": [
        "activeTab",
        "storage"
    ],
    "browser_action": {
        "default_popup": "popup.html"
    },

    "manifest_version": 2
}
```

Google extension을 만들기 위해 내가 만들 app의 정보를 적어주었다.

<br>

## popup.html

이번에 그냥 bootstrap 한번 써보고 싶어서 `select-box, text-box`에 적용해봤다.

![bc2](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/projects/binaryConverter/img/bc2.png?raw=True){: width="75%"}

간단한 app이어서 그런지 그렇게 볼건 없는 것 같다... 얼마전에 CSS에서 `grid` 를 알게 됐어서 그렇게 배치를 한 모습이다.

<br>

## 결과

![bc4](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/projects/binaryConverter/img/bc4.png?raw=True){: width="80%"}

위와 같이 결과가 잘 나오는 것을 볼 수 있다.

<br>

## 느낀점

JS 연습하는 느낌으로 해봤는데 그정도로 딱 적당했던 것 같다. 자세한 소스는 [여기](https://github.com/xi-jjun/Binary_Converter)에 있다. 혹시나 궁금하신 분이 있다면... 들러서 가져가시면 됩니다...

다음에는 좀 더 의미있는 것을 만들어보려고 한다. 이렇게 간단한거 하나 만들어보니깐 머릿속에 어떻게 구현할지 감이 대충 오는게 좋았던 것 같다.
