---
title: prototype vs __proto__
date: 2022-11-02 16:11:46
category: javascript
draft: true
---

개발 도중 로그를 출력하다보면 `__proto__`라는 것을 흔히 볼 수 있다

그리고 우리가 메서드를 검색하다보면 MDN을 자주 마주치게 되는데 이 사이트에서는 항상 `Object.prototype.values`, `Array.prototype.find`와 같이 설명해주곤 한다

이들이 어떤 의미이고 어떤 역할을 하는지 오늘 좀 자세히 알아보고자 한다

# 일급객체(First-Class Citizens)

자바스크립트의 함수는 [일급객체](https://en.wikipedia.org/wiki/First-class_citizen)다

일급객체의 대표적 조건은

1. 변수나 데이터 구조 안에 담을 수 있다

2. 파라미터로 전달 할 수 있다

3. 리턴 값으로 사용 할 수 있다

이렇게 세 가지 조건이 있는데 다음 예시를 통해 자바스크립트가 실제로 이 조건을 만족하는지 확인해 보도록 하자

## 1.함수 할당

자바스크립트는 여러가지 방법으로 변수에 함수를 담을 수 있다

### a. 변수에 할당

```js
const func = function() {
  // function body
};
```

위와 같은 방법을 `function literal`이라고 한다

### b. method로 이용

```js
```

## \*references

1. [Javascript에서 왜 함수가 1급 객체일까요?](https://soeunlee.medium.com/javascript에서-왜-함수가-1급-객체일까요-cc6bd2a9ecac)

[JavaScript 객체 지향 프로그래밍 - 15. prototype vs proto](https://youtu.be/wT1Bl5uV27Y)
[Object initializer](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Object_initializer)
