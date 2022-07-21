---
title: useCallback vs named function
date: 2022-06-28 10:06:00
category: react
draft: false
---

Reack Hooks 중 `useCallback`과 일반적인 함수의 차이는 무엇일까?

공식문서를 보면

> Pass an inline callback and an array of dependencies.<br /> > `useCallback` will return a memoized version of the callback<br />
> that only changes if one of the dependencies has changed.<br />
> This is useful when passing callbacks to optimized child components<br />
> that rely on reference equality to prevent unnecessary renders<br />
> (e.g. `shouldComponentUpdate`).

'메모이제이션된 함수를 반환하며, 이 메모이제이션된 함수는 의존성이 변경되었을 때에만 변경된다'고 나와있다

즉, 불필요한 렌더링을 방지하기 위한 함수를 위한 hooks이다

그렇다면 모든 함수를 `useCallback`으로 감싸준다면 메모리의 낭비를 최소화하는 것이 아닐까?

왜 사람들은 모든 함수를 이렇게 사용하지 않는걸까?

# 함수의 동등비교 이해하기

`useCallback`을 사용하기 전에 먼저 `useCallback`이 함수가 변경되는 것을 어떻게 알아차리는지에 대해 알아보자

```js
function factory() {
  return (a, b) => a + b
}
const sum1 = factory()
const sum2 = factory()
sum1(1, 2) // => 3
sum2(1, 2) // => 3
sum1 === sum2 // => false
sum1 === sum1 // => true
```

`sum1`, `sum2`는 `factory()`에 의해 만들어진 함수지만 이를 동등비교하면 다른 함수 객체인 것을 알 수 있다

이는 자바스크립트 객체는 자기 자신과 비교할 때에만 true를 return한다는 것을 알 수 있다

# useCallback()의 목적

동일한 코드를 공유하는 서로 다른 함수 객체는 일반적으로 리엑트 컴포넌트 안에서 생성된다

```js
function MyComponent() {
  // handleClick는 각각의 렌더링에서 재생성된다
  const handleClick = () => {
    console.log('Clicked!')
  }
  // ...
}
```

`handleClick`은 `MyComponent`가 렌더링 될 때 마다 다른 함수객체가 된다

하지만 위 예시의 경우 간단한 함수이기 때문에 각 렌더링때마다 함수가 다시 생성되어도 크게 문제되지는 않는다

```js
import { useCallback } from 'react'
function MyComponent() {
  // handleClick이 동일한 함수 객체를 반환한다
  const handleClick = useCallback(() => {
    console.log('Clicked!')
  }, [])
  // ...
}
```

위와 같이 `useCallback`을 사용하여 동일한 함수 인스턴스를 반환하여야 하는 경우는 대개 다음과 같다

1. 함수형 컴포넌트가 `React.memo()`로 감싸져 있는 경우

2. 함수가 다른 hooks에 dependency에 들어가 있는 경우<br />
   e.g. `useEffect(..., [callback])`

3. 함수에게 내부 상태가 있는 경우<br />
   e.g. `debounce` 혹은 `throttle` 함수 일 때

> 📌 `Debounce`, `Throttle`
>
> `Debounce`: 이벤트를 그룹화하여 특정시간이 지난 후 하나의 이벤트만 발생하도록 하는 기술<br /> > _e.g. resize event를 사용자가 크기를 다 조정한 후에 발생시킴_
>
> `Throttle`: 이벤트를 일정한 주기마다 발생하도록 하는 기술<br /> > _e.g. `Throttle`로 설정한 시간이 1ms라면 해당 이벤트는 1ms에 최대 한번의 이벤트만 발생함_

# useCallback()의 바람직한 사용방법

컴포넌트 안에 굉장히 큰 리스트가 있다고 가정해보자

```js
import useSearch from './fetch-items'
function MyBigList({ term, onItemClick }) {
  const items = useSearch(term)
  const map = item => <div onClick={onItemClick}>{item}</div>
  return <div>{items.map(map)}</div>
}
export default React.memo(MyBigList)
```

이런 경우에는 불필요한 렌더링을 방지하기 위해 `React.memo()`로 감싸주게 된다

```js
import { useCallback } from 'react'
export function MyParent({ term }) {
  const onItemClick = useCallback(
    event => {
      console.log('You clicked ', event.currentTarget)
    },
    [term]
  )
  return <MyBigList term={term} onItemClick={onItemClick} />
}
```

`MyBigList`는 부모 컴포넌트에서 `onItemClick`을 받게 되는데 이 `onItemClick`은 `term`이 변경되지 않는 한 `useCallback`에 의해 기억된다

따라서 `MyParent`가 리렌더링되더라도 `onItemClick`함수는 동일하므로 `useCallback`은 메모이제이션 된 값을 변경하지 않는다

# useCallback()의 잘못된 사용방법

```js
import { useCallback } from 'react'
function MyComponent() {
  // Contrived use of `useCallback()`
  const handleClick = useCallback(() => {
    // handle the click event
  }, [])
  return <MyChild onClick={handleClick} />
}
function MyChild({ onClick }) {
  return <button onClick={onClick}>I am a child</button>
}
```

위 예시를 보면 `dependency`가 없는 `useCallback`을 사용하고 있다

`MyComponent`가 렌더링 될때마다 `useCallback`이 호출되고 `useCallback`이 동일한 함수객체를 반환한다

이 경우에는 성능적 이슈가 개선되는 것 보다는 코드의 복잡성만 증가한 셈이다

최적화를 하기 위해서도 결국 자원이 필요하기 때문에 이 자원의 크기가 쓸데없이 커지지않도록 주의하여야 한다

## \*references

1. [Hooks API Reference : useCallback](https://ko.reactjs.org/docs/hooks-reference.html#usecallback)

2. [useCallback vs simple function](https://stackoverflow.com/questions/58130067/usecallback-vs-simple-function)

3. [Your Guide to React.useCallback()](https://dmitripavlutin.com/dont-overuse-react-usecallback/)

4. [디바운스(Debounce)와 스로틀(Throttle) 차이점](https://velog.io/@yes3427/Debounce-and-Throttle)

5. [How to Correctly Debounce and Throttle Callbacks in React](https://dmitripavlutin.com/react-throttle-debounce/#2-debouncing-a-callback-the-first-attempt)

6. [React hook useCallback without dependencies](https://stackoverflow.com/questions/57294549/react-hook-usecallback-without-dependencies)
