---
title: tsconfig.json 사용하기
date: 2022-05-26 10:05:66
category: typescript
draft: false
---

ts를 사용하려면 `tsconfig.json`를 사용하여 설정을 해줘야한다

물론 웹팩처럼 next.js, nest.js, CRA를 사용하면 알아서 설정을 해주기 때문에 우리가 설정할 일은 거의 없다

하지만 가끔은 내가 원하는대로 설정을 변경해줘야 될 수도 있고 해당 설정이 어떤 의미를 가지는 정도는 알아야 된다고 생각하여 정리하게 되었다

# include

_Specifies a list of glob patterns that match files to be included in compilation._

컴파일의 대상이 되는 파일 이름 혹은 패턴을 배열로 지정하면 된다

```json
{
  "include": ["src"]
}
```

# compilerOptions

_Instructs the TypeScript compiler how to compile .ts files._

.ts파일들의 컴파일 옵션을 설정하는 객체다

## outDir

설정하게 되면 해당 디렉토리로 컴파일된 파일들(`.js`, `.d.ts`, `.js.map` 등)이 들어간다

```json
{
  "compilerOptions": {
    "outDir": "dist"
  }
}
```

컴파일 시 아래와 같은 구조가 된다

```
root
├── dist
│   └── index.js
├── index.ts
└── tsconfig.json
```

## target

_Set the JavaScript language version for emitted JavaScript and include compatible library declarations._

컴파일 될 js의 버전을 설정한다

```json
{
  "compilerOptions": {
    "target": "ES6"
  }
}
```

## lib

_Specify a set of bundled library declaration files that describe the target runtime environment._

ts에게 어떤 API를 사용하고 어떤 환경에서 코드를 실행하는 지를 지정한다

```ts
{
  "compilerOptions": {
    "lib": ["ES6", "DOM"]
  }
}
```

그런데 ts는 어떻게 어떤 API를 사용하는지 알 수 있는 걸까?

이는 `declaration file`을 알아야 한다

# declaration file

우리는 개발을 할때 직접 모든 기능을 만들기 보다는 많은 프레임워크, 라이브러리를 사용한다

하지만 이러한 패키지들은 기본적으로 js로 만들어진 경우가 많으며 이를 ts환경에서 사용하기 위해서는 js파일과 모듈을 위한 타입정의가 필요하다

```
root
├── src
│   ├── index.ts
│   ├── package.js
│   └── package.d.ts
└── tsconfig.json
```

위와 같은 구조라고 할 때 `package.js`가 node_modules안에 있는 어떠한 모듈이라고 가정한다면 `.d.ts`로 해당 모듈을 정의해줘야 한다

```js
// 📁./src/package.js

export function init(config) {
  return true
}

export function exit(code) {
  return code + 1
}
```

```ts
// 📁./src/package.d.ts

interface Config {
  url: string
}

declare module 'myPackage' {
  function init(config: Config): boolean
  function exit(code: number): number
}
```

정의한 후에는 정상적으로 사용할 수 있게 된다

```ts
// 📁./src/index.ts

import { init, exit } from 'myPackage'

init({ url: 'some url' })
exit(1)
```

# JSDoc

만약 ts와 js를 혼용해서 사용해야하는 경우라면 어떨까?

모듈의 경우에는 `.d.ts`로 모든 것들의 정의 파일이 작성되어테지만 이전의 나의 프로젝트에 ts를 더하는 거라면 그렇지 않을 것이다

장기적인 관점에서는 마이그레이션(migration)을 하는 것이 좋지만 일단 몇천줄의 js코드를 당장에 마이그레이션할 순 없지만 일단 ts를 사용하고 싶다면

```json
{
  "compilerOptions": {
    "allowJs": true
  }
}
```

js를 사용할 수 있게 해주면 된다

```
root
├── src
│   ├── index.ts
│   ├── package.js
└── tsconfig.json
```

(`.d.ts`파일은 모듈의 경우이므로 이 상황에서는 없다)

allowJs를 true로 하면 이제 js코드를 ts에서 사용할 수 있는데 대부분의 타입들은 추론할 수 없는 것들이므로 any타입으로 되어있다

js파일에 타입체크를 하기 위해서는

```js
// 📁./src/package.js

// @ts-check

export function init(config) {
  return true
}

export function exit(code) {
  return code + 1
}
```

`@ts-check`를 추가하고 [JSDoc](https://www.typescriptlang.org/docs/handbook/jsdoc-supported-types.html)을 사용하면 된다

JSDoc의 문법대로 코멘트를 작성하면 ts가 이를 읽고 타입을 추론해준다

JSDoc은 코멘트로 작성되기 때문에 코드에 추가하더라도 오류가 발생하지 않는다는 장점이 있다

```js
// 📁./src/package.js

// @ts-check

/**
 * 여기에 코드에 대한 설명을 적는다
 * @param { object } config 파라미터에 대한 설명
 * @param { boolean } config.debug
 * @param { string } config.url
 * @returns { boolean } 리턴값에 대한 설명
 */
export function init(config) {
  return true
}

/**
 * 여디고 코드에 대한 설명을 적는다
 * @param { number } code 파라미터에 대한 설명
 * @returns { number } 리턴값에 대한 설명
 */
export function exit(code) {
  return code + 1
}
```

# DefinitelyTyped

어떤 패키지를 사용하다보면 `Cannot find module 'package name' or its corresponding type declarations.`라는 에러를 많이 접하게 될텐데 이는 `.d.ts`가 없는 패키지 즉, js로 된 패키지라는 말이다

이를 해결하기 위해 해당 패키지를 보면서 스스로 `.d.ts`를 작성해야 한다면 패키지를 사용하는 장점히 상당히 희석 될 것이다

따라서 이를 해결해주기 위해 [DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped)가 탄생했다

이 repository에는 다양한 패키지들의 declaration files가 작성되어 있다

패키지의 정의 파일을 받아 적용하고 싶다면 아래와 같은 명령을 실행하면 된다

```sh
# 패키지 다운로드
$ npm i ${패키지 명}

# 패키지의 정의 파일 다운로드
$ npm i -D @types/${패키지 명}
```

> 📌최근에는 처음부터 정의 파일을 포함한 패키지가 많아지고 있기 때문에 해당 오류가 없을 수도 있다

## \*references

1. [Typescript로 블록체인 만들기](https://nomadcoders.co/typescript-for-beginners)

2. [tsconfig: include](https://www.typescriptlang.org/tsconfig#include)

3. [tsconfig: outDir](https://www.typescriptlang.org/tsconfig#outDir)

4. [tsconfig: target](https://www.typescriptlang.org/tsconfig#target)

5. [tsconfig: lib](https://www.typescriptlang.org/tsconfig#lib)

6. [JSDoc](https://www.typescriptlang.org/docs/handbook/jsdoc-supported-types.html)
