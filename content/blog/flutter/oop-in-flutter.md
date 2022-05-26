---
title: 플러터의 객체지향프로그래밍(OOP)
date: 2022-05-17 13:05:45
category: flutter
draft: false
---

OOP를 위해서는 일단 클래스에 대해 알아야 한다

# class

클래스는 일종의 blueprint라고 보면 된다

예를 들어 컴퓨터 본체 도면이 있다면 GPU, CPU, RAM, mother board 등이 있을텐데 각 부품은 어느 제조사의 제품을 넣어도 서로 호환만 된다면 사용이 가능하다

```dart
void main() {
  // new 키워드 생략가능
  Idol blackPink = Idol('블랙핑크', ['지수', '제니', '리사', '로제']);

  print(blackPink.name); // 블랙핑크
  print(blackPink.members); // [지수, 제니, 리사, 로제]
  blackPink.sayHello(); // 안녕하세요 블랙핑크입니다
  blackPink.introduce(); // 저희 멤버는 [지수, 제니, 리사, 로제](이)가 있습니다
}

class Idol {
  Idol(String name, List<String> members) // constructor
      : this.name = name,
        this.members = members;

  String name;
  List<String> members;

  void sayHello() {
    print('안녕하세요 ${this.name}입니다');
  }

  void introduce() {
    print('저희 멤버는 ${this.members}(이)가 있습니다');
  }
}

```

constructor의 단축문법을 사용하면 아래와 같다

```dart
void main() {
  Idol blackPink = Idol('블랙핑크', ['지수', '제니', '리사', '로제']);

  print(blackPink.name);
  print(blackPink.members);
  blackPink.sayHello();
  blackPink.introduce();
}

class Idol {
  Idol(this.name, this.members);

  String name;
  List<String> members;

  void sayHello() {
    print('안녕하세요 ${this.name}입니다');
  }

  void introduce() {
    print('저희 멤버는 ${this.members}(이)가 있습니다');
  }
}
```

클래스의 constructor 또한 아래와 같이 named로 생성해볼 수 있다

```dart
void main() {
  Idol blackPink = Idol('블랙핑크', ['지수', '제니', '리사', '로제']);

  print(blackPink.name);
  print(blackPink.members);
  blackPink.sayHello();
  blackPink.introduce();

  Idol bts = Idol.fromList([['RM', '진', '슈가', '제이홉', '지민', '뷔', '정국'], 'BTS']);

  print(bts.name);
  print(bts.members);
  bts.sayHello();
  bts.introduce();
}

class Idol {
  Idol(this.name, this.members);
  Idol.fromList(List values) // named
      : this.members = values[0],
        this.name = values[1];

  String name;
  List<String> members;

  void sayHello() {
    print('안녕하세요 ${this.name}입니다');
  }

  void introduce() {
    print('저희 멤버는 ${this.members}(이)가 있습니다');
  }
}
```

## getter, setter

```dart
  // getter
  String get firstMember {
    return this.members[0];
  }

  // setter
  set firstMember(String name) {
    this.members[0] = name;
```

getter의 경우 일반적인 함수와 기능적인 차이는 없으나 뉘앙스의 차이가 있다

일반 함수의 경우 많은 로직들이 들어가는 경우가 많지만 getter의 경우 간단한 데이터의 가공을 목적으로 사용하게 된다

setter의 경우에는 현대 프로그래밍에서 많이 사용되지 않는다

요즘은 immutable 변수를 만드는 것이 트렌드기 때문이다

```dart
class Idol {
  Idol(this.name, this.members);

  final String name;
  final List<String> members;
}
```

> 📌 `private`속성을 부여하고 싶으면 앞에 `_`를 붙여주면 된다<br /> > `String name` &rarr; `String _name`

## override

상속 받은 클래스 내에서 다시 재정의하면 해당 값이 덮어쓰여진다

```dart
class TimesTwo {
  final int number;

  TimesTwo(
    this.number,
  );

  int calculate() {
    return number * 2; // this.number도 가능
  }
}

class TimesFour extends TimesTwo {
  TimesFour(
    int number,
  ) : super(number);

  @override
  int calculate() {
    return number * 4; // super.number 혹은 this.number도 가능
  }

  /*
  4배를 한다는 것은 결국 부모의 calculate method를 다시 * 2하는 것이므로
  아래와 같이 표현 가능

  int calculate() {
    return super.calculate() * 2;
  }
  */
}
```

위 코드에서 `return number * 2`를 `return this.number * 2`로 하는 것이 원칙이지만 이렇게하면 `don't access members with this unless avoiding shadowing`라는 경고문이 뜬다

이는 calulate 메서드의 블록스코프에서 number라는 변수가 없다면 this를 생략해줄 수 있기 때문이다 [참고](https://stackoverflow.com/questions/59599657/dart-extension-dont-access-members-with-this-unless-avoiding-shadowing)

## static

인스턴스에 귀속되지 않고 class에 귀속되는 것을 말한다

즉 클래스에서는 접근이 가능하지만 인스턴스에서는 접근이 불가능하다

```dart
void main() {
  Employee seulgi = Employee('슬기');
  Employee chorong = Employee('초롱');

  seulgi.printNameAndBuilding();
  // 제 이름은 슬기입니다. null 건물에서 근무하고 있습니다.

  Employee.building = '남산타워';

  seulgi.printNameAndBuilding();
  // 제 이름은 슬기입니다. 남산타워 건물에서 근무하고 있습니다.

  chorong.printNameAndBuilding();
  // 제 이름은 초롱입니다. 남산타워 건물에서 근무하고 있습니다.

  Employee.printBuilding();
  // 저는 남산타워 건물에서 근무중입니다
}

class Employee {
  static String? building;
  final String name;

  Employee(
    this.name,
  );

  void printNameAndBuilding() {
    print('제 이름은 $name입니다. $building 건물에서 근무하고 있습니다.');
  }

  static void printBuilding() {
    print('저는 $building 건물에서 근무중입니다');
  }
}
```

## interface

클래스의 형태를 강제할 때 사용하게 되는데 interface라는 키워드 대신 class를 사용하며 이름에 명시적으로 interface를 넣어주는 것이 일반적이다<br />
(`abstract`를 사용해 인스턴스를 만드는 것을 방지할 수 있다)

```dart
abstract class IdolInterface {
  String name;

  IdolInterface(this.name);

  void sayName();
}

class Boy implements IdolInterface {
  String name;

  Boy(this.name);

  void sayName() {
    print('제 이름은 $name입니다');
  }
}
```

위 코드에서 `Annotate overridden members`라는 경고문이 뜨는데 이는 `@override`를 넣어주지 않아서 그렇다

```dart
@override
String name;

// and

@override
void sayName() {
  print('제 이름은 $name입니다');
}
```

위와 같이 넣어주면 경고문이 사라진다

## generic

타입 또한 외부에서 받아 좀 더 유연하게 사용할 수 있게 해준다

```dart
void main() {
  Lecture<int, String> lecture = Lecture(12345, 'some name');

  print(lecture.id.runtimeType); // int
  print(lecture.name.runtimeType); // String
}

class Lecture<T, V> {
  final T id;
  final V name;

  Lecture(this.id, this.name);
}
```

# Conclusions

클래스를 사용하는데 왜 OOP(Object oriented programming)이라고 부를까?

사실 클래스를 선언하면 암묵적으로 오브젝트에서 확장해온다

```dart
void main() {
  Test test = Test();
}

class Test { } // class Test extends Object { } 와 동일
```

## \*references

1. [[무료 프로그래밍 강의] 1시간만에 끝내는 객체지향 프로그래밍](https://www.youtube.com/watch?v=7e80Il_7Z70&ab_channel=코드팩토리)
