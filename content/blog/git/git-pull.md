---
title: git pull
date: 2022-09-27 08:09:65
category: git
draft: true
---

최근에 `git pull`을 하면 멋대로 머지 내역을 커밋하여 로그가 쓸데없이 많아지고 있다

혼자서 관리중인 repository라면 상관없겠지만 업무용 repository라면 다른 팀원들이 이 때문에 불편을 겪을 수 있다

현재 `git pull을 하면 아래와 같은 메세지가 계속 출력된다

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

이는 원격 저장소와 로컬 저장소 모두 새로운 커밋을 가지고 있을 때 `Non fast-forward`가 발생하기 때문이다

이러한 병합 커밋을 만들지 않으려면 `--rebase`옵션으로 병합을 시키면 된다(단, 이때 스테이징되지 않은 변경사항이 있으면 안된다)

```sh
git pull --rebase
```

이를 매번 하는 것은 귀찮으니 글로벌 설정에 `--rebase`옵션을 넣어주면 앞으로 `git pull`만으로도 동일한 일을 수행할 수 있게된다

```sh
gitconfig --global pull.rebase ture
```

## \*references

1. [git: Why "Merge branch 'master' of ... "? when pull and push](https://stackoverflow.com/questions/15439527/git-why-merge-branch-master-of-when-pull-and-push)

2. [Git 사용 중 자주 만나는 이슈 정리](https://parksb.github.io/article/28.html)
