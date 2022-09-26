---
layout: post
title: "autocorrectionType vs spellCheckingType"
author: "xi-jjun"
tags: iOS

---

# autocorrectionType vs spellCheckingType

개발하다가 뭔가 비슷해보이는 2개의 property를 만나서 정리하고자 글을 쓰게 됐다. 사실 그냥 apple document보는 습관과 방법을 익히고, 정리하기 위함이다.

<br>

## autocorrectionType

> The autocorrection style for the text object. - [apple](https://developer.apple.com/documentation/uikit/uitextinputtraits/1624453-autocorrectiontype)

text객체의 자동수정 스타일...?이라고 한다. 이거보단 설명 보면 더 자세히 알 수 있다.

>This property determines whether autocorrection is enabled or  disabled during typing. With autocorrection enabled, the text object  tracks unknown words and suggests a more suitable replacement candidate  to the user, replacing the typed text automatically unless the user  explicitly overrides the action. 

typing하고 있을 때 `autocorrectionType`이 enable되어 있다면, 텍스트 객체는 모르는 단어들을 추적하여 사용자에게 가장 적절한 단어로 대체할 것을 제안한다고 한다. 적절한 단어들은 사용자가 해당 기능을 재정의하지만 않는다면, 자동으로 모르는 단어를 대체한다.

default로는 autocorrection이 enable된다.

그러니깐 휴대폰에 뜨는 단어 추천 기능이 이에 해당한다고 볼 수 있을 것 같다. 실제로 아래와 같이

```swift
textField.autocorrectionType = .no
```

라는 설정을 주게 되면, `hello` 라는 단어를 `helli` 로 했을 때 키보드 위에 아무것도 뜨지 않게 된다. 

<br>

## spellCheckingType

> The spell-checking style for the text object. - [apple](https://developer.apple.com/documentation/uikit/uitextinputtraits/1624461-spellcheckingtype)

텍스트 객체에 스펠링 체크를 스타일 이라고 한다. 설명을 보도록 하자.

>This property determines whether spell-checking is enabled or  disabled during typing. With spell-checking enabled, the text object  generates red underlines for all misspelled words. If the user taps on a misspelled word, the text object presents the user with a list of  possible corrections. 

typing하는 동안 스펠링 체크를 해줄지 정해주는 속성이라고 한다. enable된다면, 틀린 단어 밑에 빨간선으로 표시를 해준다고 한다. 빨간줄의 단어를 클릭하면 사용자에게 가능한한 교정된 단어를 보여준다.

>The default value for this property is `UITextSpellCheckingType.default`, which enables spell-checking when autocorrection is also enabled. ...

기본값(default)으로는 **`autocorrectionType`이 enable되어 있을 때만 spell-checking도 enable** 된다. 

<br>

## 실제 사용

```swift
// textField.autocorrectionType = .no // 자동으로 틀린글자 잡아줄 것인지? => default 사용
textField.spellCheckingType = .no // 스펠링체크 할 것인지? => 스펠링 체크 비활성화
```

스펠링 체크를 끄고, `autocorrectionType = .default`로 했을 때에는 틀린 단어에 빨간줄은 안뜨고 해당 단어에 대한 자동완성+추천단어 기능이 키보드 위에 활성화가 된다.

<br>

```swift
textField.autocorrectionType = .no // 자동으로 틀린글자 잡아줄 것인지? => 비활성화
// textField.spellCheckingType = .no // 스펠링체크 할 것인지? => 스펠링 체크 default
```

키보드 위에 다른 UI가 생성되지 않는다. 마찬가지로 틀린 단어 밑에 빨간줄도 안뜬다.

<br>

```swift
textField.autocorrectionType = .no // 자동으로 틀린글자 잡아줄 것인지? => 비활성화
textField.spellCheckingType = .yes // 스펠링체크 할 것인지? => 스펠링 체크 default
```

키보드 위에 다른 UI가 생성되지 않는다. 그러나 틀린 단어에 빨간줄은 뜬다. 틀린 단어를 탭 했을 때에 가능한한 교정된 단어들이 추천된다.

<br>

## Reference

[what is autocorrectionType](https://developer.apple.com/documentation/uikit/uitextinputtraits/1624453-autocorrectiontype)

[what is spellCheckingType](https://developer.apple.com/documentation/uikit/uitextinputtraits/1624461-spellcheckingtype)


