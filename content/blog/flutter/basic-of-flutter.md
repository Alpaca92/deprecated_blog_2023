---
title: 플러터 입문기
date: 2022-05-16 12:05:44
category: flutter
draft: false
---

와이프랑 공유되는 가계부를 작성하기 위해 `부부`라는 앱을 사용하고 있었다

우리는 공통 가계부와 개인 용돈이 따로있는데 개인용돈은 공유되지 않는 가계부로 공통 가계부는 공유되는 가계부로 작성하고 싶었다

즉 가계부 앱 하나의 2개 이상을 관리하고 싶었다

그래서 사용하다보니 아쉬운 점이 몇군데 있었는데

1. 로딩시간이 긴 것 같았다

2. 홈화면에 사진을 지정할 수 있는데 이 사진의 화질이 썩 좋지 않다

3. 다크모드가 없다

4. 관리할 수 있는 가계부가 하나밖에 없어 개인용돈 관리를 위해서는 다른 앱을 사용해야 한다

5. 엑셀로 내보내기가 없다

이러한 점들 때문에 다른 어플들을 찾아봤으나 만족스러운 어플은 없었던 것 같다

그래서 내가 앱을 만들어보면 어떨까? 싶어 react native를 사용할까 했지만 [구글 트랜드](https://trends.google.com/trends/explore?date=2019-02-01%202022-03-24&geo=KR&q=flutter,react%20native)를 보고 flutter에 관심이 더 갔다 [참고](https://www.monterail.com/blog/flutter-vs-react-native-mobile-development)

# Types

`int`, `double`, `String`, `bool` 등이 있는데 var를 사용한 [타입추론(type inference)](https://dart.dev/guides/language/type-system#type-inference)를 사용해 조금 더 간편하게 변수를 선언할수도 있다

```dart
void main() {
  var name = 'hello world!';

  print(name.runtimeType); // String
}
```

하지만 타입을 명시해줄 수 있는 장점을 버리고 var로 모든 것을 선언한다면 다른 사람의 코드를 읽을 때(협업할 때) 굉장히 어려울 수가 있다

> 💭 프론트엔드 개발자라면 왜 react에서 PropType을 쓰는지, 큰 프로젝트에서 js보다 ts를 선호하는 이유가 무엇인지를 알고 있다면 var보다는 타입을 명시하는 것이 좋겠다는 생각을 자연스럽게 했을거다

## var vs dynamic

`var`와 비슷한 용도로 사용할 수 있는 타입으로 `dynamic`이 있는데 이 둘의 차이점은 타입을 변경할 수 있냐 없냐의 차이이다

```dart
void main() {
  var name = 'hello world!';
  dynamic name2 = 'hello world';

  print('${name.runtimeType}, ${name2.runtimeType}') // String, String

  name2 = 5;
  name = 1; // error: A value of type 'int' can't be assigned to a variable of type 'String'
}
```

위와 같이 `dynamic`으로 선언하면 타입마저 변경할 수 있지만 `var`로 선언한 변수는 처음 선언했을 때의 타입을 변경할 수 없다

# nullable, non-nullable

flutter는 변수에 null이 될 수 있다면 `?`를 붙여 명시적으로 표시할 수 있다

```dart
String name = 'black pink';
name = null; // error: A value of type 'Null' can't be assigned to a variable of type 'String'

String? name2 = 'twice';

name2 = null;

print(name2.runtimeType); // Null
```

반대로 null이 될 수 없는 변수로 변환하려면 `!`를 붙여주면 된다 [참고](https://stackoverflow.com/questions/63253015/what-does-the-exclamation-mark-mean-before-a-function-call)

```dart
if (chunkCallback != null) {
  chunkCallback!(0, 100);
}
```

# final vs const

변수의 값을 변경하고 싶지 않다면 `final`, `const`를 사용해주면 된다

```dart
final String name = 'ayaan';

name = 'anonymous'; // error: The final variable 'name' can only be set once
```

```dart
const String name = 'ayaan';

name = 'anonymous'; // error: Constant variables can't be assigned a value
```

`final`, `const`를 사용할 때에는 타입을 생략할 수 있으며 이는 다음과 같다

```dart
const var name = 'ayaan';
const naem ='ayaan';
```

`final`과 `const`의 차이는 `빌드 타임에 값을 알고 있는가?`에 따라 나뉜다

> 코드를 작성하고 실행할 때 코드가 2진법으로 변환이 되는데 이를 `빌드 타임`이라고 한다

```dart
final DateTime now = DateTime.now();
const DateTime now2 = DateTime.now();
// error: Const variables must be initialized with a constant value
// error: The constructor being called isn't a const constructor
```

# Null-aware Operators

`null`인지 아닌지에 따라 값을 다르게 지정할 수 있다

```dart
double? number = 10;

number = null; // 1.

number ??= 5;
```

`1.`이 없다면 number의 값은 10이고 `1.`이 있다면 number의 값은 5일 것이다

`??=`가 의미하는 것은 `좌항(여기서는 number)가 null이면 우항을 할당한다`이기에 `1.`이 있다면 좌항이 null이므로 5가 할당된다 [참고](https://medium.com/@thinkdigitalsoftware/null-aware-operators-in-dart-53ffb8ae80bb)

# list

```dart
List<String> blackPink = ['제니', '지수', '로제', '리사'];
```

위와 같이 리스트 내에 들어갈 타입을 미리 명시해줘야 한다

# map

key-value pair로 이루어져있으며 list처럼 generic으로 key, value의 타입을 명시해줘야 한다

```dart
Map<String, String> dictionary = {
  'Harry Potter': '해리포터',
  'Ron Weasley': '론 위즐리',
}
```

# set

list와 비슷하지만 set은 중복값을 자동으로 삭제해줘 `id`등을 보관할 때 유용하다

```dart
final Set<String> names = {
  'Flutter',
  'Black Pink',
  'Flutter',
}

print(names); // {'Flutter', 'Black Pink'}
```

# enum

```dart
enum Status {
  approved,
  pending,
  rejected,
}

void main() {
  Status status = Status.pending;
  
  if (status == Status.approved) {
    print('승인입니다');
  } else if(status == Status.pending) {
    print('대기입니다');
  } else {
    print('거절입니다');
  }
}
```

`enum`을 쓰는 이유는 정확히 **`Status`안의 값들만을 사용하겠다**라고 명시해주는 것과 같기 때문이다

```dart
void main() {
  String status = 'pending';
  
  if (status == 'approved') {
    print('승인입니다');
  } else if(status == 'pending') {
    print('대기입니다');
  } else {
    print('거절입니다');
  }
}
```

위와 같이 해도 동일한 결과를 가져오지만 `enum`을 사용함으로써 오타를 방지하고 `status`의 값에는 approved, pending, rejected 딱 세가지만 있을 수 있다는 것을 명시한다

# function

```dart
void main() {
  addNumbers(1, 2, 3);
}

void addNumbers(int x, int y, int z) {
  int acc = x + y + z;

  if (acc % 2 == 0) {
    print('짝수입니다');
  } else {
    print('홀수입니다');
  }
}
```

위와 같이 파라미터에 타입을 지정해주고 함수의 타입 또한 지정해줄 수 있다<br />
(위치가 정해져 있는 파라미터를 `positional parameter`라고 한다)

## optional parameter

```dart
void main() {
  addNumbers(1, 2, 3);
}

void addNumbers(int x, [int? y = 0, int? z = 0]) {
  int acc = x + y + z;

  if (acc % 2 == 0) {
    print('짝수입니다');
  } else {
    print('홀수입니다');
  }
}
```

파라미터가 optional인 경우는 대괄호로 감싸주면 된다

## named parameter

```dart
void main() {
  addNumbers(x: 1, z: 3, y: 2);
}

void addNumbers({
  required int x,
  required int y,
  int z = 0, // optional parameter
}) {
  int acc = x + y + z;

  if (acc % 2 == 0) {
    print('짝수입니다');
  } else {
    print('홀수입니다');
  }
}
```

파라미터에 이름을 붙이는 경우에는 순서가 중요하지 않다

# arrow function

```dart
int addNumbers(int x, {required int y, int z = 0}) => x + y + z;
```

위와 같이 화살표 함수를 쓸 수 있으며 implicit return 해준다

# typedef

```dart
void main() {
  Operation operation = add;

  int result = operation(10, 20, 30); // 60

  operation = subtract;

  int result2 = operation(10, 20, 30); // -40

  int result3 = calculate(30, 40, 50, add); // 120
}

typedef Operation = int Function(int x, int y, int z);

int add(int x, int y, int z) => x + y + z;
int subtract(int x, int y, int z) => x - y - z;
int calculate(int x, int y, int z, Operation operation) {
  return operation(x, y, z);
}
```

## \*references

1. [[무료 프로그래밍 강의] 1시간만에 끝내는 Dart언어 기본기](https://www.youtube.com/watch?v=3Ck42C2ZCb8&ab_channel=코드팩토리)
