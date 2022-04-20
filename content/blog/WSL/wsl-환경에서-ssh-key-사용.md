---
title: WSL 환경에서 ssh key 사용
date: 2022-04-20 15:04:71
category: WSL
draft: false
---

회사에서 제공받은 노트북으로 개인적인 공부와 회사의 업무를 병행하려다보니 깃헙 계정을 두개를 쓸일이 많아졌다

효율적인 방식을 찾다가 ssh key를 생성하여 각 디렉토리 별로 이메일과 유저명을 다르게 적용하면 좋겠다고 생각했다

내 작업 환경은 WSL이며 디렉토리 구조는 아래와 같다 <br />
(window 10, wsl2: ubuntu 20.04 lts)

📌 `${}`으로 작성된 부분은 스스로의 상황에 맞게 변경되는 값을 표시하였다

```
📁home
 ├📁ayaan
   ├📁personal
   └📁work
```

# 1. ssh key 생성

```sh
# ayaan/.ssh 폴더를 생성
$ mkdir ~/.ssh/

# ssh key를 생성, github 로그인용 email을 사용
$ ssh-keygen -t rsa -b 4096 -C "${yourEmail@email.com}"
```

`ssh key`를 생성하면 파일명과 암호를 정해야 한다

```
Enter file in which to save the key (~/.ssh/id_rsa):

Enter passphrase (empty for no passphrase):
Enter same passphrase again:
```

나 같은 경우에는 파일명을 `id_rsa_personal`로 하고 비밀번호는 엔터로 넘겼다

위의 과정을 회사에서 사용하는 email로 동일하게 한번 더 진행해 주면

```
📁home
 ├📁ayaan
   ├📁personal
   ├📁work
   └📁.ssh
     ├📝id_rsa_personal.pub
     ├📝id_rsa_personal
     ├📝id_rsa_work.pub
     └📝id_rsa_work
```

이렇게 비밀키와 공용키 두 쌍이 생성된다

# 2. ssh key를 깃헙에 등록하는 법

그 후에 깃헙에 접속해서 로그인을 한 후 오른쪽 위에 프로필 사진을 눌러 <br />
`Settings` &rarr; `🔑SSH and GPG keys` &rarr; `New SSH key`를 눌러 `Title`은 아무거나 입력하고 `Key`에 공용키를 입력해줘야 한다

📌 예를 들어 개인작업의 공용키 `id_rsa_personal.pub`의 내용을 알고 싶다면

```sh
# ubuntu
$ cat id_rsa_ersonal.pub
```

출력된 내용을 모두 복사하여 위의 `Key`부분에 붙여넣으면 된다<br />(단, 띄어쓰기 등을 임의로 변경하면 안된다)

회사 계정도 위의 과정을 똑같이 진행하였다면 키를 등록해주도록 하자

```sh
$ eval $(ssh-agent)
$ ssh-add ~/.ssh/id_rsa_personal
$ ssh-add ~/.ssh/id_rsa_work
```

📌 등록 중 아래와 같은 문구가 출력된다면 `yes`를 입력해주도록 하자

```
ECDSA key fingerprint is
SHA256: *****************************************
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

등록된 키를 한번 확인해보도록 하자

```sh
$ ssh-add -l
```

# 3. ssh key의 사용

이제 개인용 계정과 회사용 계정의 어떤 경우에 사용되는지 정의해주자

```sh
# 📁home/ayaan/.ssh
$ nano config
```

`config`파일 내에 아래와 같이 적어 준다

```
Host ${personal}
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_rsa_personal

# 회사용 계정
Host ${work}
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_rsa_work
```

📌 위의 Host `personal`과 `work`는 나중에 자주 사용되니 되도록 짧게 만들도록 하자

`config`작성이 끝났나면 아래와 같이 테스트를 진행해보는 것이 좋다

```sh
$ ssh -T git@personal
# Hi personal! You've successfully authenticated, but GitHub does not provide shell access.
```

다 끝났다 ! 이제 `git clone`, `git remote`를 할 때 `ssh key`방식을 사용하면 되는데 기본으로 주어지는 주소값은 `git@github.com:Username/RepositoryName.git`으로 되어있을 텐데 이를 `config`에서 작성하였던 `Host`로 변경해주면 된다

```sh
# git@github.com:Username/RepositoryName.git

# 개인용
$ git clone git@personal:Username/RepositoryName.git

# 직작용
$ git clone git@work:Username/RepositoryName.git
```

`git push`를 하다보면 `username`과 `email`이 개인용이든 직장용이든 똑같은 것을 알 수 있다

이는 아마 본인이 처음 생각했던 것이랑은 다를 것이다

이를 위해서는 `.gitconfig`설정을 해줘야하는데 이 설정은 [여기](https://alpaca92.github.io/git/디렉토리-별-github-계정-설정/)를 참고하도록 하자

## \*references

1. [머신 한 대에서 GIT 계정 여러개 사용하기](https://yangeok.github.io/git/2020/03/08/ssh-multiple-account.html)
2. [SSH connection to GitHub](https://coderefinery.github.io/installation/ssh/)
3. [GIT BASH 개인키 등록시 에러](https://rutesun.wordpress.com/2015/02/04/git-bash-개인키-등록시-에러/)
