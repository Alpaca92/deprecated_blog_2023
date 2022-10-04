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
}

// 예상 출력: 4
// 실제 출력: 0
```

이런 상황이 일어나는 이유는 바로 `setState`가 비동기로 동작하기 때문이다

## \*references

1. [[React] setState를 했는데 console.log엔 새로운 값이 반영이 안되는 이유](https://hae-ong.tistory.com/97)