---
title: 윈도우에서 zsh 설치하기
date: 2022-06-02 21:06:43
category: dev
draft: false
---

내가 WSL2를 사용했던 이유는 리눅스가 필요해서가 아니라 zsh이 예뻐서였다

코딩하는데 있어서 편리함도 중요하지만 아름다움도 중요하다고 생각한다

예를 들어 cmd나 powershell을 보면 command를 입력할 맛이 안난다<br />
(진짜 입맛까지 싹 사라진다)

근데 직장에서는 보안이슈로 WSL2를 사용하기 힘든 환경이라 회사 PC와 집에서 사용하는 개인 PC의 개발 환경을 통일시키기 위해 윈도우10에서 zsh을 설치하는 방법을 찾아봤다

# zsh

zsh(Z shell)은 Bourne Shell(sh)의 확장버전으로 새로운 기능들과 여러가지 테마를 지원한다

bash와 상당히 많은 점을 공유하고 있는데 왜 zsh을 사용해야 할까?

bash에는 없는 기능 중 내가 가장 편하다 느꼈던 것은

1. **Recursive path expansion**

```sh
$ code b/a

# tab을 누르면 아래와 같이 재귀적으로 확장해준다

$ code blog/assets
```

2. **Plugin and theme support**

```
zsh에는 다양한 테마가 존재해 꾸미는 맛(?)이 상당하다
```

이외에도 다양한 차이점들이 존재한다

zsh을 사용할 때에 빠지지 않고 등장하는 것이 바로 `Oh-My-Zsh`인데 zsh의 프레임워크 중 하나이다

이는 굉장히 많은 플러그인과 테마가 내장되어 있어 가장 사랑받는 zsh의 프레임워크 중 하나이다

# zsh 설치

간단하게 zsh의 설치법과 테마(p10k)를 설치 및 설정하는 법에 대해 알아보도록 하자

## windows terminal 설치

나는 cmd랑 powershell이 너무 못생겨서(?) 극혐하기 때문에 windows terminal을 설치했다<br />
(cmd &rarr; powershell &rarr; windows terminal 순으로 생겼다)

> 📌 좀 더 자세한 내용을 알고싶다면 [여기](https://techwiser.com/command-prompt-vs-powershell-vs-windows-terminal-comparison)를 참고하도록 하자

windows terminal 설치는 [microsoft store에서 설치](https://docs.microsoft.com/en-us/windows/terminal/install#install)를 하거나 chocolatey를 사용하여 설치할 수 있다

## chocolatey 설치

[chocolatey 설치 페이지](https://chocolatey.org/install#install-step2)에 접속해서 설치 명령을 powershell이나 cmd에 복붙하면 된다<br />
(powershell 혹은 cmd는 관리자 권한으로 실행해야 한다)

```sh
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
```

chocolatey 설치가 끝났으면 원하는 패키지를 설치하면 된다

```sh
$ choco install microsoft-windows-terminal -y
$ choco install git -y
```

## windows terminal 설정

설치가 끝났으면 git bash를 기본 터미널로 사용하기 위해 windows terminal을 실행하고 `Ctrl + ,`로 설정에 들어가서 왼쪽 아래의 `Json 파일 열기`를 눌러서 기본값을 설정해준다

```sh
"defaultProfile": "{124fc1da-dadc-4276-9c4e-f0524ba57a49}",
"profiles": {
  "defaults": {
    "fontFace": "MesloLGS NF",
    "fontSize": 10
  },
"list": [
  ...
  ]
}
```

`defaultProfile`은 windows terminal을 실행하면 기본으로 켜지는 터미널 종류를 정하는 값이고

`profiles > defaults`안에서 폰트와 폰트 크기를 설정하는 값이다

현재는 `MesloLGS NF`라는 폰트가 없을텐데 이는 `powerlevel10k`를 정상적으로 사용하기 위해 설치해야 한다

> 폰트의 경우에는 [여기](https://github.com/romkatv/powerlevel10k/blob/master/font.md#manual-font-installation)에서 `.ttf`파일을 다운받아 설치해주면 된다<br />
> (나는 볼드, 이탈릭 등 모든 종류를 다 설치해줬다)

## zsh 설치

[여기](https://packages.msys2.org/package/zsh?repo=msys&variant=x86_64)에서 zsh압축파일을 다운받은 후 Git이 설치된 폴더에 넣어주면 된다<br />
(통상적으로 `📁C:\Program Files\Git`경로에 설치되므로 이 경로에 넣어주면 된다)

설치 후에 `git bash`를 실행하여

```sh
$ zsh
```

을 입력하면 zsh이 실행된다

## Oh-My-Zsh 설치

이제 zsh에서 oh-my-zsh을 설치해보도록 하자

```sh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

## zsh을 기본 shell로 설정하기

이제 zsh이 기본 shell이 되도록 설정해보도록 하자

```sh
$ nano ~/.bash_profile
```

nano 에디터를 통해 `.bash_profile`에 아래의 코드를 추가한다

```sh
# zsh configuration
# when you open git bash it will change to zsh
if [ -t 1 ]; then
exec zsh
fi
```

그 후 `Ctrl + X` &rarr; `Y`를 통해 저장하면 이제 windows terminal을 키면 자동으로 zsh이 켜지게 된다

## powerlevel10k(p10k) 설치

zsh을 좀 더 이쁘게 꾸미기 위해 p10k를 설치해보자

먼저 [여기](https://github.com/romkatv/powerlevel10k#oh-my-zsh)에서 제공하는 repository를 클론하고

```sh
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
```

`.zshrc`에 아래와 같이 테마 설정을 추가해주면 된다

```sh
$ code ~/.zshrc
```

```js
// 📁.zshrc

ZSH_THEME = 'powerlevel10k/powerlevel10k'
```

그리고 다시 windows terminal을 켜면 테마 설정을 할 수 있는데 취향에 맞게 설정하면

> 📌 설정을 변경하고 싶으면 `$ p10k configure`을 입력하면 된다

## \*references

1. [What is ZSH, and Why Should You Use It Instead of Bash?](https://www.howtogeek.com/362409/what-is-zsh-and-why-should-you-use-it-instead-of-bash/)

2. [How to Change Default Font in Windows Terminal](https://windowsloop.com/how-to-change-default-font-in-windows-terminal/)

3. [install zsh on windows 10](https://gist.github.com/fworks/af4c896c9de47d827d4caa6fd7154b6b)

4. [powerlevel10k font](https://github.com/romkatv/powerlevel10k/blob/master/font.md)

5. [powerlevel10k install](https://github.com/romkatv/powerlevel10k#oh-my-zsh)
