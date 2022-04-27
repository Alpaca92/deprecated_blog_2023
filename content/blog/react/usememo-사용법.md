---
title: useMemo 사용법
date: 2022-04-27 16:04:41
category: react
draft: false
---

React Hooks 중 하나인 `useMemo`함수에 대해 알아보도록 하자

# 1. Memoization

`useMemo`에서 `Memo`가 바로 메모이제이션이다

메모이제이션에 대해 가볍게 알아보자면 기존에 수행한 연산 결과를 어딘가에 저장해두고 동일한 입력이 들어오면 재활용하여 메모리의 낭비를 줄이는 일종의 프로그래밍 기법이라고 생각하면 된다

즉, `useMemo`는 **중복연산**을 피해 앱 성능을 최적화하기 위해 사용되는 훅입니다

# 2. useMemo 사용법

```js
import React, { useState, useCallback, useMemo } from 'react'

export default function App() {
  const [ex, setEx] = useState(0)
  const [why, setWhy] = useState(0)

  // useMemo 사용하기
  useMemo(() => {
    console.log(`ex값이 변화하였습니다`)
  }, [ex])

  // 두 개의 버튼을 설정했다. X버튼만이 ex를 변화시킨다.
  return (
    <>
      <button onClick={() => setEx(curr => curr + 1)}>X</button>
      <button onClick={() => setWhy(curr2 => curr2 + 1)}>Y</button>
    </>
  )
}
```

위 코드에서 버튼 Y를 클릭하면 콘솔에 아무것도 찍히지 않지만 버튼 X를 클릭하면 콘솔에 `ex값이 변화하였습니다`라고 찍힌다

`useEffect`를 안다면 쉽게 이해가 됐을텐데 `dependencies`를 배열로 주어졌기 때문에 해당 값들이 변화하면 `useMemo`안에 있는 함수를 실행해주는 것이다

`useMemo`는 다음과 같이 작성한다

```js
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b])
```

> 📌 `useMemo`는 **Memoization된 값**을 반환한다

## \*references

1. [Hooks API Reference : useMemo](https://ko.reactjs.org/docs/hooks-reference.html#usememo)

2. [useCallback 과 useMemo 의 차이](https://basemenks.tistory.com/238)

3. [React Hooks: useMemo 사용법](https://www.daleseo.com/react-hooks-use-memo/)
