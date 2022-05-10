---
title: git의 모든 것 -5
date: 2022-05-10 10:05:44
category: git
draft: false
---

# 태그

다른 버전 관리 시스템(VCS)처럼 git도 태그를 지원한다

보통 릴리즈할 때 `v1.0`등등으로 사용한다

## 태그 조회하기

```sh
$ git tag
v0.1
v1.3
```

검색 패턴을 사용하여 태그를 검색 할 수도 있다

```sh
$ git tag -l "v1.8.5*"
v1.8.5
v1.8.5-rc0
v1.8.5-rc1
v1.8.5-rc2
v1.8.5-rc3
v1.8.5.1
v1.8.5.2
v1.8.5.3
v1.8.5.4
v1.8.5.5
```

## 태그 붙이기

git의 태그는 `lightweight`, `annotated` 태그로 두 종류가 있다

`lightweight`태그는 브랜치와 비슷한데 브랜치처럼 가리키는 지점을 최신 커밋으로 이동시키지 않고 단순히 특정 커밋에 대한 포인터로써의 역할을 한다

`annotated`태그는 git 데이터베이스에 태그를 만드는 사람의 이름, 이메일, 태그를 만든 날짜, 태그 메세지도 저장한다

## annotated 태그

`-a`옵션으로 `annotated`태그를 만들 수 있다

```sh
$ git tag -a v1.4 -m "my version 1.4"
$ git tag
v0.1
v1.3
v1.4
```

`-m`옵션을 사용하면 태그르르 저장할 때 메세지를 함께 저장할 수 있다

저장된 메세지 등은 `$ git show`를 통해 구체적으로 확인할 수 있다

```sh
$ git show v1.4
tag v1.4
Tagger: Ben Straub <ben@straub.cc>
Date:   Sat May 3 20:19:12 2014 -0700

my version 1.4

commit ca82a6dff817ec66f44342007202690a93763949
Author: Scott Chacon <schacon@gee-mail.com>
Date:   Mon Mar 17 21:52:11 2008 -0700

    changed the version number
```

## lightweight 태그

`lightweight` 태그는 기본적으로 파일에 커밋 체크섬을 저장하는 것 뿐이다

`lightweight` 태그를 만들 때에는 `-a`, `-m`, `-s`옵션을 사용하지 않고 그저 이름만 달아주면 된다

```sh
$ git tag v1.4-lw
$ git tag
v0.1
v1.3
v1.4
v1.4-lw
v1.5
```

이 태그는 `$ git show`로 확인해도 별도의 태그 정보를 확인할 수 없다

```sh
$ git show v1.4-lw
commit ca82a6dff817ec66f44342007202690a93763949
Author: Scott Chacon <schacon@gee-mail.com>
Date:   Mon Mar 17 21:52:11 2008 -0700

    changed the version number
```

## 나중에 태그하기

예전 커밋에 대해서도 태그를 할 수 있다

```sh
$ git log --pretty=oneline
15027957951b64cf874c3557a0f3547bd83b3ff6 Merge branch 'experiment'
a6b4c97498bd301d84096da251c98a07c7723e65 beginning write support
0d52aaab4479697da7686c15f77a3d64d9165190 one more thing
6d52a271eda8725415634dd79daabbc4d9b6008e Merge branch 'experiment'
0b7434d86859cc7b8c3d5e1dddfed66ff742fcbc added a commit function
4682c3261057305bdd616e23b64b0857d832627b added a todo file
166ae0c4d3f420721acbb115cc33848dfcc2121a started write support
9fceb02d0ae598e95dc970b74767f19372d61af8 updated rakefile
964f16d36dfccde844893cac5b347e7b3d44abbc commit the todo
8a5cbc430f1a9c3d00faaeffd07798508422908a updated readme
```

```sh
$ git tag -a v1.2 9fceb02
```

```sh
$ git tag
v0.1
v1.2
v1.3
v1.4
v1.4-lw
v1.5

$ git show v1.2
tag v1.2
Tagger: Scott Chacon <schacon@gee-mail.com>
Date:   Mon Feb 9 15:32:16 2009 -0800

version 1.2
commit 9fceb02d0ae598e95dc970b74767f19372d61af8
Author: Magnus Chacon <mchacon@gee-mail.com>
Date:   Sun Apr 27 20:43:35 2008 -0700

    updated rakefile

...
```

## 태그 공유하기

`$ git push` 명령은 자동으로 리모트 서버에 태그를 전송하지 않으므로 태그를 만들었다면 서버에 별도로 push 해줘야 한다

브랜치를 공유하는 것과 방법은 동일하다<br />
(`$ git push origin ${태그이름}`)

```sh
$ git push origin v1.5
Counting objects: 14, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (12/12), done.
Writing objects: 100% (14/14), 2.05 KiB | 0 bytes/s, done.
Total 14 (delta 3), reused 0 (delta 0)
To git@github.com:schacon/simplegit.git

  * [new tag]         v1.5 -> v1.5
```

만약 한번에 여러 태그를 push하고 싶다면 `--tags`옵션을 추가하면 된다

이 명령은 리모트 서버에 없는 태그를 모두 전송해준다

이제 누군가 저장소에 clone하거나 pull하게 되면 모든 태그 정보도 함께 전송된다

## 태그를 checkout 하기

태그가 특정 버전을 가리키고 있고 특정 버전의 파일을 체크아웃해서 확인하고 싶다면 다음과 같이 실행한다

단 태그를 체크아웃하면 `detached HEAD`상태가 되며 일부 git 관련 작업이 브랜치에서 작업하는 것과 다르게 동작할 수 있다

```
$ git checkout 2.0.0
Note: checking out '2.0.0'.
You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

  git checkout -b <new-branch>

HEAD is now at 99ada87... Merge pull request #89 from schacon/appendix- final

$ git checkout 2.0-beta-0.1
Previous HEAD position was 99ada87... Merge pull request #89 from schacon/appendix-final
HEAD is now at df3f601... add atlas.json and cover image
```

`detached HEAD`상태에서 작업을 하고 커밋을 만들면 태그는 그대로 있으나 새로운 커밋이 하나 쌓인 상태가 되고 새 커밋에 도달할 수 있는 방법이 따로 없게 된다

물론 커밋의 해시 값을 정확히 기억하고 있으면 가능하긴 하지만 이보다는 브랜치를 만들어서 작업하는 것이 바람직하다

```sh
$ git checkout -b version2 v2.0.0
Switched to a new branch 'version2'
```