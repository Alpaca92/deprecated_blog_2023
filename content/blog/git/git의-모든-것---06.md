---
title: git의 모든 것 - 6
date: 2022-05-10 10:05:28
category: git
thumbnail: { thumbnailSrc }
draft: false
---

# git alias

명렁을 전부 입력해서 사용하기 귀찮을 때 사용하는게 `alias`다

```sh
$ git config --global alias.co checkout
$ git config --global alias.br branch
$ git config --global alias.ci commit
$ git config --global alias.st status
```

이제 다음과 같이 동작한다

```sh
$ git co test

# 실제 동작하는 코드
$ git checkout test
```

```sh
$ git st

# 실제 동작하는 코드
$ git status
```

한결 명령어를 사용하기 편해졌다

git을 설치하면 `git bash`가 함께 설치되는데 기본적으로 되어있는 단축어를 볼 수 있다

```sh
$ alias
```

내 경우에는 `$ git config`를 사용하지 않고 `alias.sh`을 직접 수정하여 단축어를 관리하고 있다

> 📌 `alias.sh`의 위치는 Git 디렉토리를 살펴보면 된다<br />
> (내 경우에는 `c/Program Files/Git/etc/profile.d/aliases.sh`에 있었다)

이를 수정하기 편하기 위해서 나는 아래와 같은 단축어를 추가하였다

```sh
# 📁c/Program Files/Git/etc/profile.d/aliases.sh

alias mod_alias="code /c/'Program Files'/Git/etc/profile.d/aliases.sh"
```

위와 같이 단축어를 추가하고 싶다면 `alias ${단축어}="${실제 명령}"`으로 추가하면 된다

# 브랜치

개발을 하다 보면 코드를 여러 개로 복사해야 하는 일이 자주 생긴다

코드를 통째로 복사하고 나서 원래 코드와는 상관없이 독립적으로 개발을 진행할 수 있는데, 이렇게 독립적으로 개발하는 것이 **브랜치**다

## 브랜치란 무엇인가

git이 브랜치를 다루는 과정을 이해하기 위해 git이 데이터를 어떻게 저장하는지에 대해 알아보도록 하자

git은 데이터의 변경사항(diff)을 기록하지 않고 일련의 스냅샷으로 기록한다

커밋하면 git은 현 staging area에 있는 데이터의 스냅샷에 대한 포인터, 저자나 커밋 메세지 같은 메타데이터, 이전 커밋에 대한 포인터 등을 포함하는 commit object를 저장한다

파일이 3개 있는 디렉토리가 하나 있고 이 파일을 staging area에 저장하고 커밋하는 예제를 살펴보자<br />
(디렉토리의 구조는 아래와 같다)

```
📁example directory
  ├ README
  ├ test.rb
  └ LICENSE
```

파일을 staged하면 git 저장소에 파일을 저장하고(이를 git에서는 `Blob`이라고 부른다) staging area에 해당 파일의 체크섬을 저장한다

```sh
$ git add .
$ git commit -m 'The initial commit of my project'
```

`$ git commit`으로 커밋하면 먼저 루트 디렉토리와 각 하위 디렉토리의 트리 객체를 체크섬과 함께 저장소에 저장한다

그 다음에 커밋 객체를 만들고 메타데이터와 루트 디렉토리 트리 객체를 가리키는 포인터 정보를 커밋 객체에 넣어 저장한다<br />
(그래서 스냅샷을 언제든지 필요할 때 다시 만들 수 있는 것이다)

이 작업을 마치고 나면 git에는 5개의 데이터 객체가 생긴다

1. 각 파일에 대한 Blob 3개(README, test.rb, LICENSE)

2. 파일과 디렉토리 구조가 들어있는 트리 객체

3. 메타데이터와 루트 트리를 가리키는 포인터가 담긴 커밋 객체

![commit, tree data](https://raw.githubusercontent.com/Alpaca92/alpaca92.github.io/master/content/blog/git/images/evrth_of_git_02.png)

다시 수정하고 커밋을 하면 아래와 같이 이전 커밋을 parent에 저장한다

![commit, before commit](https://raw.githubusercontent.com/Alpaca92/alpaca92.github.io/master/content/blog/git/images/evrth_of_git_03.png)

`$ git init`읋 하면 자동적으로 master 브랜치를 만드는데 이름이 master라고 해서 추가되는 브랜치들과 다른점은 없다

처음 커밋을 하게되면 이 master 브랜치가 생성된 커밋을 가리키고 이후에는 최종커밋(가장 최근에 한 커밋)을 자동으로 가리킨다

![branch, commit history](https://raw.githubusercontent.com/Alpaca92/alpaca92.github.io/master/content/blog/git/images/evrth_of_git_04.png)

## 새 브랜치 생성하기

```sh
$ git branch testing
```

이제 `testing`이라는 이름의 브랜치가 하나 생성됐다

이 브랜치도 지금 작업하고 있던 마지막 커밋을 가리키고 있다

git은 지금 작업중인 브랜치를 어떻게 파악할 수 있는걸까?

```sh
$ git log
```

커밋한 이력을 보면 항상 `HEAD`라는게 보인다

이 `HEAD`라는 특수한 포인터는 지금 작업하는 로컬브랜치를 가리킨다

## 브랜치 이동하기

```sh
$ git checkout testing
```

이제 testing 브랜치로 이동이 되었고 `HEAD` 포인터 또한 testing을 가리킨다

이제 여기서 새로 커밋을 하게 되면 다음과 같아진다

![HEAD가 가리키는 testing 브랜치가 새 커밋을 가리킴](https://raw.githubusercontent.com/Alpaca92/alpaca92.github.io/master/content/blog/git/images/evrth_of_git_05.png)

다시 master로 돌아오면 `HEAD`는 아래와 같이 이동하게 된다

```sh
$ git checkout master
```

![HEAD가 Checkout 한 브랜치로 이동함](https://raw.githubusercontent.com/Alpaca92/alpaca92.github.io/master/content/blog/git/images/evrth_of_git_06.png)

워킹디렉토리의 파일도 `f30ab` 커밋 시점으로 돌아갔다

이렇게 브랜치 작업을 하다 시각적으로 확인하고 싶다면 아래의 명령을 실행해보자

```sh
$ git log --oneline --decorate --graph --all
* c2b9e (HEAD, master) made other changes | * 87ab2 (testing) made a change
|/
* f30ab add feature #32 - ability to add new formats to the
* 34ac2 fixed bug #1328 - stack overflow under certain conditions * 98ca9 initial commit of my project
```

> 📌 `$ git checkout -b ${브랜치 명}`을 통해 브랜치의 생성과 이동을 한번에 할 수 있다

## Merge의 기초

![master와 별개로 진행하는 iss53 브랜치](https://raw.githubusercontent.com/Alpaca92/alpaca92.github.io/master/content/blog/git/images/evrth_of_git_06.png)

브랜치와 커밋상황이 위와 같을 때 iss53 브랜치를 master 브랜치에 합치고 싶다면 머지 명령을 하면된다

```sh
$ git checkout master
Switched to branch 'master'

$ git merge iss53
Merge made by the 'recursive' strategy.
index.html |    1 +
1 file changed, 1 insertion(+)
```

