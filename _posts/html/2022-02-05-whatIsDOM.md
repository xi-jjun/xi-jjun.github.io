---
layout: post
title: "HTML - DOM에 대해서"
author: "xi-jjun"
tags: HTML

---

# HTML

HTML에 대해 깊이 있게 공부하기 위해 만든 tag이다.

<br>

최근에 html을 제대로 공부하기 시작했다. 그 첫번째가 바로 DOM이었다. 대충 이런게 있다고만 들었지 정확히 뭔지는 잘 모르고 있었기에 이번 기회에 정리를 해보려고 한다.

지금 보시는 이 포스팅은 [WIT블로그](https://wit.nts-corp.com/2019/02/14/5522)와 [MDN Web Docs](https://developer.mozilla.org/ko/docs/Web/API/Document_Object_Model/Introduction)의 글을 보고 정리하여 쓴 것입니다.

# What is DOM?

> **DOM(Document Object Model) : HTML, XML 문서(웹 페이지)의 프로그래밍 interface이다.** DOM은 문서의 구조화된 표현을 제공하며 프로그래밍 언어가 DOM 구조에 접근할 수 있는 방법을 제공하여 그들이 문서 구조, 스타일, 내용 등을 변경할 수 있게 돕는다. DOM 은 구조화된 nodes와 property 와 method 를 갖고 있는 objects로 문서를 표현한다. 이들은 웹 페이지를 스크립트 또는 프로그래밍 언어들에서 사용될 수 있게 연결시켜주는 역할을 담당한다.

기본적으로 페이지에 대한 API이며 프로그램이 페이지의 내용, 구조, 스타일을 읽고 조작할 수 있게 해준다. DOM은 웹 페이지의 객체지향 표현이며 JS와 같은 언어를 이용해 DOM을 수정할 수도 있다.

- web page : 일종의 문서(document)이다. document는 웹 브라우저를 통해 그 내용이 해석되어 브라우저 화면에 나타나거나 HTML 소스 자체로 나타나기도 한다.

<br>

## 웹 페이지는 어떻게 생성될까?

브라우저가 HTML을 읽은 후, style을 입히고 대화형 페이지로 만들어 뷰 포트에 표시하기 까지의 과정을 `Critical Rendering Path`라고 한다.

- `Critical Rendering Path` : 페이지의 initial paint를 위해 브라우저가 실행해야하는 sequence.

  → 사이트의 성능을 어떻게 향상 시킬 수 있는지 이해하는데 유용하다. 향후 포스팅에서 기회가 된다면 다뤄 볼 예정이다.

<br>

`Critical Rendering Path`는 크게 2단계로 나뉠 수 있다.

1. 브라우저는 읽어드린 문서를 parsing하여 최종적으로 어떤 내용을 rendering할지 결정한다. 이 과정에서 `Render Tree`가 생성된다.
2. 해당 rendering을 수행한다.

- `Render Tree` : DOM + CSSOM 가 필요하다.
  - DOM : HTML elements의 구조화된 표현
  - CSSOM : elements와 연관된 style정보의 구조화된 표현

<br>

## DOM은 어떻게 생성될까?

DOM의 개체 구조는 Node Tree로 표현된다. 아래와 같은 예시를 보면 이해가 빠르다.

```html
<html>
  <head>
    <title>Web Page Title</title>
  </head>
  <body>
    <h1>Hello World!!</h1>
    <p>I love HTML!!</p>
  </body>
</html>
```

와 같은 HTML document가 있다고 해보자. DOM은 이와 같은 HTML의 구조화된 표현이다.(아래) 

```DOM
html
∟ head
  ∟ title
    ∟ Web Page Title
∟ body
  ∟ h1
    ∟ Hello World!!
  ∟ p
    ∟ I love HTML!!
```

<br>

## DOM이 아닌 것

1. DOM은 HTML이 아니다.

   - 작성된 HTML이 유효하지 않은 상황

     DOM은 유효한 HTML의 interface이다. DOM을 생성하는 동안 **브라우저는 유효하지 않은 HTML 코드를 올바르게 교정****해준다.

     ```html
     <html>
       Hello HTML
     </html>
     ```

     HTML의 필수 규칙 사항인 `<head>`, `<body>` tag가 빠져있다. 따라서 해당 HTML code는 유효하지 않다. 그러나 생성된 DOM을 보게 되면,

     ```DOM
     html
     ∟ head
     ∟ body
       ∟ Hello HTML
     ```

     과 같이 올바른 형식으로 나타나게 된다. 따라서 `DOM은 HTML이다`라는 말은 틀렸다.

   - Javascript에 의해 DOM이 수정되는 상황

     DOM은 HTML 문서의 내용을 볼 수 있는 인터페이스 역할을 하는 동시에 동적 자원이 되어 수정될 수 있다. 그렇게 되면 DOM은 업데이트 되지만 HTML 문서의 내용은 변경시키지 않는다. 따라서 `DOM은 HTML이다`라는 말은 틀렸다.

2. DOM은 브라우저에서 보이는게 아니다.

   브라우저의 뷰 포트에 보이는 것은 `Render Tree` 로 DOM+CSSOM의 조합이다. 그렇다면 뭐가 다르냐?

   → `Render Tree`는 'rendering되는 요소들'만 관련있기에 **시각적으로 안보이는 요소는 제외**된다. 따라서 DOM은 시각적인 것에 상관없이 구조화하여 나타내고 있기에 다르다.

3. DOM는 개발도구에서 보이는게 아니다.

   CSS의 가상요소는 CSSOM과 `Render Tree`의 일부를 구성한다. 그러나 기술적으로 DOM의 일부는 아니다.

   → DOM은 오직 원본 HTML로 부터 빌드되고, 요소에 적용되는 style은 포함하지 않기 때문이다.

<br>

## 요약 정리

- DOM은 HTML문서에 대한 interface이다.
- 첫번째로 뷰 포트에 무엇을 랜더링할지 결정하기 위해 사용되고, 두번째로는 페이지의 콘텐츠 및 구조, 그리고 스타일이 JS에 의해 수정되기 위해 사용된다.
- DOM과 HTML 차이점
  - DOM은 항상 유효한 HTML형식이다.
  - DOM은 JS에 의해 수정될 수 있는 동적 모델이다.
  - DOM은 가상요소를 포함하지 않는다.
  - DOM은 보이지 않는 요소도 포함한다.

