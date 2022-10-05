---
title: React Hooks 사용 시 주의할 점들
date: 2022-10-04 19:10:03
category: dev
draft: true
---

오늘은 여태 react hooks을 사용하면서 겪었던 경험들을 바탕으로 사용시 주의할 점에 대해 정리해보고자 한다

# useState()

## setState안에서 console.log 사용

setState를 하고나서 console.log를 통해 변경된 state 값을 확인해주는 경우는 흔하다

하지만 가끔 setState를 한 후 console.log를 찍어도 값이 변하지 않는 경우가 있었는데

```js
const [state, setState] = useState(0);

const incrementState = () => {
  setState(state + 1);
  setState(state + 1);
  setState(state + 1);
  setState(state + 1);

  console.log(state);
};

// 예상 출력: 4
// 실제 출력: 0
```

이런 상황이 일어나는 이유는 바로 `setState`가 비동기로 동작하기 때문이다

**_그렇다면 왜 `setState`는 비동기로 동작하는 걸까?_**

`useState`는 `Object.is()`를 사용해 state의 변화를 감지하고 변화가 있다면 해당 컴포넌트부터 자식컴포넌트까지 `render()`를 실행해 렌더링을 한다

그렇다면 빠르게 만번, 십만번 이상 state가 변한다면 어떻게 될까?

당연히 성능적인 측면에서 이슈가 발생할 것이기에 리엑트는 **16ms 동안 변경된 상태 값들을 모아서 한번에 리렌더링**하는 `Batch Update`방식을 사용한다

```js
const [state, setState] = useState(0);

const incrementState = () => {
  setState(state + 1);
  setState(state + 1);
  setState(state + 1);
  setState(state + 1);

  console.log(state);
};

// 예상 출력: 4
// 실제 출력: 0
```

따라서 위 코드는 `incrementState`가 끝난 후에 state의 update가 반영된다<br />(콜스택이 다 비워진 후 콜백 큐에 있는 비동기 처리가 이벤트 루프에 의해 콜스택으로 들어오기 때문에)

비동기에 대해 잘 모르겠다면 [이글](<https://alpaca92.github.io/dev/자바스크립트에-대하여-(1)/>)을 읽고오길 권장한다



## \*references

1. [[React] setState를 했는데 console.log엔 새로운 값이 반영이 안되는 이유](https://hae-ong.tistory.com/97)
