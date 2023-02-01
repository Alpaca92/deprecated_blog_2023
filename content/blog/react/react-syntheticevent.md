---
title: React SyntheticEvent
date: 2023-02-01 10:02:82
category: react
draft: true
---

자바스크립트로 이벤트를 작성했을 때에는 다음과 같은 방식을 많이 사용했을 것이다

```js
element.addEventListener(on<event>, event handler);
```

하지만 리엑트에서는 예전에 html에 이벤트를 작성하듯 작성한다

```html
<!-- HTML 방식 -->
<button onclick="eventHandler()">
  some button
</button>
<script>
  function eventHandler() {
    alert('click event is occured');
  }
</script>
```

```jsx
// React
<SomeComponent onClick={() => alert('click event is occured')}>
  some clickable element
</SomeComponent>
```

언뜻보면 동일한 것 같지만 사실은 약간 다르게 동작한다

이러한 리엑트의 이벤트를 `합성 이벤트(Synthetic Event)`라고 부른다

# 합성 이벤트(Synthetic Event)

> Your event handlers will be passed instances of SyntheticEvent, a cross-browser wrapper around the browser’s native event. It has the same interface as the browser’s native event, including stopPropagation() and preventDefault(), except the events work identically across all browsers.

_"이벤트 핸들러는 모든 브라우저에서 동일하게 이벤트를 처리하기 위해 이벤트 래퍼(wrapper) SyntheticEvent 객체를 전달받는다"_

리엑트가 합성 이벤트 객체를 제공하는 이유다

이러한 이유때문에 리엑트의 합성 이벤트는 정확하게 `NativeEvent`와 일치하지 않는다

예를 들어 `onMouseLeave`는 e.nativeEvent에서 `mouseout`을 가르킨다

이러한 매핑은 공개된 API의 일부가 아니며, 언제든지 변경될 수 있다

## Properties

리엑트 이벤트 객체는 다음과 같이 일부 표준 이벤트 속성(properties)를 구현한다

`bubbles`, `cancelable`, `currentTarget`, `defaultPrevented`, `eventPhase`, `isTrusted`, `target`, `timeStamp`

> 🍪 자세한 내용은 [여기](https://beta.reactjs.org/reference/react-dom/components/common#react-event-object-properties)를 참고하자

또한 원래 브라우저 [이벤트](https://developer.mozilla.org/en-US/docs/Web/API/Event) 객체인 `nativeEvent`도 제공한다

## \*references

- [React Docs: SyntheticEvent](https://reactjs.org/docs/events)

- [React Docs Beta: React event object](https://beta.reactjs.org/reference/react-dom/components/common#react-event-object)
