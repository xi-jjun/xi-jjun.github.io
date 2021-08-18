---
layout: post
title: "Shell script로 file 이름을 순차적으로 바꿔보자!"
author: "xi-jjun"
tags: Linux


---

# Tag 'Linux'

Linux를 쓰게 되면서 알게되는 정보들 + shell script + 명령어 를 정리하기 위한 태그.

<br>

## 문제 상황

공모전에 대한 코딩을 하는 와중 파일 100개의 이름을 `1.jpg, 2.jpg, 3.jpg...100.jpg`로 바꿔줘야 했다. 평소에 1~2개 정도면 당연히 terminal에서 `mv`명령어로 바로 해결했을 거지만, 이런 상황이 생긴 김에 오늘은 **무조건 shell script를 만들어서 해결**하고자 했다.

<br>

## 구체적인 상황

현재 상황은 이러하다.

1. 본인은 1~100까지의 `.jpg`확장자의 사진을 가지고자 하였다.

2. 코딩으로 만들까 하다가 귀찮아서 100개 정도는 google ppt를 사용하여 만들었다. 각 슬라이드를 [어떤 사이트](https://convertio.co/kr/ppt-jpg/)에서 `.jpg`로 변환했다. 따라서 나온 결과가 아래와 같다.

   ![shellscript1](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/linux/img/shellscript1.png?raw=True){: width="40%" height="60%"}

<br>

딱 봐도 이미지 이름의 맨 뒤에는 ppt 슬라이드 순서대로 숫자가 붙었고, 앞에는 뭐 알수없는 문자열들이 있는 것을 알 수 있다. 따라서 나는 다음과 같은 과정을 통하여 저 사진들을 내가 원하는 이름으로 바꾸고자 했다.

1. `for` loop로 100번의 아래와 같은 100번의 동작을 수행.
   1. mv "바꿀이름" "바꿔질 이름"
   2. 바꿔질 이름++ ← 1을 더한다는 의미.
2. 끝

말은 저렇게 쉽게 썼지만 shell script를 살면서 처음 작성하기에 구현하는데 좀 시간이 걸렸다. 아래는 내가 원하는 shell script를 구현하기 위해 거쳐갔던 흐름이다.

<br>

## 문제 1

그래서 `for` loop는 어떻게 만드는 것일까요?ㅎㅎ 아주 기초적인 것도 모르는 상태였기에 더 재밌게 했던 것 같다.

```sh
for i in {1..100};
do
  # my code
done
```

위 처럼 적으면 1~100까지 총 100번의 `for`loop가 동작함을 알게 됐다. `python` code와 비슷한 점이 있어서 좀 더 쉽게 와닿았다.

<br>

## 문제 2

근데 생각해보니 위의 `for`loop처럼 동작하면, 파일을 순서대로 `1, 2, 3...`이라는 이름을 붙여줘야 하는데 "그 파일들을 어떻게 불러오지...?"라는 의문이 들었다. `python`과 비슷한 점이 많아 보였는데 아래와 같은 코드도 역시 있었다.

```sh
for f in *.jpg; # 현재 directory의 .jpg file을 불러온다.
do
  # my code
done
```

아직 매우매우 기초적인 코딩 공부만 하는 중이지만, 유투브에서 "코드의 재사용"도 중요하다는 것을 봤던 것 같다. 맨 처음의 `for`loop는 100개의 파일밖에 접근 못하지만, 이렇게 위와 같이 코드를 적으면 `.jpg`확장자를 가진 모든 파일에 대해서 접근이 가능하기에 **코드의 재사용**측면에서는 더욱 효율적일 것 같다는 생각을 했다.

<br>

## 문제 3

자 이제 정상적으로 코드가 작동하는지 확인해보자. linux에는 `echo`라는 명령어가 있다.

>`echo` : printing something to the user’s screen - [출처](https://developer.apple.com/library/archive/documentation/OpenSource/Conceptual/ShellScripting/shell_scripts/shell_scripts.html)

```sh
➜  ~  echo "Hello Linux"
Hello Linux
```

위와 같이 terminal창에 내가 쓴 문자열을 출력해준다. 이 명령어를 사용해서 내가 작성한 `for`loop가 정상적으로 파일에 접근해서 이름을 읽어내는지 확인해볼 것이다.

<br>

```sh
# test.sh
for f in *.jpg;
do
  echo f
done
```

```sh
➜  ~  sh test.sh 
f
f
f
f
...
f
```

?????? 당황스러웠다. 생각해보면 매우 당연한건데 아무생각도 없이 `f`만 적으면 알아서 표현이 되는 줄 알았다. 지금 내가 필요한 것은 **변수를 표현하는 방법**이다.

<br>

google에 검색하여 변수를 표현하려면 `$`를 써줘야 함을 알았다. 따라서 다음과 같이 수정했다.

```sh
# test.sh
for f in *.jpg;
do
  echo $f
done
```

```shell
➜  ~ sh test.sh 
9de8cc8ae7fd49bef4eae5d71a60e48cN9mJfaaIInxj2SFR-0.jpg
9de8cc8ae7fd49bef4eae5d71a60e48cN9mJfaaIInxj2SFR-1.jpg
9de8cc8ae7fd49bef4eae5d71a60e48cN9mJfaaIInxj2SFR-10.jpg
9de8cc8ae7fd49bef4eae5d71a60e48cN9mJfaaIInxj2SFR-11.jpg
9de8cc8ae7fd49bef4eae5d71a60e48cN9mJfaaIInxj2SFR-12.jpg
9de8cc8ae7fd49bef4eae5d71a60e48cN9mJfaaIInxj2SFR-13.jpg
...
9de8cc8ae7fd49bef4eae5d71a60e48cN9mJfaaIInxj2SFR-97.jpg
9de8cc8ae7fd49bef4eae5d71a60e48cN9mJfaaIInxj2SFR-98.jpg
9de8cc8ae7fd49bef4eae5d71a60e48cN9mJfaaIInxj2SFR-99.jpg
```

정상적으로 잘 출력된 모습이다.

<br>

## 문제 4

이제 변수를 표현하는 방법과 반복할 방법을 알았으니 다음과 같은 코드를 떠올릴 수 있다.

```sh
# test.sh
i = 1
for f in *.jpg;
do
  echo $f
  mv $f ${i}.jpg
  i++
done
```

아니 "바꿔질 이름"은 문자열인데 어떻게 1을 더하지...? 라는 의문이 들었다. 위 코드는 보기에는 잘 될 것 같지만 실제로는 당연하게도 안된다. 

```shell
test.sh: line 6: i++: command not found
```

이라고 뜬다. 그렇다면 다른 방법으로 표현해보자. `mv`를 잘못했다가 파일들이 자꾸 다 사라져서 `echo`명령어로 테스트를 진행해보겠다.

<br>

```sh
# test.sh
i = 0
for f in *.jpg;
do
  echo "$f ${i}.jpg" 
  # mv $f ${i}.jpg
  i += 1
done
```

```shell
test.sh: line 6: i: command not found
```

비슷한 오류가 또 발생했다. 근데 어떤 사이트에서 띄워쓰기를 하면 안된다고 했기에 `i += 1`에서 띄워쓰기를 없애보고 실행해봤더니,

```shell
9de8cc8ae7fd49bef4eae5d71a60e48cN9mJfaaIInxj2SFR-0.jpg 0
9de8cc8ae7fd49bef4eae5d71a60e48cN9mJfaaIInxj2SFR-1.jpg 01
...
9de8cc8ae7fd49bef4eae5d71a60e48cN9mJfaaIInxj2SFR-98.jpg 011111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111
9de8cc8ae7fd49bef4eae5d71a60e48cN9mJfaaIInxj2SFR-99.jpg 0111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111
```

이렇게 실행된 결과를 볼 수 있었다. 1을 더하긴 더했는데... python에서 문자열끼리 더한 듯한 결과였다.

<br>

## 문제 5

생각해보니 `for`에 의해서 선택되는 파일의 순서는 `0, 1, 2...99` 가 아니라 뒤죽박죽 이었다. 그리고 지금 `문제 4`에서 나왔듯이 순차적인 숫자의 증가가 잘 안되는 중이다. 그래서 지금 필요한 것을 다시 정리해보자면,

1. 파일이 순서대로 들어와야 함.
2. 순서대로 들어온 파일에 대해서 이름을 바꿔줘야 함. 이름은 `1, 2, 3...100`으로 순차적으로 증가해야 한다.

<br>

`.jpg`파일이 순서대로 들어오기 위해서 생각한 방법이

1. 파일이름를 `숫자.jpg`만 남기고 다 없애준다. ← 첫번째 shell script
2. 그리고 2번째 shell script를 만들어서 `for i in {1..100};`을 쓴다. → 순차적으로 파일에 접근하게 된다.

이제 필요한 것은 **문자열 자르는 방법**이다. [해당 사이트](https://www.snoopybox.co.kr/1811)를 참고했다.

```sh
str1="ABCDEF123"
echo ${str1:0} # ABCDEF123
echo ${str1:1} # BCDEF123
echo ${str1:4} # EF123
echo ${str1:2:3} # CDE
```

위와 같은 방법으로 자르면 됐다. 아래와 같이 첫번째 코드를 작성했다. 

```sh
# firstChange.sh
for file in *.jpg;
do
  mv "$file" "${file:49}" # 파일 이름을 변경.
  echo "$file to ${file:49}" # 확인을 위한 코드
done
```

결과는 아래와 같다.

```shell
9de8cc8ae7fd49bef4eae5d71a60e48cN9mJfaaIInxj2SFR-0.jpg to 0.jpg
9de8cc8ae7fd49bef4eae5d71a60e48cN9mJfaaIInxj2SFR-1.jpg to 1.jpg
...
9de8cc8ae7fd49bef4eae5d71a60e48cN9mJfaaIInxj2SFR-98.jpg to 98.jpg
9de8cc8ae7fd49bef4eae5d71a60e48cN9mJfaaIInxj2SFR-99.jpg to 99.jpg
```

모두 잘 바뀐 것을 확인할 수 있다.

<br>

파일이름이 현재 `0, 1, 2...99`이다. 난 `1, 2, 3...100`이라는 이름을 갖길 원하기에 shell script를 하나 더 작성해줄 것이다.

```sh
for i in {0..99};
do
  # code
done
```

위와 같이 코드를 작성해주면, 파일이 순서대로 들어오기 때문에 파일이름을 순서대로 바꿀 수 있다.

<br>

```sh
# secondChange.sh
k=0
for i in {0..99};
do
  let k+=1;
  mv "${i}.jpg" "${k}.jpg" # 0.jpg를 1.jpg로 이름을 바꾸겠습니다.
  echo "${i}.jpg to ${k}.jpg" # 확인하는 코드
done
```

위와 같이 작성해서 내가 원하는 이름으로 모두 바꿀 수 있었다...라고 생각했었지만 치명적인 실수를 했었다. 위와 같이 코드를 작성하게 되면,

> 1. 0.jpg를 1.jpg로 이름을 바꿈.
> 2. 1.jpg를 2.jpg로...음? ← 1.jpg는 바뀐 이름의 결과이다.

이런식으로 의미가 없어지는 것이다. 따라서 아까 `firstChange.sh`코드를 약간 수정했다.

```sh
# 수정된 firstChange.sh
for file in *.jpg;
do
  mv "$file" "ex${file:49}" # 파일 이름을 ex#.jpg로 변경.
  echo "$file to ex${file:49}" # 확인을 위한 코드
done
```

파일 이름을 `0, 1, 2...99`에서 `ex0, ex1, ex2...ex99`로 바꿔줬다. 이제 `secondChange.sh`로 가서 아래와 같이 수정해주면 된다.

```sh
# 수정된 secondChange.sh
k=0
for i in {0..99};
do
  let k+=1;
  mv "ex${i}.jpg" "${k}.jpg" # ex0.jpg를 1.jpg로 이름을 바꾸겠습니다.
  echo "ex${i}.jpg to ${k}.jpg" # 확인하는 코드
done
```

> 1. ex0.jpg 를 1.jpg로 이름을 바꾸겠습니다.
> 2. ex1.jpg 를 2.jpg로 이름을 바꾸겠습니다.
> 3. ...
>
> 100. ex99.jpg 를 100.jpg로 이름을 바꾸겠습니다.

<br>

## Result

![shellscript2](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/linux/img/shellscript2.png?raw=True){: width="40%" height="60%"}

파일 이름이 아름답게 잘 바뀐 것을 볼 수 있다. 공모전 때문에 급하게 한 것이라서 자세히는 공부를 못했다. 나중에 시간이 남을 때 틈틈히 linux에 대해서도 공부할 생각이다.
