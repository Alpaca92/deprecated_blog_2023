---
title: Recoil 기본
date: 2022-09-18 11:09:12
category: react
draft: true
---

`Recoil`은 페이스북에서 만든 상태관리 라이브러리로 useState를 사용하는 것 만큼 사용이 간단하면서 상태관리를 효율적으로 할 수 있게 도와준다

> Recoil lets you create a data-flow graph that flows from atoms (shared state) through selectors (pure functions) and down into your React components.

즉, Recoil은 원자(atom, 공유 상태)에서 selector(순수 함수)를 거쳐 리엑트 컴포넌트로 흐르는 데이터 플로우를 만들어 준다

원자(atom)는 리엑트 컴포넌트가 구독(subscribe)할 수 있는 상태 단위이며, selector는 이 상태를 동기/비동기식으로 변환한다

# Recoil Root

여러 개의 Root가 공존할 수 있으며, atom state의 독립적인 공급자/저장소를 나타낼 수 있다

```js
import { RecoilRoot } from 'recoil'

function App() {
  return (
    <RecoilRoot>
      <someComponents />
    </RecoilRoot>
  )
}
```

# atom

atom은 recoil에서 상태를 정의하는 방법이다

```js
const todoState = atom({
  key: 'todo',
  default: [].
});
```

상태를 정의할 때는 고유값인 key를 설정하고, 기본값(default value)를 설정하면 된다

이렇게 정의한 atom은 `useRecoilValue`, `useSetRecoilState`, `useRecoilState` hook으로 사용할 수 있게 된다

1. `useRecoilValue`: 상태 값만 필요한 경우에 사용한다

2. `useSetRecoilState`: 상태를 업데이트하는 함수만 필요한 경우 사용된다

3. `useRecoilState`: `useState`와 같이 첫번째 원소는 상태를 두번째 원소는 상태를 업데이트하는 함수를 반환한다

# selector

> Selectors represent a function, or derived state in Recoil. You can think of them as similar to an "idempotent" or "pure function" without side-effects that always returns the same value for a given set of dependency values.

selector는 파생된 상태(derived state)의 조각을 나타낸다고 말하는데 이는 위의 예시 `todo`에서 사용자에 의해 조작된 값을 반환해줄 수 있기 때문이다

즉, selector는 recoil에서 관리하는 상태의 특정 부분만 선택하거나 상태를 사용하여 연산한 값을 조회할때에도 사용하면서 다른 데이터에 의존하는 동적 데이터를 구축할 수 있게 해주는 역할을 한다

## get

get을 사용하여 상태(atom 혹은 다른 selector)를 조회할 수 있는 읽기전용 selector를 만들 수 있다

읽기 전용이기 때문에 `useRecoilState`는 사용할 수 없고 `useRecoilValue`로만 상태를 조회해야 한다

```js
const fontSizeLabelState = selector({
  key: 'fontSizeLabelState',
  get: ({ get }) => {
    const fontSize = get(fontSizeState)
    const unit = 'px'

    return `${fontSize}${unit}`
  },
})
```

## set

get과 함께 set을 사용하면 읽고 쓰기가 모두 가능한 selector를 사용할 수 있다

이 selector는 값을 매개변수로 수신하고 이를 사용하여 데이터 흐름 그래프를 따라 업스트림으로 변경 사항을 전파할 수 있다

또한, 읽고 쓰기가 가능한 selector이기 때문에 `useRecoilState`사용이 가능하다

```js
const proxySelector = selector({
  key: 'proxySelector',
  get: ({ get }) => ({ ...get(myAtom), extraField: 'some string' }),
  set: ({ set }) => ({ set }, newValue) => set(myAtom, newValue),
})
```

# useRecoilCallback(callback, deps)

## \*references

1. [Recoil 기초 개념 및 사용법](https://code-masterjung.tistory.com/128)

2. [Recoil Documentation](https://recoiljs.org/docs/introduction/installation)

3. [Recoil 레시피: 스냅샷과 상태 모니터링](https://taegon.kim/archives/10126)
