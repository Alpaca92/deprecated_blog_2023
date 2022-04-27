---
title: node version 자동으로 변경하기
date: 2022-04-27 09:04:78
category: dev
draft: false
---

블로그 템플릿의 경우 node version이 `v14.x`여야 동작한다

하지만 다른 프로젝트의 경우 최신 lts version인 `v16.x`를 사용하고 있다

그래서 블로그 디렉토리를 들어올때마다

> 📌 `${}`로 표기된 곳은 본인의 상황에 맞게 변경해야 되는 값이다

```sh
$ nvm use v14.16.0
```

으로 node version을 변경해주는게 너무 귀찮아서 어떻게하면 자동으로 변경할 수 있을까 찾아보게 되었다

방법은 2가지였는데 하나는 반자동(?), 나머지 하나는 자동으로 변경되는 방법이었다

# 1. 반자동(?)으로 디렉토리별로 node version 변경하는 방법

진짜 별거 없이 node version이 변경되길 바라는 디렉토리에 `.nvmrc`파일을 생성해주고 원하는 버전을 적어주면 된다

```sh
$ code ${targetDir}
$ touch .nvmrc
```

```nvmrc
${14.16.0}
```

이렇게 하면 해당 폴더에서 작업할 때 `.nvmrc`안에 적혀있는 버전을 아래와 같이 불러오면 된다

```sh
#📂targetDir
$ nvm use
```

# 2. 자동으로 디렉토리별로 node version 변경하는 방법

사실 이건 1번의 응용에 지나지 않는다

`.nvmrc`파일의 내부에 버전을 읽어와 자동으로 `nvm use`를 해주는 것 뿐이기 때문이다

내 경우에는 `zsh`을 사용하고 있기 때문에 다음과 같은 코드를 `.zshrc`에 넣어주면 된다

```sh
$ code ~/.zshrc
```

```
# place this after nvm initialization!
autoload -U add-zsh-hook
load-nvmrc() {
  local node_version="$(nvm version)"
  local nvmrc_path="$(nvm_find_nvmrc)"

  if [ -n "$nvmrc_path" ]; then
    local nvmrc_node_version=$(nvm version "$(cat "${nvmrc_path}")")

    if [ "$nvmrc_node_version" = "N/A" ]; then
      nvm install
    elif [ "$nvmrc_node_version" != "$node_version" ]; then
      nvm use
    fi
  elif [ "$node_version" != "$(nvm version default)" ]; then
    echo "Reverting to nvm default version"
    nvm use default
  fi
}
add-zsh-hook chpwd load-nvmrc
load-nvmrc
```

`bash`, `fish` 등은 아래의 references를 참조하도록 하자

## \*references

1. [How to write a .nvmrc file which automatically change node version](https://readforlearn.com/how-to-write-a-nvmrc-file-which-automatically-change-node-version/)

2. [Node Version Manager](https://github.com/nvm-sh/nvm#nvmrc)
