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

이때 git은 3-way merge를 한다

![커밋 3개를 Merge](https://raw.githubusercontent.com/Alpaca92/alpaca92.github.io/master/content/blog/git/images/evrth_of_git_07.png)

이는 단순히 브랜치 포인터를 최신 커밋으로 옮기는 것이 아니라 3-way merge의 결과를 별도의 커밋으로 만든다

![Merge 커밋](https://raw.githubusercontent.com/Alpaca92/alpaca92.github.io/master/content/blog/git/images/evrth_of_git_07.png)

## 충돌의 기초

머지가 순조롭게 되지 않을 때도 있는데 이를 충돌(comflict)이라고 한다

충돌되는 이유는 일반적으로 머지하는 두 브랜치에서 같은 파일의 한 부분을 동시에 수정하고 머지하려고 하기 때문이다

```sh
$ git merge iss53
Auto-merging index.html
CONFLICT (content): Merge conflict in index.html
Automatic merge failed; fix conflicts and then commit the result.
```

git이 자동으로 머지를 하지 못해 새 커밋도 생기지 못했다

이렇게 충돌이 발생했을 때 어떤 파일을 머지할 수 없었는지는 `$ git status`로 확인할 수 있다

```sh
$ git status
On branch master
You have unmerged paths.
  (fix conflicts and run "git commit")

Unmerged paths:
  (use "git add <file>..." to mark resolution)

     both modified:      index.html

no changes added to commit (use "git add" and/or "git commit -a")
```

충돌이 일어난 파일은 **unmerged**에 표시되고 충돌된 부분은 아래와 같이 표시된다

```
<<<<<<< HEAD:index.html
<div id="footer">contact : email.support@github.com</div> =======
<div id="footer">
 please contact us at support@github.com </div>
>>>>>>> iss53:index.html
```

=======위쪽의 내용은 HEAD 버전(위의 예시에서는 master)의 내용이고 아래쪽이 iss53버전임을 알 수 있다

충돌을 해결하려면 위쪽과 아래쪽 중 하나를 선택하거나 새로 작성하여 머지를 해줘야 한다

## 브랜치 관리

merge 유무를 간단한 명령으로 확인할 수 있다

```sh
$ git branch --merged
  iss53
* master

$ git branch --no-merged
  testing
```

아직 머지되지 않은 브랜치를 삭제하려 할 때에는 삭제가 되지 않는다

```sh
$ git branch -d testing
error: The branch 'testing' is not fully merged.
If you are sure you want to delete it, run 'git branch -D testing'.
```

> 📌 머지하지 않은 브랜치를 강제로 삭제할 때에는 `$ git branch -D ${브랜치 명}`명령으로 삭제하면 된다

## 리모트 브랜치

리모트 refs는 리모트 저장소에 있는 포인터인 레퍼런스다<br />
(리모트 저장소에 있는 브랜치, 태그 등을 의미한다)

`$ git ls-remote ${리모트 명}`명령으로 모든 리모트 refs를 조회할 수 있다

`$ git remote show ${리모트 명}`명렁은 모든 리모트 브랜치와 그 정보를 보여준다

리모트 refs가 있지만 보통 **리모트 트래킹 브랜치**를 사용한다

**리모트 트래킹 브랜치**는 리모트 브랜치를 추적하는 레퍼런스이며 브랜치다

**리모트 트래킹 브랜치**는 로컬에 있으며 리모트 서버에 연결할 때마다 리모트의 브랜치 업데이트 내용에 따라 자동으로 갱신될 뿐 임의로 움질일 수는 없다

리모트 저장소에 마지막으로 연결했던 순간에 브랜치가 무슨 커밋을 가리키고 있었는지를 나타낸다

**리모트 트래킹 브랜치**의 이름은 `${리모트 명}/${브랜치 명}`형식으로 되어있다

예를 들어 리모트 저장소 origin의 master브랜치를 보고 싶다면 `origin/master`라는 이름으로 브랜치를 확인하면 된다

![Clone 이후 서버와 로컬의 master 브랜치](https://raw.githubusercontent.com/Alpaca92/alpaca92.github.io/master/content/blog/git/images/evrth_of_git_10.png)

로컬 저장소에서 작업을 하고있는데 다른 팀원이 해당 소스에 push를 하고 master 브랜치를 업데이트 한다면 아래와 같을 것이다

![로컬과 서버의 커밋 히스토리는 독립적임](https://raw.githubusercontent.com/Alpaca92/alpaca92.github.io/master/content/blog/git/images/evrth_of_git_11.png)

리모트 저장소와 어떠한 데이터도 주고 받지 않았기 때문에 origin/master 포인터는 그 자리 그대로 있다

리모트 서버로 부터 저장소를 동기화 하기 위해서는 `$ git fetch origin`을 사용하면 된다

이 명령을 실행하면 먼저 origin서버의 주소 정보(위 그림에서는 `git.ourcompany.com`)을 찾아 현재 로컬 저장소가 갖고있지 않은 새로운 정보가 있으면 모두 내려받고

받은 데이터를 로컬 저장소에 업데이트하고 origin/master 포인터를 최신커밋 위치로 이동시킨다

![git fetch명령은 리모트 브랜치 정보를 업데이트](https://raw.githubusercontent.com/Alpaca92/alpaca92.github.io/master/content/blog/git/images/evrth_of_git_12.png)

## push 하기

로컬의 브랜치를 서버로 전송하려면 쓰기 권한이 있는 리모트 저장소에 push 해야 한다

push 명령은 `$ git push ${리모트 명} ${브랜치 명}`으로 한다

```sh
$ git push origin serverfix
Counting objects: 24, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (15/15), done.
Writing objects: 100% (24/24), 1.91 KiB | 0 bytes/s, done.
Total 24 (delta 2), reused 0 (delta 0)
To https://github.com/schacon/simplegit

 * [new branch]      serverfix -> serverfi
```

git은 serverfix라는 브랜치 이름을 `refs/heads/serverfix:refs/heads/serverfix`로 확장한다

이는 serverfix라는 로컬 브랜치를 serverfix 리모트 브랜치에 push(업데이트)한다는 것을 의미한다

로컬 브랜치의 이름과 리모트 브랜치의 이름이 다를 경우에는 `$ git push origin serverfix:anotherbranch`처럼 사용하면 된다

```sh
$ git fetch origin
remote: Counting objects: 7, done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 0), reused 3 (delta 0) Unpacking objects: 100% (3/3), done.
From https://github.com/schacon/simplegit

 * [new branch]      serverfix    -> origin/serverfix
```

여기서 짚고 넘어가야할 부분이 있는데 fetch 명렁으로 **리모트 트래킹 브랜치**를 내려받는다해도 로컬 저장소에 수정할 수 있는 브랜치가 새로 생기는 것은 아니다

다시 말해 serverfix라는 브랜치가 생기는 것이 아니라 그저 수정 못 하는 origin/serverfix브랜치 포인터가 생기는 것이다

새로 받은 브랜치의 내용을 머지하려면 `$ git merge origin/serverfix`명령을 사용한다

**리모트 트래킹 브랜치**에서 시작하는 새 브랜치를 만들려면

```sh
$ git checkout -b serverfix origin/serverfix
Branch serverfix set up to track remote branch serverfix from origin.
Switched to a new branch 'serverfix'
```

위와 같은 명령어를 사용하면 된다

## 브랜치 추적

**리모트 트래킹 브랜치**를 로컬 브랜치로 checkout하면 자동으로 "트래킹 브랜치"가 만들어진다<br />
(트래킹하는 대상 브랜치를 "upstream branch"라고 부른다)

트래킹 브랜치는 리모트 브랜치와 직접적인 연결고리가 있는 로컬 브랜치로 `$ git pull`명령으로 리모트 저장소로부터 데이터를 내려받아 연결된 리모트 브랜치와 자동으로 머지한다

추적 브랜치의 설정을 알고 싶다면

```sh
$ git branch -vv
  iss53     7e424c3 [origin/iss53: ahead 2] forgot the brackets
  master    1ae2a45 [origin/master] deploying index fix
* serverfix f8674d9 [teamone/server-fix-good: ahead 3, behind 1] this should do it
  testing   5ea463a trying something new
```

위와 같은 명령어를 사용하면 되는데 예를 들어 iss53 브랜치의 경우 현재 2개의 추적 브랜치가 있다는 것을 알 수 있다

여기서 `ahead`는 서버로 보내지 않은 커밋의 갯수를 말하며, 반대로 `behind`는 서버 브랜치에서 아직 로컬 브랜치로 머지하지 않은 커밋의 갯수를 말한다

## pull 하기

```sh
$ git fetch origin
$ git merge origin/master
```

```sh
$ git pull origin master
```

위의 두 명령은 같은 결과를 가져오지만 일반적으로 전자를 사용하는 것이 좋다

# Rebase 하기

git에서 한 브랜치를 다른 브랜치르 합치는 방법은 merge와 rebase가 있다

rebase의 장단점 및 merge와의 차이점을 살펴보자






## \*references

1. [[번역] : Git fetch와 pull, pull은 이제 그만!](https://merrily-code.tistory.com/124)
