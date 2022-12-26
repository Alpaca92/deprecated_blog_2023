---
title: React Hooks 사용 시 주의할 점들
date: 2022-10-04 19:10:03
category: dev
# draft: true
draft: false
---

오늘은 여태 react hooks을 사용하면서 겪었던 경험들을 바탕으로 사용시 주의할 점에 대해 정리해보고자 한다

(따라서 불편함을 겪거나 궁금한 점이 있을 때마다 업데이트가 될 것 같다)

정리하기에 앞서 리엑트가 렌더링되는 조건을 가볍게 되짚어보면

1. `State`가 변경되었을 때

2. `Props`가 변경되었을 때

3. `Parent component`가 리렌더링 될 때

# useState()

## useState를 const로 사용하는 이유

우리가 `useState`를 사용할 때에는 일반적으로 다음과 같이 사용한다

```js
import { useState } from 'react';

function App() {
  const [state, setState] = useState(0);

  // do something..
}
```

state는 분명 변경되는데 어째서 state를 변경 불가능한 const로 선언하였을까?

이를 알기 위해서는 useState가 동작하는 방식에 대해 알 필요가 있다

## setState(setter)는 비동기로 작동한다

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

# useEffect()

## deps에 state가 아닌 일반 변수를 넣으면 어떻게 될까?

`deps`에 일반적인 형태의 변수는 넣어도 리엑트는 이를 업데이트 하지 않는다

```js
function App() {
  let normalCounter = 0;

  useEffect(() => {
    console.log('effect: counter updated');
  }, [normalCounter]);

  return (
    <div>
      <div>normalCounter: {normalCounter}</div>
      <button
        onClick={() => {
          ++normalCounter;
          console.log(`normalCounter: ${normalCounter}`);
        }}
      >
        click me
      </button>
      {console.log('render: i am rendered')}
    </div>
  );
}
```

위와 같은 코드에서 초기 렌더링 시에는 콘솔에서는

```
render: i am rendered
effect: counter updated
```

순으로 출력되며, `click me` 버튼을 세 번 클릭하면

```
<1회>
normalCounter: 1

<2회>
normalCounter: 2

<3회>
normalCounter: 3
```

`normalCounter`의 값은 증가하지만 리렌더링은 발생하지 않는다

그렇다면 `state`를 넣어 비교해보도록 하자

```js
function App() {
  const init = () => {
    console.log('state: init function was fired');
    return 0;
  };
  const [counter, setCounter] = useState(init());
  let normalCounter = 0;

  useEffect(() => {
    console.log('effect: counter updated');
  }, [counter]);

  return (
    <div>
      <div>counter: {counter}</div>
      <div>normalCounter: {normalCounter}</div>
      <button
        onClick={() => {
          setCounter(counter + 1);
          ++normalCounter;
          console.log(`counter: ${counter}, normalCounter: ${normalCounter}`);
        }}
      >
        click me
      </button>
      {console.log('render: i am rendered')}
    </div>
  );
}
```

초기 렌더링 시에는

```
state: init function was fired
render: i am rendered
effect: counter updated
```

위와 같이 `state initialization` > `rendering` > `useEffect`순으로 실행되며, 똑같이 `click me` 버튼을 세 번 클릭하면

```
<1회>
counter: 0, normalCounter: 1
state: init function was fired
render: i am rendered
effect: counter updated

<2회>
counter: 1, normalCounter: 1
state: init function was fired
render: i am rendered
effect: counter updated

<3회>
counter: 2, normalCounter: 1
state: init function was fired
render: i am rendered
effect: counter updated
```

위와 같다

여기서 `counter`가 1, 2, 3이 아닌 이유는 `setCounter`와 동일한 scope에 있기 때문에 아직 리렌더링이 일어나기 전이라 이전 값을 출력하기 때문에 0, 1, 2로 출력된다

`normalCounter`의 경우는 버튼이 클릭될 때마다 `setCounter`에 의해 `state`가 변경되어 리렌더링되어 다시 선언되어 항상 0이 되기 때문에 버튼을 몇번 누르냐와 관계없이 항상 1이 출력된다

## dependency의 비교 알고리즘 이해하기

리엑트는 항상 객체와 배열을 그들의 reference로 비교한다

즉, 두 객체(혹은 배열)이 완벽하게 동일하더라도 reference가 다르면 다르다고 판단해 useEffect를 다시 실행시킨다

```js
const userDetails = {
  username: 'Kim',
};

const userDetails2 = {
  username: 'Kim',
};

console.log(userDetails === userDetails2);
// false
```

따라서 이런 경우 개발자의 의도와는 다르게 동작할 수 있다

이를 해결하기 위해서는 **좀 더 명확한 dependency를 사용**하면 된다

```js
const userDetails = {
  username: 'Kim',
};

const userDetails2 = {
  username: 'Kim',
};

console.log(userDetails.someKey === userDetails2.someKey);
// true
```

useEffect를 사용한다면 다음과 같을 것이다

```js
function UserProfile({ userDetails }) {
  const [error, setError] = useState('');

  useEffect(() => {
    if (userDetails.username) {
      // Do something…!
    }
  }, [userDetails.username]);
}
```

하지만 이 방법은 매우 작은 정보만을 담고있는 객체(혹은 배열)에서만 가능한 방법이다

이를 위해 우리는 [메모이제이션(memoization)](https://en.wikipedia.org/wiki/Memoization)을 해서 비교하는 것이 바람직하다는 것을 알 수 있다

```js
function UserProfile({ userDetails }) {
  const [error, setError] = useState('');
  const { username, email, address } = userDetails;

  const user = useMemo(() => createUser({ username, email, address }), [
    username,
    email,
    address,
  ]);

  useEffect(() => {
    if (username) {
      // Do something…!
    }
  }, [user]);
}
```

위와 같이 작성하면 createUser 함수는 username, email, address가 변경될 때에만 실행되어 새로운 유저객체를 생성한다

> 결론은 dependency array에 넣는 data type이 **primitive type(Boolean, Number, String, Null, Undefined)**라면 예상대로 동작하지만 **object type(Object, Function, Array)**을 넣는 경우에는 **reference compare**를 하는데 매 렌더링마다 새로운 메모리주소에 할당되므로 항상 콜백이 실행된다

🍪 추가로 [dependency array비교 코드](https://github.com/facebook/react/blob/ddd1faa1972b614dfbfae205f2aa4a6c0b39a759/packages/react-reconciler/src/ReactFiberHooks.new.js#L296)를 보면 useEffect뿐만 아니라 다른 훅들에서도 사용되고 있다 짐작할 수 있다

## \*references

- [hooks에서 useState가 const로 선언되는 이유](https://dudghsx.tistory.com/18)

- [[번역] 심층 분석: React Hook은 실제로 어떻게 동작할까?](https://hewonjeong.github.io/deep-dive-how-do-react-hooks-really-work-ko/)

- [[React] setState를 했는데 console.log엔 새로운 값이 반영이 안되는 이유](https://hae-ong.tistory.com/97)

- [UseEffect dependency array and object comparison!](https://dev.to/ms_yogii/useeffect-dependency-array-and-object-comparison-45el)

- [React useEffect 의 dependency array](https://sgwanlee.medium.com/useeffect의-dependency-array-ebd15f35403a)

- [의존성 배열은 shallow compare (equal), deep equal 중 어떤 것을 할까?](https://velog.io/@ckvelog/dependency-array-shallow-equal)

- [React: Let’s deep dive into deps array of useEffect](https://medium.com/suyeonme/react-lets-deep-dive-into-deps-array-of-useeffect-13ab96468db7)

- [리액트의 렌더링은 어떻게 일어나는가?](https://yceffort.kr/2022/04/deep-dive-in-react-rendering#리엑트-렌더링-규칙)

- [When does React re-render components?](https://felixgerschau.com/react-rerender-components/)
