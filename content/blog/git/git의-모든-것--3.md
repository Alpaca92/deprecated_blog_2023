---
title: git의 모든 것 -3
date: 2022-05-09 15:05:56
category: git
draft: false
---

# 커밋 히스토리 조회하기

기본적인 히스토리 조회 방법은 `$ git log`가 있다

```sh
$ git log
commit ca82a6dff817ec66f44342007202690a93763949
Author: Scott Chacon <schacon@gee-mail.com>
Date:   Mon Mar 17 21:52:11 2008 -0700

    changed the version number

commit 085bb3bcb608e1e8451d4b2432f8ecbe6306e7e7
Author: Scott Chacon <schacon@gee-mail.com>
Date:   Sat Mar 15 16:40:33 2008 -0700

    removed unnecessary test

commit a11bef06a3f659402fe7563abf99ad00de2209e6
Author: Scott Chacon <schacon@gee-mail.com>
Date:   Sat Mar 15 10:31:28 2008 -0700

    first commit
```

원하는 내용들만을 출력하고 싶을 때에는 `--pretty-format`옵션을 사용하면 된다

```sh
$ git log --pretty=format:"%h - %an, %ar : %s"
ca82a6d - Scott Chacon, 6 years ago : changed the version number
085bb3b - Scott Chacon, 6 years ago : removed unnecessary test
a11bef0 - Scott Chacon, 6 years ago : first commit
```

여기서 `%h`는 커밋해시 7자리, `%an`은 저자 이름, `%ar`은 저자가 커밋한 시각, `%s`는 커밋 메세지 요약(제목)을 의미한다

> ✔ 더 많은 옵션을 보고싶다면 [여기](https://git-scm.com/docs/pretty-formats)를 참고하도록 하자

`$ git log`에는 다양한 flag들이 있는데 그 중에 가장 유용한 flag중 하나가 `-S` flag다

이 옵션은 코드에서 추가되거나 제거된 내용 중 특정 텍스트가 포함되어 있는지를 검색한다

```sh
$ git log -S function_name
```

# 되돌리기

## 커밋 수정하기

종종 완료된 커밋을 수정해야 할때가 생긴다

이 때 사용하는 것이 `--amend` 옵션이다

```sh
$ git commit --amend
```

이 명령은 staging area를 사용하여 커밋한다

따라서 커밋을 하자마자 사용한다면 커밋메세지만 변경하는 명령이 되고 또 다른 파일들을 add했다면 그 파일들까지 같이 커밋되면서 커밋메세지가 수정된다

## 파일 상태를 unstaged로 변경하기

두 개의 파일을 따로 커밋하고 싶었는데 `$ git add .`를 통해 둘 다 staging area에 들어가 버렸을 때는 어떻게 해야할까?

```sh
$ git add .
$ git status
On branch master Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

     renamed:    README.md -> README
     modified:   CONTRIBUTING.md
```

아래 괄호에 `$ git reset HEAD <file>...`를 사용하면 unstaged로 만들 수 있다고 나와있다

```sh
$ git reset HEAD CONTRIBUTING.md
Unstaged changes after reset:
M   CONTRIBUTING.md

$ git status
On branch master Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    renamed:    README.md -> README

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

    modified:   CONTRIBUTING.md
```

> ⚠ `git reset`은 조심해서 사용해야 한다<br />
특히 `--hard`옵션을 사용하면 더 위험한데 이 부분에 대해서는 나중에 좀 더 자세히 다루도록 하자

## modified 파일 되돌리기

수정된 파일을 어떻게 하면 되돌릴 수 있을까?

가장 간단한 방법으로는 `ctrl + z`를 연타하는 것이다

하지만 git을 사용하면 좀 더 세련된 방법으로 되돌릴 수 있다

```sh
$ git status
On branch master Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    renamed:    README.md -> README

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

    modified:   CONTRIBUTING.md
```

위 메세지는 꽤 구체적인 방법을 알려준다

```sh
$ git checkout -- CONTRIBUTING.md $ git status
On branch master Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

     renamed:    README.md -> README
```

> ⚠ `git checkout - [file]` 명령은 꽤 위험한 명령이다<br />
원래 파일로 덮어쓰기 때문에 수정한 내용이 전부 사라지고 복원할 수 없다

변경한 내용을 쉽게 버릴 수 없고 당장은 되돌리고 싶을 때에는 `stash`, `branch`를 사용하는 것이 좋다

이에 대해서는 나중에 좀 더 구체적으로 다뤄보자