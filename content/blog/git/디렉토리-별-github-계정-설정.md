---
title: 디렉토리 별 github 계정 설정
date: 2022-04-20 16:04:52
category: git
draft: false
---

`github`을 사용할 때 회사용, 개인용 2개의 어카운트를 사용하는 경우가 있다

나 역시도 그랬고 그렇기 때문에 디렉토리별로 `name`과 `email`이 다르길 원했다

현재 글로벌 설정은 어떻게 되어있는지 확인해보자

```sh
$ git config --list --show-origin
# file:/home/ayaan/.gitconfig  user.email=personal@email.com
# file:/home/ayaan/.gitconfig  user.name=ayaan
```

초기에 본인이 설정했던 `email`, `name`을 알 수 있고 해당 설정이 어디에 위치하는지까지 알려준다

내 경우는 `/home/ayaan/.gitconfig`에 글로벌 설정이 위치해 있다

# 1. git의 글로벌 설정과 로컬 설정

여기서 글로벌 설정과 로컬 설정의 우선순위를 알아야하는데 <br />
로컬 설정을 하는 방법은 굉장히 쉽다

```sh
#📁someDir
$ git config user.name "newName"
$ git config user.email "newEmail@email.com"
```

이렇게 설정하고 나서 확인해보면 덮어씌어진 설정 값이 출력된다

```sh
$ git config user.name
# newName

$ git config user.email
# newEmail@email.com
```

즉, 글로벌 설정과 로컬 설정이 있다면 `로컬 설정이 더 우선순위가 높다`는 것을 알 수 있다

# 2. 특정 폴더에 로컬설정을 유지하는 법

그렇다면 한번만 작성해주면 이 설정이 유지되도록 하려면 어떻게 해야 할까?

먼저 글로벌 설정 파일을 확인해보자

```sh
$ nano ~/.gitconfig
```

```
[user]
  email = personal@email.com
  name = ayaan
```

여기서 어떤 디렉토리에서는 재작성된 설정파일을 사용하겠다는 옵션을 추가해주면 된다

```
📁ayaan
  ├📁personal
  └📁work
```

내 경우에는 폴더구조가 위와 같았기 때문에 그에 맞게 경로를 설정해 줬다

```
[user]
  email = personal@email.com
  name = ayaan

[includeIf "gitdir:~/work/"]
  path = ~/work/.gitconfig
```

이제 `ayaan/work/.gitconfig`를 만들어 작성해주기만 하면 된다

```sh
#📁ayaan/work
$ nano .gitconfig
```

```
[user]
  email = work@email.com
  name = ayaan2
```

각 디렉토리에서 확인해보자

```sh
#📁auaan/personal
$ git config user.name
# ayaan

$ git config user.email
# personal@email.com
```

```sh
#📁auaan/work
$ git config user.name
# ayaan2

$ git config user.email
# work@email.com
```

## \*references

1. [GitHub 멀티 어카운트를 사용할 때 유용한 Git 설정](https://www.lainyzine.com/ko/article/useful-git-settings-when-using-github-multi-account/)
