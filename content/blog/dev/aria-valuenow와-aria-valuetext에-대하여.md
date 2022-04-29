---
title: aria-valuenow와 aria-valuetext에 대하여
date: 2022-04-29 09:04:89
category: dev
draft: false
---

먼저 aria에 대해 가볍게 알아보면 `aria(accessible rich internet applications)`는 장애를 가진 사용자가 웹 콘텐츠와 웹 앱에 더 쉽게 접근할 수 있는 방법을 정의하는 여러 특성을 말한다

## 1. aria-valuenow

현재 range 위젯의 값을 정의한다

![](https://mblogthumb-phinf.pstatic.net/MjAxODAyMDFfNTQg/MDAxNTE3NDgyNjUxMzEx.Frrtr2IMwSxUuAalm7dx4MxMpUPOPSUDb6npMlXHtxYg.s00NjDfQMa8YkWKbrwTyJHGekcnbvUM6nsRJpNWVsqUg.PNG.akydosa/processbar3.PNG?type=w2)

위와 같이 range가 있을 때 스크린리더기를 통해 현재 어떠한 값인지를 알려줄 수 있다

하지만 value로 들어갈 수 있는 값은 숫자값뿐이므로 사용자에게 친숙한 단어를 사용할 수 없다

예를 들어 위의 경우 '40%'가 아닌 '40'으로 발화(發話)하기 때문에 의미전달이 명확하지 않을 수 있다

## 2. aria-valuetext

이를 보완하여 다양한 문자열을 사용할 수 있게한 것이라고 보면 된다

즉 위의 예시에서 '40%'를 정확하게 안내할 수 있으므로 좀 더 사용자 친화적이라 할 수 있다

`aria-valuenow`와 `aria-valuetext`가 같이 있다면 스크린 리더는 `aria-valuetext`를 우선시한다

> _When both the `aria-valuetext` and `aria-valuenow` are included, the `aria-valuetext` is announced. When there is no `aria-valuetext` attribute, assistive technologies will announce the `aria-valuenow` attribute for the current value._

## \*references

1. [ARIA](https://developer.mozilla.org/ko/docs/Web/Accessibility/ARIA)

2.[aria-valuenow](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Attributes/aria-valuenow)

3. [aria-valuetext](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Attributes/aria-valuetext)
