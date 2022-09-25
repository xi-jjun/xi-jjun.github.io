---
layout: post
title: "iOS UITextField"
author: "xi-jjun"
tags: iOS

---

# iOS UITextField

iOS UIKit에 관한 사용법을 정리하는 글입니다. 자세한 내용에 대해서는 여기서 다루지 않을 수 있습니다.

<br>

## 갑자기 iOS...?

Java spring공부만 하다보니 정신이 나갈 것 같아서 취미생활삼아 iOS를 공부하게 됐다.

<br>

## 로그인 화면과 같이 사용자에게 '입력'을 받을 수 있는 화면을 만들고 싶을 때

> 목표 : 사용자가 텍스트를 입력할 수 있게 하고, 버튼을 누르게 되면 입력했던 텍스트를 보여주는 화면 만들기

![iosuikit1_1.png](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/iOS/img/iosuikit1_1.png?raw=True){: height="70%"}

위와 같이 Storyboard에서 화면을 구성해 줬다. 그리고 결과는 아래와 같이 나왔다.

![iosuikit1_2.png](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/iOS/img/iosuikit1_2.png?raw=True){: height="75%"}

![iosuikit1_3.png](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/iOS/img/iosuikit1_3.png?raw=True){: height="75%"}

![iosuikit1_4.png](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/iOS/img/iosuikit1_4.png?raw=True){: height="75%"}

`Show!` 버튼을 누르게 되면, 키보드가 다시 내려가게 된다.

<br>

### UI Configure Code

```swift
func setUp() {
    view.backgroundColor = #colorLiteral(red: 0.9141908288, green: 0.8648947477, blue: 0.976552546, alpha: 1)

    userTextField.keyboardType = UIKeyboardType.default
    userTextField.placeholder = "뭐든 적으세요"
    userTextField.borderStyle = .roundedRect
    userTextField.backgroundColor = #colorLiteral(red: 0.9718628526, green: 0.9668141007, blue: 1, alpha: 1)

    textShowLabel.text = "여기에 적으신 내용이 보여집니다"
    textShowLabel.numberOfLines = 0 // 줄 바꿈 되게 하기 위해서

    doneBtn.setTitle("Show!", for: .normal)
    doneBtn.setTitleColor(.black, for: .normal)
    doneBtn.tintColor = #colorLiteral(red: 0.9909078479, green: 0.7395697236, blue: 0.9589526057, alpha: 1)
}
```

<br>

### 버튼 눌렀을 때 키보드 다시 내리는 코드

```swift
func textFieldWrittenCompeleted(_ textField: UITextField) -> Bool {
    userTextField.resignFirstResponder() // <= 중요
    return true
}

@IBAction func doneBtnTapped(_ sender: UIButton) {
    textShowLabel.text = userTextField.text!
    textFieldWrittenCompeleted(userTextField)
}
```

<br>

## 어떻게 가능했을까?

>resignFirstResponder() - Notifies this object that it has been asked to relinquish its status as first responder in its window. [apple](https://developer.apple.com/documentation/uikit/uiresponder/1621097-resignfirstresponder)

구글에 검색했을 때에는 `resignFirstResponder()` method를 호출하여 위와 같은 코드로 키보드를 내리라고 했다. 근데 iOS개발이 처음이다보니 어떤 원리로 동작하는지 궁금해졌다.

`first responder` 라는 상태를 현재 윈도우에서 포기하게 하는 method..인 것으로 보인다. 

<br>

## First Responder란?

>The first responder is usually the first object in a responder chain  to receive an event or action message. In most cases, the first responder is a view object that the user selects or activates with the  mouse or keyboard. 

그러니깐 `first responder`는 이벤트나 action message를 받는 responder chain에서의 첫번째 object라고 한다. 좀 더 친숙하게 설명하자면, 사용자가 마우스 또는 키보드를 선택하여 활성화 할 때 그러한 view object라고 생각하면 될 것 같다.

<br>

따라서 사용자는 버튼을 탭 하는 과정에서 해당 메서드를 호출하게 될 때, first responder로 등록되어 있는 키보드를 resign해줬기에 키보드가 내려갈 수 있었다.

<br>

## Reference

[iOS 키보드 내리는 방법](https://hyerios.tistory.com/103)

[resignFirstResponder() method](https://developer.apple.com/documentation/uikit/uiresponder/1621097-resignfirstresponder)

