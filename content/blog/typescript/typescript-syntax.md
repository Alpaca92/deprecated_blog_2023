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
type Add = (a: number, b: number) => number

const add: Add = (a, b) => a + b
```

이는 아래와 같다

```ts
const add = (a: number, b: number): number => a + b
```

하지만 전자의 경우가 분리되어 있어 좀 더 깔끔하고 가독성이 좋아보인다

## overloading

오버로딩이란 함수가 여러개의 call signatures를 갖고 있는 경우를 말한다

우리는 어떠한 라이브라리를 사용하는 경험을 굉장히 많이 하는데 해당 라이브러리에서 함수의 args는 항상 같은 타입이 아닐 수 있다

예를 들어 nextjs에서 `Router.push()`는 string을 받을 수도 있고 obj를 받을 수도 있다

```ts
Router.push('/profile')

// or

Router.push({
  pathname: '/profile',
  query: { name: 'someone' },
})
```

이렇게 다양한 args를 어떻게 핸들링 할 수 있을까?

```ts
type Config = {
  pathname: string
  qeury: {
    name: string
  }
}
type Push = {
  (path: string): void
  (config: Config): void
}

const push: Push = config => {
  // (parameter) config: string | Config
  if (typeof config === 'string') {
    console.log(config)
  } else {
    console.log(config.pathname)
  }
}
```

물론 args의 갯수가 다른 경우에도 가능하다

```ts
type Add = {
  (a: number, b: number): number
  (a: number, b: number, c: number): number
}

const add: Add = (a, b, c?: number) => {
  if (typeof c === 'number') return a + b + c

  return a + b
}
```

## Polymorphism

**poly(several, much, multi-) + morphos(form, structure)**의 합성어로 즉 여러가지의 형태를 의미한다

> 📌자세한 polymorphism의 의미에 대해서는 이전에 작성한 [OOP에 대하여](https://alpaca92.github.io/dev/what-is-oop/#polymorphism다형성)를 참고하도록 하자

배열을 받고 각 배열의 요소를 모두 출력해주는 함수를 만들어보자

```ts
type Printer = {
  (arr: number[]): void
  (arr: boolean[]): void
}

const printer: Printer = arr => {
  arr.forEach(i => console.log(i))
}
```

위 코드에서 문제는 `string[]`, `[string, boolean, number]` 등 원하는 형태의 배열을 함수에 사용할 수 없다는 문제가 있다

여기서 해결방안으로는 `any[]`를 사용하는 방법도 있다

하지만 `any`라는 타입을 남용하면 결국 js와 ts의 차이가 없어지게 되므로 이를 위해 사용되는 것이 [Generics](https://www.typescriptlang.org/docs/handbook/2/generics.html)이다

제네릭을 사용하는 경우는 call signatures를 사용할 수 없는 즉, 명확하게 케이스를 나눌 수 없을때 사용한다

```ts
type Printer = {
  <T>(arr: T[]): void
}

const printer: Printer = arr => {
  arr.forEach(i => console.log(i))
}

// const printer: <string | number | boolean>(arr: (string | number | boolean)[]) => void
printer([1, 2, 3, true, 'false'])
```

> 📌 `number`, `string` 같은 타입을 **concrete type**이라고 하며 포괄적인 타입을 **generic**이라고 한다

return 타입에 대해서도 제네릭을 사용할 수 있다

```ts
type Printer = {
  <T>(arr: T[]): T
}

const printer: Printer = arr => arr[0]

const a = printer([1, 2, 3, 4]) // const a: number
const b = printer(['1', 2, 3, 4]) // const b: string | number
const c = printer([true, 2, 3, 4]) // const a: boolean | number
```

> 📌`any`와 `generics`의 차이는 무엇인가? _[참고1](https://www.typescriptlang.org/docs/handbook/2/generics.html#hello-world-of-generics)_, _[참고2](https://stackoverflow.com/questions/44023061/what-are-the-difference-between-generic-typet-vs-any-in-typescript)_<br /> > <br />
> 'any'를 사용하는 것은 어떤 타입이든 받을 수 있다는 점에서 'generics'과 같지만 함수를 반환하는데 있어 'any'는 받았던 인수들의 타입을 활용하지 못한다<br />
> 즉, 'generics'은 어떤 타입이든 받을 수 있다는 점에서 'any'와 같지만 해당 정보를 잃지 않고 타입에 대한 정보를 다른 쪽으로 전달할 수 있다는 점이 다르다

여태까지는 타입과 함수의 선언을 따로 분리했는데 합쳐서 한번에 선언할 수도 있다

```ts
function printer<T>(arr: T[]) {
  return a[0]
}

// or

const printer = <T>(arr: T[]) => a[0]
```

# Classes

ES6에서 클래스가 생기면서 js는 보다 나은 OOP 구현 환경이 생겼고 ts는 이를 도와주는 여러 기능들이 있기에 그 부분에 대해 다뤄보려 한다

> 🔥 C#, Java 등 OOP를 구현해야 하는 언어를 위해서라도 공부해두는 것이 좋다고 생각한다

```ts
class Player {
  constructor(
    private firstName: string,
    private lastName: string,
    public nickname: string
  ) {}
}

const ayaan = new Player('ayaan', 'yim', '이안')

ayaan.firstName // error: Property 'firstName' is private and only accessible within class 'Player'.
```

## abstract

추상클래스(abstract class)는 다른 클래스가 상속받을 수 있는 클래스다<br />
(instance를 직접 만드는 것은 불가능하다)

```ts
abstract class User {
  constructor(
    private firstName: string,
    private lastName: string,
    public nickname: string
  ) {}

  public getFullName() {
    return `${this.firstName} ${this.lastName}`
  }
}

// error: Cannot create an instance of an abstract class.
new User('ayaan', 'yim', '이안')

class Player extends User {}

const ayaan = new Player('ayaan', 'yim', '이안')

console.log(ayaan.getFullName()) // ayaan yim
```

> 📌 `private`, `public`, `protected`의 차이
>
> `private` : private로 선언한 클래스 내에서만 접근할 수 있으며, 상속을 받은 클래스 및 인스턴스는 접근할 수 없다
>
> `public` : 상속을 받은 클래스 및 인스턴스에서 자유롭게 접근할 수 있다
>
> `protected` : 상속을 받은 클래스 내에서는 접근할 수 있지만 인스턴스에서는 접근할 수 없다

|구분|선언한 클래스 내에서 접근|상속받은 클래스 내에서 접근|인스턴스에서 접근|
|:-:|:----------------------:|:----------------------:|:--------------:|
|private| ⭕ | ❌ | ❌ |
|public| ⭕ | ⭕ | ⭕ |
|protected| ⭕ | ⭕ | ❌ |

추상클래스를 사용하면 추상메서드(abstract method)를 사용할 수 있는데 이는 추상클래스를 상속받는 모든 것들이 필수로 구현해야하는 메소드를 말한다

```ts
abstract class User {
  constructor(
    private firstName: string,
    private lastName: string,
    public nickname: string
  ) {}

  abstract getNickName(): void

  public getFullName() {
    return `${this.firstName} ${this.lastName}`
  }
}

class Player extends User {
  getNickName() {
    return nickname
  }
}
```

인스턴스를 생성 시 받아올 값이 없다면

```ts
type Words = {
  [key: string]: string
}

class Dict {
  private words: Words

  constructor() {
    this.words = {}
  }

  // class를 type처럼 사용할 수 있다
  add(word: Word) {
    if (this.words[word.term] === undefined) this.words[word.term] = word.def
  }

  def(term: string) {
    return this.words[term]
  }
}

class Word {
  constructor(public term: string, public def: string) {}
}

const dict = new Dict()
const kimchi = new Word('kimchi', 'Korean food')
dict.add(kimchi)
dict.def('kimchi')
```

위와 같은 식으로 코드를 짜줄 수도 있다

Dict 클래스는 constructor의 args가 없기 때문에 외부에서 받아오는 값은 없고 내부에서 `this.words = {}`를 통해 words를 정의한다

> 📌 클래스 내에서는 암묵적으로 최상위에 this 객체를 생성한다<br />
> 즉, Dict 클래스 최상위에 `this = {}`가 생성되고 constructor의 body로 인해 `this = { words : {} }`가 된다

# interfaces

인터페이스는 타입과 비슷하지만 조금의 차이점이 있다

```ts
type Team = 'red' | 'yellow' | 'blue'

type Player = {
  nickname: string
  team: Team
}

const ayaan: Player = {
  nickname: 'ayaan',
  team: 'red',
}
```

```ts
type Team = 'red' | 'yellow' | 'blue'

interface Player {
  nickname: string
  team: Team
}

const ayaan: Player = {
  nickname: 'ayaan',
  team: 'red',
}
```

타입의 경우 `type Team = "red" | "yellow" | "blue"` 같이 특정 값을 가질수도 있지만 인터페이스의 경우 객체를 특정해주기 위한 것이다

또한 인터페이스는 누적이 가능하다

```ts
interface User {
  name: string
}

interface User {
  age: number
}
```

위 코드는 아래와 같다

```ts
interface User {
  name: string
  age: number
}
```

타입과 인터페이스 모두 확장이 가능한데

```ts
interface User {
  name: string
}

interface Player extends User {
  age: number
}

const ayaan: Player = {
  name: 'ayaan',
  age: 29,
}
```

```ts
type User = {
  name: string
}

type Player = User & {
  age: number
}

const ayaan: Player = {
  name: 'ayaan',
  age: 29,
}
```

[typescript type vs interface 더 알아보기](https://stackoverflow.com/questions/37233735/interfaces-vs-types-in-typescript)

추상화를 통한 OOP는 아래와 같은 식으로 코드를 작성하였다

```ts
abstract class User {
  constructor(protected firstName: string, protected lastName: string) {}

  abstract sayHi(name: string): string
  abstract fullName(): string
}

class Player extends User {
  fullName() {
    return `${this.firstName} ${this.lastName}`
  }

  sayHi(name: string) {
    return `Hello ${name} ! My name is ${this.fullName()}`
  }
}
```

이를 인터페이스를 사용하여 작성해보면

```ts
interface User {
  firstName: string
  lastName: string
  sayHi(name: string): string
  fullName(): string
}

class Player implements User {
  constructor(public firstName: string, public lastName: string) {}

  fullName() {
    return `${this.firstName} ${this.lastName}`
  }

  sayHi(name: string) {
    return `Hello ${name} ! My name is ${this.fullName()}`
  }
}
```

위 코드와 같다

이 둘의 차이점은 먼저 인터페이스를 사용하면 private, protected 변수를 만들 수 없다

하지만 컴파일하여 js파일로 만들때 implements는 흔적이 남지 않아 좀 더 가벼운 파일을 만들어준다<br />
(추상 클래스의 경우 컴파일시 js에서 일반 클래스로 변환 됨)

# result

여태까지 배웠던 것을 바탕으로 브라우저에서 쓰는 로컬 스토리지를 ts로 표현해보도록 하자

```ts
interface IStorage<T> {
  [key: string]: T
}

class LocalStorage<T> {
  private storage: IStorage<T> = {}

  set(key: string, value: T) {
    this.storage[key] = value
  }

  remove(key: string) {
    delete this.storage[key]
  }

  get(key: string): T {
    return this.storage[key]
  }

  clear() {
    this.storage = {}
  }
}

const stringsStorage = new LocalStorage<string>()
```

제네릭의 경우 위 코드와 같이 아래로 전달 될 수 있다

이제 ts에 대한 기본 개념들은 다 배운 것 같다

좀 더 자세히 알고 싶다면 아래의 `references`를 참고하길 바란다

## \*references

1. [Typescript로 블록체인 만들기](https://nomadcoders.co/typescript-for-beginners)

2. [The TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/intro.html)

3. [한눈에 보는 타입스크립트(updated)](https://heropy.blog/2020/01/27/typescript/)

4. [What is the syntax for Typescript arrow functions with generics?](https://stackoverflow.com/questions/32308370/what-is-the-syntax-for-typescript-arrow-functions-with-generics)
