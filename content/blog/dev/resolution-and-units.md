---
title: 해상도와 디스플레이 단위(px, dp, sp, pt)
date: 2022-10-05 18:10:76
category: dev
thumbnail: { thumbnailSrc }
draft: true
---

웹개발을 하다보면 대부분 픽셀(px), 그리고 상대적인 단위(rem, em, etc.)를 사용하게 되는데 앱개발에서의 단위는 이와는 조금 다른 구석이 있다

이 단위를 간략하게 알아보도록 하자

# Pixel

픽셀(pixel)은 Picture + Element의 합성어로 색을 구성하는 최소 단위를 말한다

이는 절대적인 크기를 의미하지 않으며 따라서 각 기기마다 1px의 크기는 모두 다를 수 있다

![px이 모바일에서 적용될 때 영역](./images/resolution_and_units_02.JPG)

위 그림에서 `mdpi`에서 `xxhdpi`로 갈수록 동일한 픽셀(180 x 180)이 차지하는 영역의 크기가 줄어들고 있다

즉, 고해상도 기기에서의 픽셀의 크기가 저해상도에서의 픽셀의 크기보다 작다<br />(모니터 해상도를 조절할 때 아이콘 등이 커지고 작아진 경험이 있을 것이다)

## 🍪(물리픽셀, 논리픽셀)

# DP(Density Independent Pixel)

주로 안드로이드(android)에서 쓰이는 단위로 **픽셀과 상관없이 독립적인 크기를 가진다**

![dpi별 dp의 영역(크기)](./images/resolution_and_units_01.JPG)

위 예시를 보면 가로길이의 절반크기의 정사각형을 볼 수 있다

## \*references

1. [What is the Difference Between Screen Size and Screen Resolution?](https://www.itsasap.com/blog/screen-size-vs-screen-resolution)

2. [디스플레이 단위(PX, DP, SP, PT) 알아보기](https://brunch.co.kr/@zalhanilll/407)
