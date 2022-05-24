---
title: typescript 기초
date: 2022-05-24 08:05:73
category: typescript
draft: false
---

cra에 ts 템플릿이 있어서 사용해보면서 문법에 대한 이해가 없이 하느라 많은 삽질(?)이 있어서 이참에 배워두려고 강의를 찾아보았다

블로깅은 내가 개발자가 되게 해준 [노마드 코더](https://nomadcoders.co/)에서 나온 무료강의를 들으면서 작성했다

# implicit types vs explicit types

```ts
let a = 'hello'
a = 'bye'
a = 1 // error: Type 'number' is not assignable to type 'string'.
```

위 코드를 보면 a의 타입은 암묵적으로 string이 된다

이를 number 타입으로 변경하려고 하면 에러가 발생한다

즉, ts는 변수에 한번 타입을 지정하면 바꿀 수 없다는 것을 알 수 있다

그래서 위 코드처럼 암묵적이 아닌 변수에 타입을 명시적으로 지정해줄 수도 있다

```ts
let b: boolean = true
b = 'hello' // error: Type 'string' is not assignable to type 'boolean'.
```

전자의 경우 type checker에 의해 암묵적으로 변수의 타입이 결정되기 때문에 프로그래머의 의도와 조금 다를 수도 있다

따라서 대부분의 경우 명시적인 타입선언을 사용한다

명시적 타입선언에 대해 좀 더 살펴보자면

```ts
// array
const arr: number[] = [1, 2, 3, 4]

// object
const player: {
  name: string
} = {
  name: 'ayaan',
}

// function
function playerMaker(name: string): { name: string } {
  return {
    name,
  }
}

// arrow function
const playerMaker = (name: string): { name: string } => {
  return {
    name,
  }
}

// tuple
const player: [string, number, boolean] = ['ayaan', 29, true]
```

물론 optional type을 선언할 수도 있다

```ts
const player: {
  name: string
  age?: number
} = {
  name: 'ayaan',
}
```

여기서 age는 number 타입이거나 undefined라는 것을 ts에게 알려주기 위해 `?`를 붙여준다

# Alias

비슷한 구조의 객체가 계속 있다고 한다면 타입을 일일이 지정하기에는 너무 효율적이지 못하다

따라서 재사용 가능하게 만들어줄 필요가 있다

```ts
type Player = {
  name: string
  age?: number
}

const ayaan: Player = {
  name: 'ayaan',
}

const anonymous: Player = {
  name: 'anonymous',
  age: 19,
}
```

# Readonly

```ts
type Player = {
  readonly name: string
  age?: number
}

const playerMaker = (name: string): Player => ({ name })
const ayaan = playerMaker('ayaan')
ayaan.name = 'anonymous' // error: Cannot assign to 'name' because it is a read-only property.
```

위 코드와 같이 특정 프로퍼티를 읽기전용으로 만들 수도 있다

```ts
const numbers: readonly number[] = [1, 2, 3, 4]
numbers.push(5) // error: Property 'push' does not exist on type 'readonly number[]'.
```

혹은 위와 같이 배열이나 객체에 값을 추가/제거 등의 가공을 할 수 없게 만들 수도 있다

# typescript에만 있는 타입

여태까지는 js에서 사용하던 타입들을 배웠다<br />
(`readonly`는 타입이 아니라 데이터의 형식이다)

하지만 ts에는 js에는 없는 데이터 타입들이 있다

이를 잘 쓴다면 좀 더 안정성있는 서비스를 만들 수 있을 것이다

## unknown

API와 소통을 하는데 해당 API가 어떤 타입을 줄지 모를때 이럴때 사용하는 것이 `unknown`타입이다

```ts
let a: unknown

if (typeof a === 'number') {
  const b = a + 1
} else if (typeof a === 'string') {
  const b = a.toUpperCase()
}
```

즉, 어떤 변수의 타입을 미리 알지 못할 때 `unknown`을 사용하여 나중에 해당 변수를 사용할 때 타입 별로 프로세스를 나눌 때 사용한다

## void

값을 반환하지 않는 함수의 반환 값을 표현하는 것이 `void`다

```ts
function hello(): void {
  console.log('hello, world!')
}
```

혹은

```ts
let result = 0

function counter(operation: string) {
  if (operation === 'plus') {
    ++result
  } else if (operation === 'minus') {
    --result
  }
}
```

## never

일부 함수는 값을 반환하지 않을 때가 있는데 이는 함수가 예외를 throw하거나 실행을 종료하기 때문인데 이때 사용하는 것이 `never`타입이다

```ts
function hello(): never {
  throw new Error('this is ERROR')
}
```

# function

ts에서 함수를 사용하기 위해선 여러가지 추가개념들이 필요하다

## call signatures

함수의 매개 변수(params)와 반환 타입을 지정하는 것을 말한다

```ts
type Add = (a: number, b: number) => number;

const add: Add = (a, b) => a + b;
```

이는 아래와 같다

```ts
const add = (a: number, b: number): number => a + b;
```

하지만 전자의 경우가 분리되어 있어 좀 더 깔끔하고 가독성이 좋아보인다

## overloading













## \*references

1. [Typescript로 블록체인 만들기](https://nomadcoders.co/typescript-for-beginners)

2. [The TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/intro.html)

3. [한눈에 보는 타입스크립트(updated)](https://heropy.blog/2020/01/27/typescript/)
