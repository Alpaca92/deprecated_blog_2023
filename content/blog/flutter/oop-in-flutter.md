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

> 📌 `private`속성을 부여하고 싶으면 앞에 `_`를 붙여주면 된다<br />
`String name` &rarr; `String _name`



## \*references

1. [[무료 프로그래밍 강의] 1시간만에 끝내는 객체지향 프로그래밍](https://www.youtube.com/watch?v=7e80Il_7Z70&ab_channel=코드팩토리)