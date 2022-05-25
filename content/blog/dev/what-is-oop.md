---
title: OOP(객체지향형 프로그래밍)에 대하여
date: 2022-05-18 09:05:35
category: dev
draft: false
---

요즘 트랜드는 함수형 프로그래밍(FP)인 것 같다는 생각이 든다

하지만 ES7에서 constructor와 super의 생략, arrow function 지원 등을 보면 OOP에도 많은 신경을 써주는 것 같다

유행은 돌고 도는법이고 OOP를 해야하는 언어를 사용하게 될 날도 올 수 있기 때문에 알고있는 것은 대단히 도움이 될 것이다

# OOP의 주요개념 4가지

OOP에는 `Encapsulation`, `Abstraction`, `Polymorphism`, `Inheritance`이라는 주요한 4가지 개념이 존재한다

한국어로 변역하면 `캡슐화`, `추상화`, `다형성`, `상속`이란 뜻인데 한국말로 해도 어렵다

그래서 이 4가지 개념을 좀 더 자세히 살펴보도록 하자

## Encapsulation(캡술화)

캡슐화는 데이터와 데이터를 활용하는 함수를 캡슐(혹은 컨테이너)에 넣는 것을 말하며 OOP의 경우에 캡슐은 `class`를 의미한다

예를 들어 객체와 해당 객체를 활용하는 함수가 있다고 해보자

```js
const entrepreneur = {
  firstName: 'Elon',
  lastname: 'Musk',
  shares: 17700000,
  company: 'TSLA',
}

function calcluateNetWorth(shares, company) {
  const sharePrice = getSharePrice(company)

  return shares * sharePrice
}

calcluateNetWorth(elon.shares, elon.company)
```

이를 캡슐화한다면 아래와 같이 `class`로 표현할 수 있다

```ts
class Entrepreneur {
  constructor(
    private firstName: string,
    private lastname: string,
    private shares: number,
    private company: string
  ) {}

  public calculateNetWorth() {
    return this.shares * getSharePrice(this.company)
  }
}

const elon = new Entrepreneur('Elon', 'Must', 17700000, 'TSLA')

elon.calculateNetWorth()
```

이렇게 하면 this를 사용하기 때문에 함수에 args를 받을 필요가 없어지기 때문에 좀 더 직관적인 코드를 짤 수 있으며 재사용성이 뛰어나다

> 📌 속성(`private`, `public` 등)을 활용하면 해당 클래스 외부에서는 해당 필드에 엑세스를 하거나 수정을 할 수 없게 되는 등 접근 권한을 부여할 수 있다

```ts
// not working
elon.shares = 0
elon.company = 'AMZN'
```

예를 들어 `private` 속성을 가진 이름을 얻고 싶다면

```ts
class Entrepreneur {
  // ...
  public getName() {
    return `${this.firstName} ${this.lastName}`
  }
}

const elon = new Entrepreneur('Elon', 'Must', 17700000, 'TSLA')
elon.getName()
```

위와 같이 `public` 메서드를 만들어서 얻을 수 있다

즉 **읽을 수는 없지만 쓸 수 있는 값**이 된 것이다

## Inheritance(상속)

상속은 말그대로 물려받는 것이다

일반적으로 상속이라는 의미는 부모님이 돌아가셨을 때 그 재산 등을 물려받는 것을 말한다

![Inheritance](https://raw.githubusercontent.com/Alpaca92/alpaca92.github.io/master/content/blog/dev/images/what_is_oop_01.jpg)

위 그림을 보면 패티만 있는 버거에서 다른 두 가지의 버거로 파생되었다

이렇게 파생될 수 있었던 것은 부모의 것은 그대로 받고 거기에 다른 무언가(치즈, 베이컨 등)이 추가되었기 때문이다

```ts
class Entrepreneur {
  constructor(
    private firstName: string,
    private lastName: string,
    private shares: number,
    private company: string
  ) {}
}

class Actor {
  constructor(
    private firstName: string,
    private lastName: string,
    private oscars: number,
    private age: number
  ) {}
}
```

위의 두개의 클래스를 보면 이름 부분이 겹치는데 이를 부모로 만들어주면 된다

```ts
// parent
class Person {
  constructor(private firstName: string, private lastName: string) {}

  public sayHi() {
    return `Hi, my name is ${this.firstName} ${this.lastName}`
  }
}

// children
class Entrepreneur extends Person {
  constructor(
    firstName: string,
    lastName: string,
    private shares: number,
    private company: string
  ) {
    super(firstName, lastName)
  }
}

class Actor extends Person {
  constructor(
    firstName: string,
    lastName: string,
    private oscars: number,
    private age: number
  ) {
    super(firstName, lastName)
  }
}
```

이렇게 상속을 받으면 동일하게 `Person`이 갖고 있던 것들을 모두 `Entrepreneur`, `Actor`가 갖게 되며 각각 원하는 것들을 추가할 수 있다

즉, 메서드 또한 상속을 받는다는 말이기에 아래와 같이 메서드를 사용할 수 있다

```ts
const elon = new Entrepreneur('Elon', 'Must', 17700000, 'TSLA')
const johnny = new Actor('Jonny', 'Depp', 20, 58)

elon.sayHi()
johnny.sayHi()
```

예를 통해 알아봤듯 상속을 통해 우리는 클래스들을 더 작게 쪼개어 세분화 할 수 있다

이는 재사용성이 늘어난다는 말과 동일하다 **(_divide and conquer_)**

## Abstraction(추상화)

`act of specifying a general interface hiding implementation details`

구현 세부 정보를 숨기는 일반 인터페이스를 지정하는 행위를 추상화라고 한다

한국말로 들어도 이해가 잘 되지 않는다

좀 더 쉽게 예시를 통해 알아보도록 하자

우리가 차를 운전할 때 **인터페이스**를 사용하는데 여기서 **인터페이스**란 페달, 버튼, 스티어링 휠(핸들) 등을 의미하며 이는 차량 제조사에 의해 노출되어 있다(볼 수 있다)

우리는 이런 **인터페이스**를 활용해 차를 운전할 수 있다

하지만 우리는 차량의 내부에 대한 정보는 알지 못한다

즉, 엑셀을 밟았을 때 어떤 방식을 통해 차량이 가속하는지 핸들을 돌리면 어떤 방식으로 차량이 방향전환을 하는지 등 구현 세부 정보는 노출되어 있지 않다

이를 `Abstraction`이라고 한다

이제 코드를 보며 어떻게 추상화가 이뤄지는지 알아보자

```ts
class BetterArray {
  private items: string[]

  constructor() {
    this.itmes = []
  }

  public getItems() {
    return [...this.itmes]
  }

  public addItem(item: string) {
    this.itmes.push(item)
  }

  public removeItem(itemToDelete: string) {
    this.items = this.items.filter(item => item !== itemToDelete)
  }

  public modifyItem(itemToChange: string, newValue: string) {
    const index = this.items.indexOf(itemToChange)

    if (index !== -1) this.items[index] = newValue
  }
}
```

메서드들의 이름을 보면 간단하게 어떻게 배열을 다루는 메서드인지 알 수 있다

하지만 이를 사용할 때 `filter`, `indexOf` 등의 함수를 알 필요는 없다

우리는 메서드들을 통해 추상화를 이뤄냈다

## Polymorphism(다형성)

`poli(multiple) + morphos(shape, form)`

즉 여러개의 형태라는 뜻인데 이는 `Inheritance`와 연관이 된다

우리는 부모의 것을 상속 받을 수 있다는 것을 안다

하지만 부모 클래스에서의 메서드를 오버라이딩(재정의)할 수 있는데 오버라이딩에도 규칙이 있다

예를 들면 return value type이 string이라면 오버라이딩한 메서드도 return value type이 string이어야 한다

즉, 클래스의 핵심은 그대로 있으면서 모양과 모습이 달라지는 것이다(multiple shape)

```ts
class Person {
  public sayHi() {
    return 'Hi !'
  }

  public sayBye() {
    return '✌'
  }
}

class Korean extends Person {
  // overwriting
  public sayHi() {
    return '안녕 !'
  }
}

class American extends Person {}
```

위 코드를 보면 미국인과 한국인의 `sayHi` 메서드가 다른 값을 반환하지만 그럼에도 불구하고반환값은 동일하게 `string`이다

이를 `Polymorphism`이라고 한다

## \*references

1. [개발자 교양필수? 객체지향 개념정리 10분컷.](https://www.youtube.com/watch?v=IeLWSKq0xIQ&t=27s&ab_channel=노마드코더NomadCoders)
