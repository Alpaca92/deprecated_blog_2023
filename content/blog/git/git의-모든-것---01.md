---
title: git의 모든 것 - 1
date: 2022-05-06 15:05:32
category: git
draft: false
---

git에 대한 심도있는 공부가 필요한 것 같아 [Pro git](https://git-scm.com/book/ko/v2)을 다운받았다

여기서 읽은 내용을 바탕으로 git에 대해 정리해보려 한다

# Git의 무결성

git은 데이터를 저장하기 전에 항상 체크섬을 구하고 그 체크섬으로 데이터를 관리한다

이 체크섬은 `SHA-1 hash`를 사용하여 만들며 40자 길이의 16진수를 사용한다

> ex. 24b9da6552252987aa493b52f8696cd6d3b00373

# Git의 세 가지 상태

git은 `committed`, `modified`, `staged`의 세 가지 상태로 구분되는데

1. `committed` : 데이터가 로컬 데이터베이스에 안전하게 저장되었음

2. `modified` : 수정한 파일을 아직 로컬 데이터베이스에 커밋하지 않음

3. `staged` : 현재 수정한 파일을 곧 커밋할 것이라고 표시한 상태

![working tree, staging area, git directory](https://raw.githubusercontent.com/Alpaca92/alpaca92.github.io/master/content/blog/git/images/evrth_of_git_01.png)

git 디렉토리는 git이 프로젝트의 메타데이터와 객체 데이터베이스를 저장하는 곳이다

git 디렉토리에 있는 파일들은 `committed` 상태이다

파일을 수정하고 `staging area`에 추가했다면 `staged`다

checkout하고 나서 수정은 했지만 아직 `staging area`에 추가하지 않았다면 `modified`다

> 📌 `$ git add`와 `$ git commit`을 하면 git 디렉토리로 들어가게 된다

# Git 최초 설정

`git config`라는 도구로 설정 내용을 확인하고 변경할 수 있는데 이때 사용되는 설정파일은 세 가지로 나뉜다

1. `/etc/gitconfig` : 시스템의 모든 사용자와 모든 저장소에 적용되는 설정이다

> `git config --system`옵션으로 이 파일을 읽고 쓸 수 있다

2. `~/.gitconfig`, `~/.config/git/config` : 특정 사용자(현재 사용자)에게만 적용되는 설정이다

> `git config --global`옵션으로 이 파일을 읽고 쓸 수 있다

3. `.git/config` : git 디렉토리에 있고 특정 저장소에만 적용되는 설정이다

> `git config --local`옵션으로 이 파일을 읽고 쓸 수 있다(--local: default value)

위의 설정은 3 > 2 > 1순으로 우선순위를 갖는다

이제 우선순위를 알았으니 글로벌설정을 진행해보자

```sh
$ git config --global user.name "ayaan"
$ git config --global user.email "email@gmail.com"
```

편집기는 시스템의 기본편집기를 사용하는데 내 경우는 vscode로 설정하였다

```sh
git config --global core.editor "code --wait"
```

> `--wait` flag는 편집이 완료되고 해당 파일의 실행을 종료했을 때까지 다음 입력을 대기한다는 설정이다

이제 설정이 다 끝났다면 설정이 잘 됐는지 확인해보도록 하자

```sh
$ git config --list --show-origin
```

`$ git config --list`만으로도 설정을 확인할 수 있지만 어떤 파일에 의해 설정이 되었는지 확인할 수 있는 ``show-origin` flag를 사용하면 설정을 수정할 때 좀 더 용이하다


