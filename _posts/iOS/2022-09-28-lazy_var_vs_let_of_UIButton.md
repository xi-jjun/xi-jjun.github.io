---
layout: post
title: "lazy var vs let"
author: "xi-jjun"
tags: iOS

---

# lazy var vs let

iOS 연습으로 BMI앱을 만들고 있던 도중, '뒤로가기' 버튼을 구현했으나 클릭해도 아무 반응이 없었다. 반응 없던 버튼은 다음과 같이 코드로 만들었다.

```swift
private let backBtn: UIButton = {
    let btn = UIButton(type: .custom)
    ...
    print("back button created")
    btn.addTarget(self, action: #selector(backBtnTapped), for: .touchUpInside)

    return btn
}()

override func viewDidLoad() {
    super.viewDidLoad()
    print("BMI View Controller is loaded")
    ...
}
...

@objc func backBtnTapped() {
    print("backBtn pressed")
    dismiss(animated: true)
}
```

문제를 해결하기 위해서 `print`를 삽입하여 '클릭자체'가 안되는 것인지 확인했다. 결과적으로는 클릭자체가 안되고 있었다. 그래서 다음과 같이 바꿔줬더니 정상적으로 동작하였다.

```swift
private lazy var backBtn: UIButton = {
    let btn = UIButton(type: .custom)
    ...
    print("back button created")
    btn.addTarget(self, action: #selector(backBtnTapped), for: .touchUpInside)

    return btn
}()

override func viewDidLoad() {
    super.viewDidLoad()
    print("BMI View Controller is loaded")
    ...
}
...

@objc func backBtnTapped() {
    print("backBtn pressed")
    dismiss(animated: true)
}
```

`backBtn` 을 `let`이 아닌 `lazy var`로 선언을 바꿔줬다.

<br>

## 바꿔준 근거는?

Java JPA를 공부할 때에 lazy loading에 대해서 공부를 했던 적이 있었다. 그리고 컴퓨터 구조를 공부하면서 C build과정 중 `linking`의 과정에서 각 library를 lazy loading으로 가져온다는 것 또한 공부했었다. 따라서 해당 버튼View를 **'바로 가져오는 것이 아닌', '클래스가 생성된 후에 가져오는 것'** 이 해결책이라 생각하여 위와 같이 바꾼 것이었다. 

아래는 로그 출력의 결과이다.

```console
// 위 view controller에 접근을 했을 때 뜨는 print 순서
...
back button created
BMI View Controller is loaded
...
```

`let` 으로 버튼을 선언했을 때에는 위와 같이 클래스가 생성되면서 class member로 button을 생성해버렸기 때문에 `BMIViewController`의 `viewDidLoad()`가 호출되기 전에 버튼이 만들어진 것을 볼 수 있다.

<br>

```console
// 위 view controller에 접근을 했을 때 뜨는 print 순서
...
BMI View Controller is loaded
back button created
backBtn pressed
...
```

`lazy var`로 버튼을 선언했을 때에는 `viewDidLoad()`가 먼저 호출된 다음에 버튼이 autolayout에 올라가야했다. 따라서 그 순간이 바로 '처음 사용되는 순간'이기에 버튼이 다음으로 생성되는 것을 볼  수 있다.

정상적으로 잘 생성됐기에 버튼은 잘 눌러졌다.

<br>

## Why?

왜 `lazy` 하게 가져왔던 것일까? 라는 말을 누군가가 물어본다면, 너무나 당연하게 할 말이 많다. JPA포스팅에서도 적어놨겠지만 기본적으로 '현재 필요한 것만 쓴다' 가 모토라고 생각한다. 우리가 프로그램을 실행한다는 것에 대한 뜻을 곰곰히 생각해보면 답이 나오게 된다.

프로그램을 실행한다는 것은 

> 하드디스크에 있는 우리의 프로그램을 메모리에 load해서 '실행 중인 상태'로 만들겠다는 것이다.

따라서 '메모리'가 필요한데, 컴퓨터의 자원은 무한하지 않기 때문에 우리는 '현재 필요한 것'들에 대해서만 메모리에 올려놓고 필요하다면 하드디스크에서다시 메모리로 load하여 사용하는 것이다.

<br>

## Document로 확인

일단 비슷할 것이라 추측만 했고 확인은 안한 상태이다. Swift document를 보면서 정확히 알아보도록 하자.

> A *lazy stored property* is a property whose initial value isn’t calculated until the first time it’s used. - [swift docs](https://docs.swift.org/swift-book/LanguageGuide/Properties.html)

`lazy` property는 사용되기 전까지 초기화되지 않는다. 그리고 `lazy`로 선언할 때에는 `let` 이 아닌 `var` keyword를 쓰라고 되어 있는데 당연하게도 class instance가 초기화 될 때 lazy로 선언한 변수가 사용되지 않을 수 있기 때문이다. 아래 글에 설명이 되어 있다.

> You must always declare a lazy property as a variable (with the `var` keyword), because its initial value might not be retrieved until after  instance initialization completes. Constant properties must always have a value *before* initialization completes, and therefore can’t be declared as lazy.

`let`으로 선언된 상수들은 초기화 전에 값을 가지고 있어야하기 때문이다.

<br>

## Synchronize issue...?

document를 잘 읽다가 음? 하는 문장을 보게 되어 이어서 적게 됐다.

> If a property marked with the `lazy` modifier is accessed by multiple threads simultaneously and the  property hasn’t yet been initialized, there’s no guarantee that the  property will be initialized only once.

아직 초기화되지 않은 `lazy`로 선언된 속성에 대해 **'동시에 여러개의 thread가 접근한다면'** 해당 속성이 1번만 초기화 될 것이라는 보장이 없다는 것이다. 그렇다면 `lazy` keyword로 선언된 변수에 대해서 동기화 이슈는 어떻게 처리하는지 궁금하여 더 찾아보게 됐다.

<br>

## Dispatch Queue

> An object that manages the execution of tasks serially or concurrently on your app's main thread or on a background thread.

일단... 아직 초보라서 다음에 더 다루도록 하겠다. 찾아본 바로는 Swift concurrency나 GCD를 사용하는 것 같았다.

<br>

## Thread-safe한 선언

> ... However, in the case of global *constants* (`let`) or `static let` members on structs, enums, and classes in Swift, we **can** say that they are **thread-safe**, because they are atomic *and* **immutable**. - [reference](https://www.jessesquires.com/blog/2020/07/16/swift-globals-and-static-members-are-atomic-and-lazily-computed/)





`Global let`과 struct, class, enum의 static member에 대해서는 thread-safe가 보장된다고 한다. 하지만 `let` 선언은 **초기화된 후**에만 thread-safe가 보장된다고 한다. immutable하기 때문이다.

<br>

## Conclusion

동기화를 보장하는 방법까지 찾으면 너무 오래걸릴 것 같아서 최대한 궁금한 점 위주로 찾아봤다. iOS를 시작한지 별로 안됐어도, Java나 다른 언어들이 가지는 근본이나 체계자체는 다들 비슷한 느낌을 받았다. 

힐링을 위해 하는 공부지만 꽤 재미있다는게 함정이다...

<br>

## Reference

[swift properites document](https://docs.swift.org/swift-book/LanguageGuide/Properties.html)

[swift class members's atomic and thread-safe](https://www.jessesquires.com/blog/2020/07/16/swift-globals-and-static-members-are-atomic-and-lazily-computed/)

[dispatch queue](https://developer.apple.com/documentation/dispatch/dispatchqueue)

[swift lazy var 동기화 관해서](https://brunch.co.kr/@tilltue/71)

