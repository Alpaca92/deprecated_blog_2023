---
title: PWA(Progressive Web App)이란?
date: 2022-07-22 08:07:04
category: dev
draft: false
---

앱을 만드는 방법은 다양하다

`swift`, `java`를 사용하여 native app을 만드는 방법도 있고, `react-native`나 `flutter`를 사용하여 만드는 방법도 있다

오늘은 그 중 `PWA`에 대해 알아보고자 한다

# PWA

_Progressive Web App is a special class of web apps that allow the user to interact with the site in the same way as with a mobile app._

`PWA`는 웹과 네이티브 앱의 이점을 갖도록 특정 기술과 표준 패턴을 사용해 개발된 웹앱이다

`PWA`는 네이티브 앱 기능(푸시 알림, GPS 내비게이션 및 기타 기능)을 갖춘 웹앱이다

![2021년 이커머스 PWA 개발 계획 설문조사(북아메리카, 유럽)](https://raw.githubusercontent.com/Alpaca92/alpaca92.github.io/master/content/blog/dev/images/pwa_research_01.png)

위 조사는 북아메리카 및 유럽에서 PWA 개발에 대한 이커머스 회사들의 계획에 대한 답변이다(by. [statista](https://www.statista.com/statistics/1174534/investing-pwa-ecommerce-companies-worldwide/))

## 모바일 앱과 PWA의 차이점

`PWA`는 웹 사이트를 기반으로 하는 앱이기 때문에 동일한 컨텐츠 관리 시스템을 가지고 있다

따라서 `PWA`는 다음과 같은 장점을 갖는다

1. 네이티브 앱처럼 다운로드를 할 필요가 없이 간단하게 홈스크린에 아이콘을 추가하기만 하면 된다

2. 웹사이트를 이용하였기 때문에 SEO 측면에서 이점을 갖는다

3. `PWA`는 웹사이트를 기반으로 만들어진 앱이기 때문에 [CMS](https://www.oracle.com/kr/content-management/what-is-cms/)가 가능하다

## PWA의 장점

1. `PWA`는 네이티브 앱과 유사하게 생겼기 때문에 유저에게 친숙한 UX/UI를 제공한다

2. 각 OS(Android, iOS 등)에 대응하는 앱을 따로따로 만들지 않아도 되므로 개발시 많은 자원을 아낄 수 있다

3. 네이티브 앱과 마찬가지로 GPS, 카메라, 마이크 등 기기의 기능을 접근, 사용할 수 있다

4. 앱 개발시 평균 70~90% 정도 개발비용이 절약된다

5. 네이티브 앱과 다르게 설치, 실행이 빠르다

6. Apple Store, Google Play에 앱 등록이 필요하지 않다

7. 앱 사용을 위한 지속적인 인터넷 연결이 필요하지 않다(캐싱 방법에 따라 다름)

8. 앱 업데이트가 필요하지 않다

![PWA의 사용자, 비즈니스적 장점](https://raw.githubusercontent.com/Alpaca92/alpaca92.github.io/master/content/blog/dev/images/pwa_progressive_01.jpeg)

## PWA의 단점

1. 현재 iOS 기기에서는 PWA의 일부 기능을 지원하지 않는다 [참고](https://firt.dev/notes/pwa-ios/)

2. 지문인식, 근접 센서 사용 불가 등 기기의 기능을 제한적으로밖에 상요할 수 없다

3. 더 많은 베터리를 사용한다

4. 몇 주간 해당 앱을 접속하지 않는다면 유저의 캐시가 모두 삭제된다

5. 기기 설정을 통해서만 홈 화면에 수동으로 앱 아이콘을 추가할 수 있다

## \*references

1. [MDN: Progressive web apps](https://developer.mozilla.org/en-US/docs/Web/Progressive_web_apps)

2. [WHAT IS A PROGRESSIVE WEB APP? PROS & CONS OF PWA](https://lvivity.com/introduction-to-progressive-web-apps)
