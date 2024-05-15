---
layout: post
title: "Git flow cli로 써보기"
author: "xi-jjun"
tags: Git

---

# Git Flow를 CLI로 써보자!

## 개요

매번 IntelliJ라는 좋은 도구로 git flow의 기능들을 사용했었는데, cli로 사용해보면서 이때까지 얼마나 편하게 살았는지를 느껴보도록 하겠다.

<br>

## git init

저장소를 만들어보겠다. 마침 Spring 비동기 처리 관련해서 궁금증이 생겼고, 이를 저장하기 위한 `spring-practices` repo를 만들기로 하였다.

```shell
git init
git remote add origin https://git....
```

<br>

## git flow init

```shell
git flow init
No branches exist yet. Base branches must be created now.
Branch name for production releases: [master] master
Branch name for "next release" development: [develop] develop

How to name your supporting branch prefixes?
Feature branches? [feature/] practice
Bugfix branches? [bugfix/] bugfix
Release branches? [release/] release
Hotfix branches? [hotfix/] hotfix
Support branches? [support/] support
Version tag prefix? []
Hooks and filters directory? [/Users/kimjaejun/workspace/spring-practices/.git/hooks]
```

- production 브래치 : master
- next release 브랜치 : develop
- feature 브랜치 : practice (기능이 아닌 '연습'의 단위로 머지할 것 같아서 그냥 이름을 바꿔보았다.)
- 그 외는 동일

<br>

## git flow start feature

```shell
git flow feature start project_init
Switched to a new branch 'practiceproject_init'

Summary of actions:
- A new branch 'practiceproject_init' was created, based on 'develop'
- You are now on branch 'practiceproject_init'

Now, start committing on your feature. When done, use:

     git flow feature finish project_init
```

![git_flow_init_fail](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/gitpost/img/git_flow_init_fail.png?raw=True){: width="70%"}

... `/` 가 빠졌다. 다시 초기화하자.

<br>

```shell
git flow init -f

Which branch should be used for bringing forth production releases?
   - develop
   - master
   - practiceproject_init
Branch name for production releases: [master] master

Which branch should be used for integration of the "next release"?
   - develop
   - practiceproject_init
Branch name for "next release" development: [develop] develop

How to name your supporting branch prefixes?
Feature branches? [feature/] practice/
Bugfix branches? [bugfix/]
Release branches? [release/]
Hotfix branches? [hotfix/]
Support branches? [support/]
Version tag prefix? []
Hooks and filters directory? [/Users/kimjaejun/workspace/spring-practices/.git/hooks]
```

![git_flow_init_success](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/gitpost/img/git_flow_init_success.png?raw=True){: width="70%"}

`/` 까지 포함되어 브랜치가 잘 생성되는 것을 확인했다!

<br>

## git flow feature finish

커밋하고 push는 pass. finish를 바로 때려보자. (사실 `--no-ff` 옵션을 까먹어서 `test1` practice 브랜치를 다시 생성하여 진행함)

merge 기록을 남기기 위해서 `--no-ff` 옵션을 사용했다.

```shell
git flow feature finish --no-ff
Switched to branch 'develop'
Merge made by the 'ort' strategy.
 README.md | 1 +
 1 file changed, 1 insertion(+)
To https://github.com/xi-jjun/spring-practices.git
 - [deleted]         practice/test1
Deleted branch practice/test1 (was d6d4668).

Summary of actions:
- The feature branch 'practice/test1' was merged into 'develop'
- Feature branch 'practice/test1' has been locally deleted; it has been remotely deleted from 'origin'
- You are now on branch 'develop
```

```shell
git push origin develop
```

이후 로컬에 merge된 develop을 origin으로 push하게되면, 아래와 같은 commit log를 `develop` 브랜치에서 확인이 가능하다.

```shell
git log --graph --oneline --decorate --all
*   c741ca8 (HEAD -> develop, origin/develop) Merge branch 'practice/test1' into develop
|\
| * d6d4668 test1
|/
* ae6f470 (origin/practice/project_init, practice/project_init) project init
* 87af359 (origin/master, practiceproject_init, master) Initial commit
```

- `c741ca8` : `--no-ff` 옵션으로 만든 merge용 commit
- `d6d4668` : `test1` feature에서 만들었던 commit
- `ae6f470` : 아까 실수로 fast forward 옵션으로 develop에 merge했던 commit
- `87af359` : 해당 repo의 프로젝트 최초 commit

<br>

## git flow release start/finish

이제 master로의 머지(실제라면 실환경 배포를 위한 머지)를 해보도록 하자.

```shell
git flow release start 20240515_1628
Switched to a new branch 'release/20240515_1628'

Summary of actions:
- A new branch 'release/20240515_1628' was created, based on 'develop'
- You are now on branch 'release/20240515_1628'

Follow-up actions:
- Bump the version number now!
- Start committing last-minute fixes in preparing your release
- When done, run:

     git flow release finish '20240515_1628'
```

릴리즈 브랜치 명은 그냥 그날 날짜_시각을 적었다.

<br>

참고) `20240515_1628`는 release finish하다가 실수로 머지 커밋을 이상하게 해서... 아래 `20240515_1633` 으로 다시 하였다...

```shell
git flow release finish '20240515_1633'
Switched to branch 'master'
Merge made by the 'ort' strategy.
 README.md | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
Already on 'master'
Switched to branch 'develop'
Merge made by the 'ort' strategy.
Deleted branch release/20240515_1633 (was 839c45b).

Summary of actions:
- Release branch 'release/20240515_1633' has been merged into 'master'
- The release was tagged '20240515_1633'
- Release tag '20240515_1633' has been back-merged into 'develop'
- Release branch 'release/20240515_1633' has been locally deleted
- You are now on branch 'develop'
```

```shell
git push origin develop master
```

```shell
git log --graph --oneline --decorate --all
*   4500fb2 (HEAD -> develop, origin/develop) Merge tag '20240515_1633' into develop
|\
| *   76e51ae (tag: 20240515_1633, origin/master, master) Merge branch 'release/20240515_1633'
| |\
| |/
|/|
* |   839c45b Merge branch 'practice/test2' into develop
|\ \
| * | b1d53dc practice: test2
|/ /
* | 7dddc6c Merge tag '20240515_1628' into develop
|\|
| *   ed162fb (tag: 20240515_1628) Merge branch 'release/20240515_1628'
| |\
| |/
|/|
* |   c741ca8 Merge branch 'practice/test1' into develop
|\ \
| * | d6d4668 test1
|/ /
* / ae6f470 (origin/practice/project_init, practice/project_init) project init
|/
* 87af359 (practiceproject_init) Initial commit
```

![git_merge_feature_and_release](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/gitpost/img/git_merge_feature_and_release.png?raw=True){: width="70%"}

릴리즈 브랜치(`20240515_1633`)이 develop, master에 각각 잘 merge된 것을 확인할 수 있다.

<br>

## Next...

rebase, squash 등등에 도전해볼 예정.

<br>

## References

- [git flow cheatsheet](http://danielkummer.github.io/git-flow-cheatsheet/)
