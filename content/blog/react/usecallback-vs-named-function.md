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
  return (a, b) => a + b;
}
const sum1 = factory();
const sum2 = factory();
sum1(1, 2); // => 3
sum2(1, 2); // => 3
sum1 === sum2; // => false
sum1 === sum1; // => true
```



## \*references

1. [Hooks API Reference : useCallback](https://ko.reactjs.org/docs/hooks-reference.html#usecallback)

2. [useCallback vs simple function](https://stackoverflow.com/questions/58130067/usecallback-vs-simple-function)

3. [Your Guide to React.useCallback()](https://dmitripavlutin.com/dont-overuse-react-usecallback/)
