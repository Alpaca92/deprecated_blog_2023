---
title: React Flux pattern
date: 2022-09-02 09:09:69
category: react
draft: false
---

React를 공부하다보면 자연스럽게 MVC와 Flux에 대해 듣게된다

그 중 오늘은 Flux에 대해 알아보도록 하자

# Flux의 출현배경

기존에 사용되고 있던 MVC 패턴은 Model에 데이터를 정의해두고, Controller를 이용해 Model의 데이터를 CRUD하고, 변경된 데이터는 View layer에 출력하여 사용자에게 전달하였다

![MVC 패턴](./images/flux_01.JPG)

이 패턴의 문제점은 어플리케이션의 규모가 커질수록 데이터의 흐름이 매우 복잡해진다는 점이다

이 문제점 중 가장 잘 알려져 있는 것이 facebook notification 버그다

![MVC 패턴](./images/flux_02.JPG)

facebook에 로그인 했을 때 화면 위의 메세지 아이콘에 알림이 떠 있지만, 그 메세지 아이콘을 클릭해서 들어가보면 아무런 메세지가 없던 적이 있었을 것이다

알림은 사라지겠지만 몇 분 뒤 알림이 다시 나타나고 여전히 아무런 메세지도 나타나지 않는다

이 사이클이 계속반복되는 버그가 바로 facebook notification 버그였다

facebook팀은 이 버그를 고쳤지만 얼마동안 지나고나면 다시 나타났다

이렇게 문제가 생기고 고치는 뫼비우스의 띠는 끝이 보이지 않았다

그래서 facebook에서는 단기적인 해결책이 아닌 시스템을 더욱 예측가능하게 만들어 문제점을 완전히 없애고 싶어했다

# 근본적인 문제점

facebook이 찾은 근본적인 문제점은 `데이터 흐름`에 있었다

![MVC 패턴](./images/flux_03.JPG)

이전에는 데이터를 가지고 있는 model이 rendering하기 위해 view layer로 데이터를 보냈다

사용자와의 상호작용이 view를 통해 일어나기 때문에 사용자의 입력에 따라 가끔 view가 model을 업데이트해야 할 필요가 있었다

이러한 일들이 매우 많이 일어난다면 마치 산사태처럼 아주 많은 변경들이 일어나게되고 이 변경들은 예측불가능할 것이다

![MVC 패턴](./images/flux_04.JPG)

이런 변경들이 비동기적으로 일어나거나 하나의 변경이 다수를 변경시킬 수도 있으므로 이는 데이터 흐름을 디버그하기 어렵게 만든다

# 해결책

## \*references

1. [Flux로의 카툰 안내서](https://bestalign.github.io/translation/cartoon-guide-to-flux/)

2. ['데이터가 폭포수처럼 흘러내려' React의 flux 패턴](https://www.huskyhoochu.com/flux-architecture/)
