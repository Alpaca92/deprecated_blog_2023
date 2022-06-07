---
title: 플러터의 비동기 프로그래밍(AP)
date: 2022-05-30 14:05:72
category: flutter
draft: false
---

비동기 프로그래밍을 이해하기 위해 먼저 `CPU Thread`라는 개념에 대해 알아보도록 하자

![CPU Thread](https://raw.githubusercontent.com/Alpaca92/alpaca92.github.io/master/content/blog/flutter/images/cpu_thread.png)

쓰레드란 **작업을 수행하는 가장 작은 단위**를 말하는데 위의 그림에서 작업 1을 시작하여 완료할 때까지는 다른 작업에 CPU 사용이 불가하고<br />
다시 작업 2를 시작하여 완료할 때까지는 CPU 사용이 불가하다

일반적인 코드의 경우 이러한 작업 처리방식이 문제가 되지 않지만 API를 사용에 서버에 무언가를 요청하는 등의 경우에는 이 요청에 대한 응답을 받기 전까지 아무것도 하지 못하고 기다려야 하는 상황이 발생한다

이는 불필요하게 시간을 낭비할 뿐이기에 이를 해결하기 위해 비동기 프로그래밍이 탄생했다

# Future

계산된 값을 저장하는 것으로 **미래에 받아올 값**을 의미한다

## value

미래에 받아올 값을 지정할 수 있다

```dart
void main() {
  Future<String> name = Future.value('Ayaan');
}
```

## delayed

**얼마 후에** 미래에 값을 받아올 것인지를 정할 수 있는 메서드다

```dart
void main() {
  addNumbers(1, 1);
}

void addNumbers(int number1, int number2) {
  print('계산 시작: $number1 + $number2');

  Future.delayed(Duration(seconds: 2),
      () => print('계산 완료: $number1 + $number2 = ${number1 + number2}'));

  print('함수 종료');
}

/*
  계산 시작: 1 + 1
  함수 종료
  계산 완료: 1 + 1 = 2
*/
```

우리가 지금까지 배웠던 것을 바탕으로 추론해보면 **계산 시작, 계산 완료, 함수 종료**의 순으로 출력될 것이라고 예상했을 것이다

하지만 `delayed` 메서드를 통해 2초 뒤에 실행을 하게 했으므로 **계산 시작, 함수 종료, 계산 완료**순으로 출력된다

## async/await

서버와 통신하여 데이터를 받는다면 해당 데이터를 가공하는 로직을 작성할 때도 있다

이러한 경우를 위해 데이터를 비동기적으로 처리하는 `async/await`를 사용하는 것이다

```dart
void main() {
  addNumbers(1, 1);
}

void addNumbers(int number1, int number2) async {
  print('계산 시작: $number1 + $number2');

  await Future.delayed(Duration(seconds: 2),
      () => print('계산 완료: $number1 + $number2 = ${number1 + number2}'));

  print('함수 종료');
}

/*
  계산 시작: 1 + 1
  (2초간 기다린 후) 계산 완료: 1 + 1 = 2
  함수 종료
*/
```

위 코드를 보면 '2초 동안 그러면 아무런 일도 하지 못하는 것 아닐까?'라는 의구심이 생기게 되는데 아래의 코드를 보면

```dart
void main() {
  addNumbers(1, 1);
  addNumbers(2, 2);
}

void addNumbers(int number1, int number2) async {
  print('계산 시작: $number1 + $number2');

  await Future.delayed(Duration(seconds: 2),
      () => print('계산 완료: $number1 + $number2 = ${number1 + number2}'));

  print('함수 종료');
}

/*
  계산 시작: 1 + 1
  계산 시작: 2 + 2
  계산 완료: 1 + 1 = 2
  함수 종료
  계산 완료: 2 + 2 = 4
  함수 종료
*/
```

위와 같이 출력하는 것을 볼 수 있는데 이는 `await`뒤의 작업들은 따로 작업을 수행하면서 그 다음의 함수들을 바로 실행하기 때문이다

이를 좀 더 구체적으로 살펴보면 'addNumbers(1, 1) 실행 &rarr; print('계산 시작') 실행 &rarr; await문 따로 작업(1) &rarr; addNumbers(2, 2) 실행(1) &rarr; await문 따로 작업(2) &rarr; 작업 된 (1) 실행 &rarr; print('함수 종료') 실행 &rarr; 작업 된 (2) 실행 &rarr; print('함수 종료') 실행'의 순서대로 실행되는 것을 알 수 있다

즉, 짜장면 배달을 시킬 때 우리는 '짜장면 배달옴 &rarr; 짜장면 먹음 &rarr; 짜장면 그릇 기사님이 회수함'의 과정을 거치지만 기사님께서 짜장면을 다 먹을때까지기다리고 있지 않고 다른 일을 하다가 짜장면을 다 먹으면 그릇을 회수하러 오는 것과 같다고 보면 된다



## \*references

1. [[무료 프로그래밍 강의] 40분만에 끝내는 비동기 프로그래밍](https://www.youtube.com/watch?v=rk41rBXq3zQ&ab_channel=코드팩토리)

2. [What is a future](https://dart.dev/codelabs/async-await#what-is-a-future)