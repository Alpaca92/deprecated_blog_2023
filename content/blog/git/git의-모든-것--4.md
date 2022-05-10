---
title: git의 모든 것 -4
date: 2022-05-10 09:05:09
category: git
draft: false
---

# 리모트 저장소

리모트 저장소를 관리할 수 있는 능력이 있을 때 비로소 협업을 할 수 있다고 말할 수 있다

리모트 저장소는 인터넷이나 네트워크 어딘가에 있는 저장소를 말한다<br />
(구글 드라이브 같은 곳에 코드를 올려둔다고 생각하면 된다)

저장소를 클론한다면 리모트 저장소가 자동으로 등록되기 때문에 `origin`이라는 이름을 볼 수 있다

```sh
$ git clone https://github.com/schacon/ticgit Cloning into 'ticgit'...
remote: Reusing existing pack: 1857, done. remote: Total 1857 (delta 0), reused 0 (delta 0)
Receiving objects: 100% (1857/1857), 374.35 KiB | 268.00 KiB/s, done. Resolving deltas: 100% (772/772), done.
Checking connectivity... done.
$ cd ticgit
$ git remote origin
```

이를 `-v` 옵션을 통해 단축이름과 URL을 함께 볼 수 있다

```sh
$ git remote -v
origin  https://github.com/schacon/ticgit (fetch)
origin  https://github.com/schacon/ticgit (push)
```

만약 리모트 저장소가 여러곳이라면 모든 저장소를 보여준다

```sh
$ git remote -v
bakkdoor  https://github.com/bakkdoor/grit (fetch)
bakkdoor  https://github.com/bakkdoor/grit (push)
cho45     https://github.com/cho45/grit (fetch)
cho45     https://github.com/cho45/grit (push)
defunkt   https://github.com/defunkt/grit (fetch)
defunkt   https://github.com/defunkt/grit (push)
koke      git://github.com/koke/grit.git (fetch)
koke      git://github.com/koke/grit.git (push)
origin    git@github.com:mojombo/grit.git (fetch)
origin    git@github.com:mojombo/grit.git (push)
```

이렇게 여러개의 리모트 저장소가 등록되어 있으면 다른 사람이 기여한 내용(contributions)을 쉽게 가져올 수 있다

## 리모트 저장소 추가하기

리모트 저장소를 추가하기 위해서는 해당 저장소에 들어가 `$ git remote add ${리모트 저장소 이름} ${저장소 url}`명령을 사용하면 된다

```sh
$ git remote add pb https://github.com/paulboone/ticgit
$ git remote -v
origin  https://github.com/schacon/ticgit (fetch)
origin  https://github.com/schacon/ticgit (push)
pb  https://github.com/paulboone/ticgit (fetch)
pb  https://github.com/paulboone/ticgit (push)
```

이제 URL 대신에 `pb`라는 이름을 사용할 수 있다

```sh
$ git fetch pb
remote: Counting objects: 43, done.
remote: Compressing objects: 100% (36/36), done.
remote: Total 43 (delta 10), reused 31 (delta 5) Unpacking objects: 100% (43/43), done.
From https://github.com/paulboone/ticgit
 * [new branch]      master     -> pb/master
 * [new branch]      ticgit     -> pb/ticgit
```

`$git fetch ${리모트 저장소 이름}`을 통해 `paulboone`의 저장소에 있는 것을 가져올 수 있다

로컬에서 `pb/master`가 `paulboone`의 master 브랜치다

이 브랜치를 로컬 브랜치 중 하나에 merge하거나 checkout해서 브랜치 내용을 자세히 확인할 수 있다

## 리모트 저장소를 pull 하거나 fetch 하기

위에서 설명했듯 리모트 저장소에서 데이터를 가져오려면 간단히 아래와 같이 실행한다

```sh
$ git fetch ${리모트 저장소 이름}
```

이 명령은 로컬에는 없지만 리모트 저장소에는 있는 데이터를 모두 가져온다

이때 데이터를 모두 로컬로 가져온다고 해서 자동으로 merge를 하는 것은 아니다

이를 보완하는 명령어가 `$ git pull`이다

`$ git pull` 명령은 리모트 저장소 브랜치에서 데이터를 가져올 뿐만 아니라 자동으로 로컬 브랜치와 merge 시킬 수 있다

## 리모트 저장소에 push 하기

프로젝트를 공유하고 싶을 때 upstream 저장소에 push 할 수 있다

이 명령은 `$ git push ${리모트 저장소 이름} ${브랜치 이름}`으로 단순하다

```sh
$ git push orirgin master
```

이 명령은 리모트 저장소에 쓰기 권한이 있고 본인이 clone하고 난 이후 아무도 upstream 저장소에 push 하지 않았을 때만 사용할 수 있다

다시 말해 clone하여 작업하는 사람이 여러명이라면 다른 사람이 push한 후에 push하려고 해도 할 수 없다(conflict error)

이러한 충돌에러는 다른 사람이 push한 내용을 merge한 후에 push하면 해결할 수 있다

## 리모트 저장소 살펴보기

`$ git remote show ${리모트 저장소 이름}` 명령으로 리모트 저장소의 구체적인 정보를 확인할 수 있다

```sh
$ git remote show origin * remote origin
  Fetch URL: https://github.com/schacon/ticgit
  Push  URL: https://github.com/schacon/ticgit
  HEAD branch: master
  Remote branches:

    master                               tracked
    dev-branch                           tracked

  Local branch configured for 'git pull':
    master merges with remote master

  Local ref configured for 'git push':
    master pushes to master (up to date)
```

리모트 저장소의 URL과 추적하는 브랜치를 출력한다

이 명령은 `$ git pull` 명령을 실행할 때 master 브랜치와 merge할 브랜치가 무엇인지 보여준다

`$ git pull` 명령은 리모트 저장소 브랜치의 데이터를 모두 가져오고 나서 자동으로 merge하고 가져온 모든 리모트 저장소 정보를 출력해준다

## 리모트 저장소 이름을 바꾸거나 리모트 저장소를 삭제하기

```sh
$ git remote remae pb paul
$ git remote
origin
paul
```

위와 같이 간단하게 변경 할 수 있다

이때 로컬에서 관리하던 리모트 저장소의 브랜치 이림도 함께 변경된다는 점을 기억하자

지금까지 `pb/master`로 리모트 저장소 브랜치를 사용했다면 이제는 `paul/master`라고 사용해야 한다

리모트 저장소를 삭제해야 한다면 `$ git remote remove(rm)` 명령을 사용하면 된다

```sh
$ git remote rm paul
$ git remote
origin
```

remove로 리모트 저장소를 삭제하면 해당 리모트 저장소에 관련된 추적 브랜치 정보나 모든 설정 내용도 함께 삭제된다