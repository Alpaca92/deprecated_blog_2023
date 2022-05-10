---
title: git의 모든 것 -2
date: 2022-05-09 10:05:43
category: git
draft: false
---

# git 저장소 만들기

## 기존 디렉토리를 git 저장소로 만들기

```sh
$ cd my_project
$ git init
```

이 명령은 `.git`이라는 하위 디렉토리를 만든다

`.git`디렉토리에는 저장소에 필요한 뼈대 파일(skeleton)이 들어 있어 해당 디렉토리를 git 저장소로 만들 수 있게 된다

## 기존 저장소를 clone 하기

다른 프로젝트를 참여하거나 git 저장소를 복사하고 싶다면 `git clone`을 사용하면 된다

```sh
$ git clone https://github.com/example.git
```

위 명령어는 `📁example`안에 프로젝트를 clone하는데 디렉토리 이름을 변경하고 싶다면 뒤에 원하는 디렉토리명을 써주면 된다

```sh
$ git clone https://github.com/example.git ${원하는 디렉토리명}
```

# 수정하고 저장소에 저장하기

워킹 디렉토리의 모든 파일은 크게 tracked, untracked로 나뉜다

track는 또 다시 unmodified, modified, staged로 나뉜다

```
file ┬ untracked
     └ tracked ┬ unmodified: 수정하지 않음
               ├ modified: 수정 함
               └ staged: add 명령어로 staging area로 감
```

파일의 상태를 확인할 때에는 일반적으로 다음과 같이 확인한다

```sh
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
nothing to commit, working directory clean
```

위의 내용은 파일을 하나도 수정하지 않았다는 것을 말해준다<br />
(정확히는 tracked 파일은 하나도 수정되지 않았다는 의미다)

프로젝트에 README 파일을 만들면 다음과 같이 메세지가 바뀐다

```sh
$ git status
Your branch is up-to-date with 'origin/master'. Untracked files:
  (use "git add <file>..." to include in what will be committed)

     README

nothing added to commit but untracked files present (use "git add" to track)
```

git은 untracked 파일을 아직 스냅샷(커밋)에 넣어지지 않은 상태라고 본다

## 파일을 새로 추적하기

```sh
$ git add README
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

     new file:   README
```

이제 README 파일은 tracked 상태면서 커밋에 추가될 staged 상태라는 것을 알 수 있다

## modified상태의 파일을 stage하기

이미 tracked 상태인 파일을 수정하면 다음과 같다

```sh
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

     new file:   README

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

    modified:   CONTRIBUTING.md
```

이 경우 다시 `$ git add`를 통해 CONTRIBUTING.md 파일을 staged 상태로 만들어줘야 한다

```sh
$ git add CONTRIBUTING.md
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

     new file:   README
     modified:   CONTRIBUTING.md
```

여기서 다시 한번 CONTRIBUTING.md 파일을 수정하게 되면 다음과 같은 상태가 된다

```sh
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

     new file:   README
     modified:   CONTRIBUTING.md

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

    modified:   CONTRIBUTING.md
```

즉, 수정하기 전의 CONTRIBUTING.md 파일은 staged 상태이지만 수정된 CONTRIBUTING.md 파일은 unstaged 상태이다

따라서 다시 한번 `$ git add`명령어를 통해 staged 상태로 만들어줘야 한다

```sh
$ git add CONTRIBUTING.md
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

     new file:   README
     modified:   CONTRIBUTING.md
```

## 파일 무시하기

git으로 관리하지 않아도 되는 파일이나 공개되서는 안되는 파일들이 있다

이를 손쉽게 무시할 수 있는 방법이 `.gitignore`이다

```sh
$ touch .gitignore # root path
```

```sh
# 📁.gitignore

*.[oa] #.o, .a 파일을 git이 무시
*~ # ~로 끝나는 파일을 git이 무시
```

asterisk 2개(\*\*)로 디렉토리 안의 디렉토리 또한 표현할 수 있다

예를 들어 `a/**/z`는 a/z, a/b/z, a/b/c/z 등의 패턴을 모두 git이 무시한다

이러한 패턴을 작성하는 것은 사용자의 몫이며 아래는 간단한 `.gitignore`의 예시디

```sh
# 📁.gitignore

# 확장자가 .a인 파일 무시
*.a

# 윗  라인에서 확장자가 .a인 파일은 무시하게 했지만 lib.a는 무시하지 않음
!lib.a

# 현재 디렉토리에 있는 TODO파일은 무시하고 subdir/TODO처럼 하위디렉토리에 있는 파일은 무시하지 않음
/TODO

# build/ 디렉토리에 있는 모든 파일은 무시
build/

# doc/notes.txt 파일은 무시하고 doc/server/arch.txt 파일은 무시하지 않음
doc/*.txt

# doc 디렉토리 아래의 모든 .pdf 파일을 무시
doc/**/*.pdf
```

## staged와 unstaged 상태의 변경 내용을 보기

단순히 파일이 변경된 것이 아니라 좀 더 디테일하게 변경내용을 살펴보려면 `$ git diff`를 사용해야 한다

```sh
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

     modified:   README

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

    modified:   CONTRIBUTING.md
```

```sh
$ git diff
diff --git a/CONTRIBUTING.md b/CONTRIBUTING.md
index 8ebb991..643e24f 100644
--- a/CONTRIBUTING.md
+++ b/CONTRIBUTING.md
@@ -65,7 +65,8 @@ branch directly, things can get messy.
 Please include a nice description of your changes when you submit your PR;
 if we have to read the whole diff to figure out why you're contributing  in the first place, you're less likely to get feedback and have your change
-merged in.
+merged in. Also, split your changes into comprehensive chunks if your patch is
+longer than a dozen lines.
 If you are starting to work on a particular area, feel free to submit a PR
 that highlights your work in progress (and note in the PR title that it's
```

이렇게 `$ git diff`를 통해 tracked && unstaged 파일의 변경점을 확인할 수 있다<br />
(이 명령은 워킹 디렉토리와 staging area에 있는 것을 비교한다)

만약 커밋을 위해 staging area에 넣은 파일의 변경 부분을 보고 싶으면 `$ git diff --staged`를 사용하면 된다<br />
(이 명령은 commit한 것과 staging area에 있는 것을 비교한다)

```sh
$ git diff --staged
diff --git a/README b/README
new file mode 100644
index 0000000..03902a1
--- /dev/null
+++ b/README
@@ -0,0 +1 @@
+My Project
```

기본 도구는 vim으로 되어있으나 터미널로 출력되는게 불편하다면 아래 명령어를 통해 사용 가능한 도구를 확인하고 변경하도록 하자

```sh
$ git difftool --tool-help
```

## 변경사항 커밋하기

`$ git add .`로 모든 파일들을 staged 상태로 만들었다면 커밋을 할 수 있다

```sh
$ git commit
```

```sh
# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
# On branch master
# Your branch is up-to-date with 'origin/master'.
#
# Changes to be committed:
#   new file:   README
#   modified:   CONTRIBUTING.md
# ~ ~ ~
".git/COMMIT_EDITMSG" 9L, 283C
```

이 또한 vim인데 편집기를 변경하고 싶다면 [git의 모든것 -1](https://alpaca92.github.io/git/git의-모든-것--1/#git-최초-설정)을 확인해보도록 하자

커밋은 제목과 본문으로 이뤄져 있는데 인라인으로 간단하게 메세지를 남기고 싶다면

```sh
$ git commit -m "Story 182: Fix benchmarks for speed"
[master 463dc4f] Story 182: Fix benchmarks for speed
 2 files changed, 2 insertions(+)
 create mode 100644 README
```

위와 같이 `-m` flag를 사용하면 된다

## staging area 생략하기

보통은 수정된 파일을 모두 staging area로 올리고 commit을 하므로 이 과정을 단축하고 싶을 때가 있을 것이다

이때는 `$ git add .`대신 `-a` flag를 사용하자

```sh
$ git commit -a -m 'added new benchmarks'
[master 83e38c7] added new benchmarks
 1 file changed, 5 insertions(+), 0 deletions(-)
```

## 파일 삭제하기

파일을 그냥 지우는 것과 git 명령어를 통해 지우는 것에 대한 차이점을 알아보자

```sh
# 그냥 파일을 지울 때
$ rm PROJECTS.md
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
hanges not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        deleted:    PROJECTS.md

no changes added to commit (use "git add" and/or "git commit -a")
```

```sh
# git 명령어를 통해 지웠을 때
$ git rm PROJECTS.md
rm 'PROJECTS.md'

$ git status On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

     deleted:    PROJECTS.md
```

두 개의 차이를 알겠는가?

일반적인 방법으로의 삭제한 경우에는 unstaged 상태인 것을 알 수 있다

하지만 git 명령어로 삭제를 하면 staged 상태가 되고 이를 커밋하면 비로소 파일은 삭제된다

여러 파일을 한번에 삭제하고 싶을 때에는 file-glob형태를 사용할 수 있다

```sh
$ git rm log/\*.log
```

이 명령은 log 디렉토리안에 있는 모든 .log파일을 삭제한다

파일을 실제로 삭제하는 것이 아니라 staging area에서만 삭제하고 싶다면 어떻게 해야할까?

이때는 --cached 옵션을 사용하도록 하자

```sh
$ git rm --cached README
```

## 파일 이름 변경하기

파일 이름을 변경할 때에는 `$ git mv`명령을 사용하면 된다

```sh
$ git mv ${기존의 파일이름} ${변경할 파일이름}
```

사실 위 명렁은 아래의 과정을 단축한 명령일 뿐이다

```sh
$ mv README.md README
$ git rm README.md
$ git add README
```

이 명령을 실행하고 나면 git은 이름이 바뀐 사실을 알고 있다

```sh
$ git mv README.md README
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)
     renamed:    README.md -> README
```
