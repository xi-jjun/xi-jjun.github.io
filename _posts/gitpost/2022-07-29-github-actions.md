---
layout: post
title: "Github actions로 자동 gradle build 해보기"
author: "xi-jjun"
tags: Git

---

# Github Actions로 gradle 자동 build

프로젝트 팀원을 구하기 위해 인프런 커뮤니티를 뒤적거리다 좋은 팀을 만나게 됐다. 나를 포함하여 총 4명의 개발 지망생과 현업자 한 명으로 이뤄진 팀에서는 프로젝트를 진행할 때, aws에 배포하며 테스트를 할 것이기 때문에 CI/CD가 필수적이었다. 

처음 도전하는 것이기에 가장 간단한 것부터 하기로 했다.

> Build test를 자동으로 해보자!

라는 것인데, 사용자가 직접 `./gradlew clean test` 를 해도 되고, intelliJ에서 빌드해도 된다. 근데 github actions로 github repo에 push할 때 자동으로 해주게끔 해보는 방법을 찾아볼 것이다.

<br>

## why?

위에서 필수적이라고 했는데 왜 그런지에 대해 말해보자면, 너무 귀찮다는 것이다. 로컬에서 테스트를 다 끝냈다고 생각했으나 수정사항이 생겨서 다시 로컬에서 수정하고 빌드하고 테스트 하고 배포할 생각을 하면, 수정하기가 싫을 정도이다. 최근에 토이 프로젝트로 [백발백준](https://github.com/xi-jjun/baekjoon-recommender) 을 진행했었는데, 현재 aws에 배포를 했으나, 추가할 기능이 생겨서 현재 나중으로 미뤄둔 상태이다...

이번에 할 프로젝트에 대해서는 다음과 같은 이유로 aws에 배포하려고 하는 것이다.

> Front-end에서 test를 로컬이 아닌, aws로 하려는 것

이는 front-end입장에서는 굳이 로컬에서 Java를 실행하는 방법을 몰라도 손쉽게 실제 테스트가 가능하기에 좋은 것 같다. 결국 이 말은 이번 프로젝트에서는 AWS에 배포할 일이 많아진다는 뜻이기도 하기에 자동으로 빌드, 테스트, 배포를 해주는 방법이 필요해져서 공부하게 됐다.

<br>

# Working Flow

어떤 일이든지 방법을 모르면 접근하기가 쉽지 않다. 일단 github actions는 자동으로 우리가 의도한 동작을 서버에 실행시켜준다는 것 같다는 느낌만 알고 시작했다.

근데 방법 자체는 생각보다 매우 간단했고, 이제 간단한 문서 정도는 읽고 바로 적용할 수 있었기에 적용을 바로 해봤다.

<br>

## Test github actions

먼저 테스트 용으로 repository를 하나 생성하였다.

![githubactions1_1](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/git/img/githubactions1_1.png?raw=True){: width="70%"}

위 사진을 보게 되면, `./github/workflows` 라는게 보인다.

![githubactions1_2](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/git/img/githubactions1_2.png?raw=True){: width="70%"}

여기에 내가 원하는 workflow를 yml file로 생성하면 되는 것이다.

<br>

## main.yml

```yaml
# This is a basic workflow to help you get started with Actions

name: CI practice

# Controls when the workflow will run
on:
  # Triggers the workflow on push or pull request events but only for the "master" branch
  push:
    branches: [ "master" ]
  pull_request:
    branches: [ "master" ]

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "build"
  build:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest

    # Steps represent a sequence of tasks that will be executed as part of the job
    # 실제로 해야하는 일을 정의
    steps:
      # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
      # - uses: actions/checkout@v3

      # Runs a single command using the runners shell
      # name : script name
      # run : actual command
      - name: Run pwd
        run: pwd

      # Runs a set of commands using the runners shell
      - name: Run a multi-line script ls and echo
        run: |
          ls -al
          echo test.
```

실제로 테스트 때 사용한 코드이다. github주소는 [여기로](https://github.com/xi-jjun/github-actions-practice/blob/master/.github/workflows/main.yml) 가면 된다. 기본으로 나오는 주석의 설명이 매우 잘 되어 있기에 해당 부분만 읽어도 어느정도 이해는 다 됐다.

workflow에서는 job이라고 불리는 작업을 순서대로 혹은 병렬적으로 실행시킬 수 있다고 나와있다. 그리고 해당 작업들은 이름을 지어줄 수 있는데 위에서는 현재 `build` 라는 이름의 작업만을 정의했다. 실제 우리가 동작시킬 command는 `steps` 에 있다. `name` 은 진짜로 문자열 그 자체 이름이고, 아래에 있는 `run` 이 어떤 동작인지 설명하기 뤼함이다. `run` 에서는 우리가 의도한 동작을 진행할 command를 기입하면 된다.

<br>

## 적용

요즘 TDD에 대해 관심이 생겼고, 이 또한 이번 프로젝트에서 사용해보기 위해서 `Next-Step` 의 강의를 하나 사서 듣게 됐다. 그리고 해당 강의에서는 어플리케이션 기능을 구현하고, 테스트 케이스를 작성해야할 일이 많기에 github actions로 특정 branch에 push를 하게 될 때 자동으로 테스트가 되게 해보고 싶었다.

![githubactions1_3](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/git/img/githubactions1_3.png?raw=True){: width="70%"}

`new workflow` 를 누른 뒤,

![githubactions1_4](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/git/img/githubactions1_4.png?raw=True){: width="70%"}

`Java with Gradle` 를 클릭하면 된다. 직접 만드는 것도 좋지만, 반복되는 작업인 만큼 웬만한 작업에 대해서는 이미 만들어진 스크립트가 존재했었다. 따라서 나는 현재 gradle build, test를 자동으로 하고 싶었기에 해당 스크립트로 진행했다.

<br>

![githubactions1_5](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/git/img/githubactions1_5.png?raw=True){: width="70%"}

스크립트에서 trigger branch정도만 내가 원하는 것으로 수정해준 뒤, 해당 branch로 push를 하게 되면 위와 같이 가상의 서버에서 내가 의도한 동작이 자동으로 수행됨을 볼 수 있다. 해당 workflow 코드는 [여기서](https://github.com/xi-jjun/java-baseball-playground) 볼 수 있다.

<br>

## 후기

오늘은 aws 자동 배포 전에 github actions에 대해 간단하게 써보고자 포스팅을 하게 됐다. 생각보다 간단해서 놀랐는데 간단한 것만 해서 그런 것 같다. 현재 목표로 잡고 있는 것은, `main` branch에 PR, push가 발생할 경우, notion api를 사용하여 자동으로 문서를 만들 생각이다. 그 전에 aws자동 배포부터 끝마치고 말이다.

영어는 나에게 익숙하지 않은 존재인줄 알고 공식 docs를 이해못할 줄 알았는데 생각보다 너무 자세히 잘 설명되어 있어서 더 쉽게 할 수 있었던 것 같다.

<br>

## Reference

[github actions docs](https://docs.github.com/en/actions/learn-github-actions/understanding-github-actions)

[생활 코딩 - github actions](https://www.youtube.com/watch?v=uBOdEEzjxzE)





















