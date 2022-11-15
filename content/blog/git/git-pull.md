---
title: git pull할때마다 나오는 hint 해결하기
date: 2022-09-27 08:09:65
category: git
draft: false
---

최근에 `git pull`을 하면 멋대로 머지 내역을 커밋하여 로그가 쓸데없이 많아지고 있다

혼자서 관리중인 repository라면 상관없겠지만 업무용 repository라면 다른 팀원들이 이 때문에 불편을 겪을 수 있다

# what is git pull

`git pull --help`로 `git pull`에 대한 도움말을 보면

> If the current branch is behind the remote, then by default it will fast-forward the current branch to match the remote. If the current branch and the remote have diverged, the user needs to specify how to reconcile the divergent branches with --rebase or --no-rebase (or the corresponding configuration option in pull.rebase).

`fast-forward` 관계의 경우 빠르게 병합되지만 그렇지 않은 경우에는 `--rebase`, `--no-rebase`의 옵션을 사용하여야 한다고 설명한다

보다 정확하게는 `git pull = git fetch + git merge(or git rebase)`라고 할 수 있다

# git pull hint

현재 `git pull`을 하면 아래와 같은 메세지가 계속 출력된다

```sh
$ git pull

hint: Pulling without specifying how to reconcile divergent branches is
hint: discouraged. You can squelch this message by running one of the following
hint: commands sometime before your next pull:
hint:
hint:   git config pull.rebase false  # merge (the default strategy)
hint:   git config pull.rebase true   # rebase
hint:   git config pull.ff only       # fast-forward only
hint:
hint: You can replace "git config" with "git config --global" to set a default
hint: preference for all repositories. You can also pass --rebase, --no-rebase,
hint: or --ff-only on the command line to override the configured default per
hint: invocation.
```

그리고 커밋메세지도 함께 작성된다

```sh
Merge branch 'some_branch' of *** into some_branch
```

위와 같은 일이 일어나는 이유를 알아보기 위해 `git pull -h`로 설명을 보도록 하자

```sh
$ git pull -h

# (전략)
# --commit              perform a commit if the merge succeeds (default)
# (후략)
```

`git pull`은 merge가 성공하면 그에 대한 commit을 진행하는 것이 기본 값이다

따라서 원치 않게 커밋메세지가 쌓이는 것이다

# 해결방법

위 현상은 원격 저장소와 로컬 저장소 모두 새로운 커밋을 가지고 있을 때 `Non fast-forward`가 발생하기 때문이다

이러한 병합 커밋을 만들지 않으려면 `--rebase`옵션으로 병합을 시키면 된다(단, 이때 스테이징되지 않은 변경사항이 있으면 안된다)

```sh
git pull --rebase
```

이를 매번 하는 것은 귀찮으니 글로벌 설정에 `--rebase`옵션을 넣어주면 앞으로 `git pull`만으로도 동일한 일을 수행할 수 있게된다

```sh
gitconfig --global pull.rebase ture
```

# git pull options

위에서 살펴본 `--rebase` 이외에도 `--ff-only`, `--no-rebase`라는 옵션이 존재한다

그렇다면 이 옵션들은 각각 무슨 역할을 하는걸까?

## git pull --ff-only

이는 `fast-forward only`라는 뜻이며, `fast-forward`란 간단하게 말해 직접적으로 연결이 되어있는 관계를 말한다

```sh
# commit B는 commit D의 fast-forward
A ⎯ B ⎯ C ⎯ D

# commit F에 commit D의 history가 포함되지 않기 때문에 non fast-forward
A ⎯ B ⎯ C ⎯ D
         ⎿ E ⎯ F
```

따라서 `--ff-only` 옵션을 사용하게 되면 `fast-forward`일 때에만 git pull을 실행하며, `Non fast-forward`일 때에는 오류가 발생한다

`* branch master -> FETCH_HEAD 593a5e1..208efa3 master -> origin/master fatal: Not possible to fast-forward, aborting`

이 경우에는 `git rebase`, `git merge`등으로 해당 관계를 정리해줘야 한다

> 🍪 원격 저장소로부터 가져온 모든 브랜치의 HEAD를 .git/FETCH_HEAD에 기록하며, FETCH_HEAD는 원격 저장소로부터 가져온 브랜치의 HEAD를 의미한다

## git pull --no-rebase

위 옵션은 **기본 값**으로 원격 저장소와 로컬 저장소의 형상이 다른 경우 이를 merge하고 commit을 남긴다

## \*references

1. [git: Why "Merge branch 'master' of ... "? when pull and push](https://stackoverflow.com/questions/15439527/git-why-merge-branch-master-of-when-pull-and-push)

2. [Git 사용 중 자주 만나는 이슈 정리](https://parksb.github.io/article/28.html)

3. [도식으로 보는 git-merge의 --ff, --no-ff, --squash의 차이점](https://blog.naver.com/parkjy76/220308638231)

4. [Git: pull strategy(전략, 종류)](https://wookkl.tistory.com/46)
