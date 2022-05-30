---
title: 플러터의 함수형 프로그래밍(FP)
date: 2022-05-27 08:05:05
category: flutter
draft: false
---

개인적으로 함수형 프로그래밍(functional programming)은 OOP에 비하면 쉽다고 생각한다

그래도 flutter에서는 어떤식으로 사용되어지고 있는지 알 필요가 있으니 정리를 해두도록 하자

# 형변환

```dart
void main() {
  List<String> blackPink = ['로제', '지수', '리사', '제니', '제니'];

  print(blackPink.asMap());
  // {0: 로제, 1: 지수, 2: 리사, 3: 제니, 4: 제니}

  print(blackPink.toSet());
  // {로제, 지수, 리사, 제니}

  Map blackPinkMap = blackPink.asMap();

  print(blackPinkMap.keys);
  // (0, 1, 2, 3, 4)

  print(blackPinkMap.values);
  // (로제, 지수, 리사, 제니, 제니)

  print(blackPinkMap.keys.toList());
  // [0, 1, 2, 3, 4]

  print(blackPinkMap.values.toList());
  // [로제, 지수, 리사, 제니, 제니]

  Set blackPinkSet = Set.from(blackPink);

  print(blackPinkSet.toList());
  // [로제, 지수, 리사, 제니]
}
```

이렇게 list에서 map, set으로 map과 set을 list로 자유롭게 형변환이 가능하다

> 📌 `Set.from()` vs `toSet()`의 차이점<br /> > <br /> > `toSet()` 메서드를 사용하면 해당 타입을 읽어오지만 `Set.from()` 생성자의 경우는 타입을 명시해줘야한다<br />
> ex. Set<String>.from(someList);

## map

iterable한 자료를 다른 형태로 변경할 때 사용되는 메서드다

```dart
// List

void main() {
  List<String> blackPink = ['로제', '지수', '리사', '제니'];

  final newBlackPink = blackPink.map((x) => '블랙핑크 $x');

  print(newBlackPink.toList());
  // [블랙핑크 로제, 블랙핑크 지수, 블랙핑크 리사, 블랙핑크 제니]

  print(blackPink == blackPink); // true
  print(newBlackPink == newBlackPink); // true
  print(blackPink == newBlackPink); // false
}
```

```dart
// Map

void main() {
  Map<String, String> harryPotter = {
    'Harry Potter': '해리 포터',
    'Ron Weasley': '론 위즐리',
    'Hermione Granger': '헤르미온느 그레인저'
  };

  final result = harryPotter.map((key, value) =>
      MapEntry('Harry Potter Character $key', '해리포터 캐릭터 $value'));

  print(result);
  /*
    {
      Harry Potter Character Harry Potter: 해리포터 캐릭터 해리 포터,
      Harry Potter Character Ron Weasley: 해리포터 캐릭터 론 위즐리,
      Harry Potter Character Hermione Granger: 해리포터 캐릭터 헤르미온느 그레인저
    }
  */
}
```

```dart
// Set

void main() {
  Set blackPink = {
    '로제',
    '지수',
    '제니',
    '리사'
  };

  final newBlackPink = blackPink.map((x) => '블랙핑크 $x').toSet();

  print(newBlackPink);
  // {블랙핑크 로제, 블랙핑크 지수, 블랙핑크 제니, 블랙핑크 리사}
}
```

## split

문자열을 매치되는 패턴에 의해 분리해 리스트에 담아주는 메서드다

```dart
// List

void main() {
  String number = '13579';

  final parsed = number.split('');

  print(parsed); // [1, 3, 5, 7, 9]

  final dotJpg = number.split('').map((x) => '$x.jpg');

  print(dotJpg.toList()); // [1.jpg, 3.jpg, 5.jpg, 7.jpg, 9.jpg]
}
```

## where

특정 조건을 만족하는 요소들만을 분리하고 싶을 때 사용하는 메서드다

```dart
void main() {
  List<Map<String, String>> people = [
    {
      'name': '로제',
      'group': '블랙핑크',
    },
    {
      'name': '지수',
      'group': '블랙핑크',
    },
    {
      'name': 'RM',
      'group': 'BTS',
    },
    {
      'name': '뷔',
      'group': 'BTS',
    },
  ];

  final blackPink = people.where((x) => x['group'] == '블랙핑크').toList();

  print(blackPink);
  // [{name: 로제, group: 블랙핑크}, {name: 지수, group: 블랙핑크}]
}
```

> 💭 js를 아는 사람이라면 `filter()`와 유사하다는 것을 알 수 있다

## reduce

요소를 반복적으로 결합하여 단일 값으로 축소합니다<br />
(대상 iterable은 1개 이상의 요소를 갖고 있어야 한다)

```dart
void main() {
  List<int> numbers = [1, 3, 4, 5, 7, 9];

  final sum = numbers.reduce((prev, next) => prev + next);

  print(sum); // 29

  List<String> words = [
    '안녕하세요 ',
    '저는 ',
    'ayaan 입니다'
  ];

  final greeting = words.reduce((prev, next) => prev + next);

  print(greeting); // 안녕하세요 저는 ayaan 입니다
}
```

단, reduce를 사용할 때 실행 대상이 되는 데이터의 타입과 반환되는 데이터의 타입이 동일해야만 한다

```dart
void main() {
  List<String> words = [
    '안녕하세요 ',
    '저는 ',
    'ayaan 입니다'
  ];

  final length = words.reduce((prev, next) => prev.length + next.length);
  // error: The return type 'int' is not a 'String' as required by the closure's context
}
```

## fold

reduce와 비슷하지만 initial value를 지정하고 제너릭으로 타입을 지정할 수 있다

```dart
void main() {
  List<int> = numbers = [1, 3, 5, 7, 9];

  final sum = numbers.fold<int>(0, (prev, next) => prev + next);

  print(sum); // 25
}
```

## spread operator

iterable한 데이터 값을 개별로 분리 할 수 있다 _[ref](https://dart.dev/guides/language/language-tour#spread-operator)_

```dart
void main() {
  List<int> even = [2, 4, 6, 8];
  List<int> odd = [1, 3, 5, 7];

  print([...even, ...odd]);
  // [2, 4, 6, 8, 1, 3, 5, 7]
}
```

## 결론

위에서 배웠던 것들을 종합하여 사용해 본다면 이래와 같다

```dart
void main() {
  final List<Map<String, String>> people = [
    {
      'name': '지수',
      'group': '블랙핑크',
    },
    {
      'name': '로제',
      'group': '블랙핑크',
    },
    {
      'name': 'RM',
      'group': 'BTS',
    },
    {
      'name': '뷔',
      'group': 'BTS',
    },
  ];

  final parsedPeople = people.map(
    (x) => Person(
      name: x['name']!,
      group: x['group']!,
    ),
  ).toList();

  print(parsedPeople);
  /*
    [
      Person(name: 지수, group: 블랙핑크),
      Person(name: 로제, group: 블랙핑크),
      Person(name: RM, group: BTS),
      Person(name: 뷔, group: BTS)
    ]
  */

  final bts = parsedPeople.where(
    (x) => x.group == 'BTS',
  ).toList();

  print(bts);
  /*
    [
      Person(name: RM, group: BTS),
      Person(name: 뷔, group: BTS)
    ]
  */
}

class Person {
  final String name;
  final String group;

  Person({
    required this.name,
    required this.group,
  });

  @override
  String toString() {
    return 'Person(name: $name, group: $group)';
  }
}
```

FP의 가장 큰 장점은 `map(...).where(...).fold<int>(...)` 이런식으로 여러 함수들은 체이닝(chaining)하여 사용할 수 있다는 점이다

## \*references

1. [[무료 프로그래밍 강의] 40분만에 끝내는 함수형 프로그래밍](https://www.youtube.com/watch?v=fwh27A_D-20&ab_channel=코드팩토리)

2. [map<T> method](https://api.dart.dev/stable/2.17.1/dart-core/Iterable/map.html)

3. [split method](https://api.dart.dev/stable/2.17.1/dart-core/String/split.html)

4. [Set<E>.from constructor](https://api.dart.dev/stable/2.16.0/dart-core/Set/Set.from.html)

5. [toSet method](https://api.dart.dev/stable/2.17.1/dart-core/Set/toSet.html)

6. [where method](https://api.dart.dev/stable/2.17.1/dart-core/Iterable/where.html)

7. [reduce method](https://api.dart.dev/stable/2.17.1/dart-core/Iterable/reduce.html)

8. [fold<T> method](https://api.dart.dev/stable/2.17.1/dart-core/Iterable/fold.html)

9. [operators](https://dart.dev/guides/language/language-tour#operators)
