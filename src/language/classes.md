---
title: 클래스
description: 클래스, 클래스 인스턴스, 클래스 멤버에 대한 요약.
---

<?code-excerpt replace="/ *\/\/\s+ignore_for_file:[^\n]+\n//g; /(^|\n) *\/\/\s+ignore:[^\n]+\n/$1/g; /(\n[^\n]+) *\/\/\s+ignore:[^\n]+\n/$1\n/g; / *\/\/\s+ignore:[^\n]+//g; /([A-Z]\w*)\d\b/$1/g"?>

Dart는 클래스와 mixin 기반 상속을 지원하는 객체지향언어입니다.
모든 객체는 클래스의 인스턴스이고, `Null`을 제외한 클래스는 모두 [`Object`][]에서 비롯합니다.
*Mixin 기반 상속*이란 말은, 모든 클래스가 하나의 부모 클래스를 가지고 있지만
([최상위 클래스][top-and-bottom]인 `Object?`를 제외한) 클래스의 바디는 다양한 클래스 계층에서 재사용 될 수 있음을 의미합니다.
[확장 메서드][Extension methods]는 서브 클래스를 생성하거나, 클래스를 바꾸지 않고 클래스에 기능을 추가하는 방법입니다.
[클래스 제어자][Class modifiers]를 사용하여 라이브러리가 클래스를 서브타입하는 방법을 제어할 수 있습니다.


## 클래스 멤버 사용하기

객체들은 함수와 데이터 (각각 *메서드*, *인스턴스 변수*)로 이루어진 *멤버*를 가집니다.
메서드를 호출 할 때, 객체에서 함수를 *호출*합니다:
메서드는 해당 객체의 함수와 데이터에 접근 할 수 있습니다.

점 (`.`)을 사용햐여 인스턴스 변수나, 메서드를 사용합니다:

<?code-excerpt "misc/test/language_tour/classes_test.dart (object-members)"?>
```dart
var p = Point(2, 2);

// y의 값을 얻음.
assert(p.y == 2);

// p의 distanceTo() 메서드 호출.
double distance = p.distanceTo(Point(4, 4));
```

만약 왼쪽 피연산자가 null 일 수도 있다면, `.`대신 `?.`을 사용하세요:

<?code-excerpt "misc/test/language_tour/classes_test.dart (safe-member-access)"?>
```dart
// p가 non-null이라면, a의 값을 p의 y의 값으로 설정합니다.
var a = p?.y;
```


### 생성자 사용하기

*생성자*를 사용하여 객체를 생성 할 수 있습니다.
생성자의 이름은 <code><em>ClassName</em></code> 또는
<code><em>ClassName</em>.<em>identifier</em></code>가 될 수 있습니다.
예를 들면, 다음의 예제에서 `Point` 객체를 `Point()`와 `Point.fromJson()` 생성자를 사용하여 생성합니다:

<?code-excerpt "misc/test/language_tour/classes_test.dart (object-creation)" replace="/ as .*?;/;/g"?>
```dart
var p1 = Point(2, 2);
var p2 = Point.fromJson({'x': 1, 'y': 2});
```

다음 코드는 같은 결과를 생성하지만,
생성자 이름에 선택적인 키워드인 `new`를 사용하였습니다:

<?code-excerpt "misc/test/language_tour/classes_test.dart (object-creation-new)" replace="/ as .*?;/;/g"?>
```dart
var p1 = new Point(2, 2);
var p2 = new Point.fromJson({'x': 1, 'y': 2});
```

몇몇 클래스는 [상수 생성자][constant constructors]를 제공합니다.
상수 생성자를 사용하여 컴파일 타임 상수를 생성하고 싶다면, 생성자 이름 앞에 `const`를 사용하세요:

<?code-excerpt "misc/test/language_tour/classes_test.dart (const)"?>
```dart
var p = const ImmutablePoint(2, 2);
```

다음과 같이 두개의 동일한 컴파일 타임 상수를 생성하는 것은, 하나의 동일한 인스턴스를 생성합니다.

<?code-excerpt "misc/test/language_tour/classes_test.dart (identical)"?>
```dart
var a = const ImmutablePoint(1, 1);
var b = const ImmutablePoint(1, 1);

assert(identical(a, b)); // 둘은 같은 인스턴스입니다!
```

_상수 컨텍스트 (constant context)_ 안에서, 생성자나 리터럴 뒤의 `const`는 생략이 가능합니다. 
상수 map을 생성하는 다음 코드를 살펴봅시다:

<?code-excerpt "misc/test/language_tour/classes_test.dart (const-context-withconst)" replace="/pointAndLine1/pointAndLine/g"?>
```dart
// 불필요한 const 키워드가 많습니다.
const pointAndLine = const {
  'point': const [const ImmutablePoint(0, 0)],
  'line': const [const ImmutablePoint(1, 10), const ImmutablePoint(-2, 11)],
};
```

`const`를 선언 할 때 첫 번째 `const`를 제외하고 다른 `const`들은 생략 할 수 있습니다:

<?code-excerpt "misc/test/language_tour/classes_test.dart (const-context-noconst)" replace="/pointAndLine2/pointAndLine/g"?>
```dart
// 상수 컨텍스트를 만들어주는 하나의 const만 사용하면 됩니다.
const pointAndLine = {
  'point': [ImmutablePoint(0, 0)],
  'line': [ImmutablePoint(1, 10), ImmutablePoint(-2, 11)],
};
```

상수 생성자가 상수 컨텍스트의 밖에 존재하고 `const` 없이 호출되면,
**상수가 아닌 객체 (non-constant object)** 가 생성됩니다:

<?code-excerpt "misc/test/language_tour/classes_test.dart (nonconst-const-constructor)"?>
```dart
var a = const ImmutablePoint(1, 1); // 상수를 생성합니다
var b = ImmutablePoint(1, 1); // 상수를 생성하지 않습니다.

assert(!identical(a, b)); // 둘은 같은 인스턴스가 아닙니다!
```


## 객체 타입 검출

런타임에서 객체의 타입을 얻고 싶다면,
[`Type`][] 객체를 반환하는 `Object`의 프로퍼티인 `runtimeType`을 사용하세요.

<?code-excerpt "misc/test/language_tour/classes_test.dart (runtimeType)"?>
```dart
print('The type of a is ${a.runtimeType}');
```

{{site.alert.warn}}
  객체의 타입을 테스트하려면, `runtimeType` 대신 [타입 테스트 연산자][type test operator]를 사용하세요.
  프로덕션 환경에서, `object is Type` 테스트가 `object.runtimeType == Type` 테스트 보다 더 안전합니다.
{{site.alert.end}}

여기까지 클래스 _사용법_ 에 대해 알아보았습니다.
나머지 섹션에서는 _구현법_ 에 대해 알아보겠습니다.


## 인스턴스 변수

인스턴스 변수는 다음과 같이 선언합니다:

<?code-excerpt "misc/lib/language_tour/classes/point_with_main.dart (class)"?>
```dart
class Point {
  double? x; // 초기값이 null인 인스턴스 변수 x를 선언.
  double? y; // 초기값이 null인 y 선언.
  double z = 0; // 초기값이 0인 z 선언.
}
```

초기화되지 않은 인스턴스 변수는 `null` 값을 가집니다.

모든 인스턴스 변수는 내부적으로 *getter* 메서드를 생성합니다.
Non-final 변수 그리고 이니셜라이저가 없는
`late final` 인스턴스 변수 또한 내부적으로 *setter* 메서드를 생성합니다.
더 자세히 알고 싶다면, [Getter와 setter][Getters and setters]를 참고하세요.

Non-`late` 변수가 선언된 동시에 초기화되면
인스턴스가 생성될 때, 생성자와 해당 이니셜라이저 목록이 실행되기 전에 값이 설정됩니다.
결과적으로, non-`late` 인스턴스 변수 이니셜라이저는 `this`에 접근할 수 없습니다.

<?code-excerpt "misc/lib/language_tour/classes/point_with_main.dart (class+main)" replace="/(double .*?;).*/$1/g" plaster="none"?>
```dart
class Point {
  double? x; // 초기값이 null인 인스턴스 변수 x 선언.
  double? y; // 초기값이 null인 y 선언.
}

void main() {
  var point = Point();
  point.x = 4; // x의 setter 메서드를 사용합니다.
  assert(point.x == 4); // x의 getter 메서드를 사용합니다.
  assert(point.y == null); // y의 디폴트 값은 null입니다.
}
```

인스턴스 변수는 `final`로 선언할 수 있고, 그런 경우에는 단 한 번만 값이 정확하게 할당됩니다.
`final`과 non-`late` 인스턴스 변수를 선언할 때 생성자 매개변수나,
생성자의 [이니셜라이저 목록][initializer list]를 사용하여 초기화하세요:

<?code-excerpt "misc/lib/effective_dart/usage_good.dart (field-init-at-decl)"?>
```dart
class ProfileMark {
  final String name;
  final DateTime start = DateTime.now();

  ProfileMark(this.name);
  ProfileMark.unnamed() : name = '';
}
```

생성자 바디가 시작된 후에 `final` 인스턴스 변수의 값을 할당하고 싶다면, 다음 중 하나를 사용하세요:

* [factory 생성자][factory constructor]를 사용하세요.
* `late final`를 [_주의해서_][late-final-ivar] 사용하세요: initializer가 없는
`late final`는 API에 setter를 추가합니다.

## 암묵적 인터페이스

클래스는 클래스의 모든 인스턴스 멤버와 클래스가 구현하는 모든 인터페이스를 포함하는 인터페이스를 암시적으로 정의합니다.
B 클래스를 상속받지 않은 A 클래스가 B의 API를 사용하고 싶다면 B 인터페이스를 구현해야 합니다.

하나의 클래스는 `implements`문 안에 하나 혹은 여러 개의 인터페이스를 구현하고,
인터페이스에 필요한 API들을 제공합니다:

<?code-excerpt "misc/lib/language_tour/classes/impostor.dart"?>
```dart
// person. 암묵적 인터페이스는 greet()을 포함합니다.
class Person {
  // 인터페이스의 안에 있지만 해당 라이브러리에서만 확인이 가능합니다.
  final String _name;

  // 생성자이기 때문에 인터페이스에 없습니다.
  Person(this._name);

  // 인터페이스에 있습니다.
  String greet(String who) => 'Hello, $who. I am $_name.';
}

// Person 인터페이스의 구현.
class Impostor implements Person {
  String get _name => '';

  String greet(String who) => 'Hi $who. Do you know who I am?';
}

String greetBob(Person person) => person.greet('Bob');

void main() {
  print(greetBob(Person('Kathy')));
  print(greetBob(Impostor()));
}
```

다음은 여러 개의 인터페이스를 가지는 클래스입니다:

<?code-excerpt "misc/lib/language_tour/classes/misc.dart (point_interfaces)"?>
```dart
class Point implements Comparable, Location {...}
```


## 클래스 변수와 메서드

`static` 키워드를 사용해 클래스 와이드한 변수와 메소드를 구현하세요.

### 정적 변수

정적 변수(클래스 변수)는 클래스 와이드한 상수와, 상태를 정의할 때 유용합니다:

<?code-excerpt "misc/lib/language_tour/classes/misc.dart (static-field)"?>
```dart
class Queue {
  static const initialCapacity = 16;
  // ···
}

void main() {
  assert(Queue.initialCapacity == 16);
}
```

정적 변수는 사용하기 전에는 초기화되지 않습니다.

{{site.alert.note}}
  이 페이지는 [스타일 가이드 추천](/guides/language/effective-dart/style#identifiers)
  에서 선호하는 `lowerCamelCase`를 상수 선언에 사용합니다.
{{site.alert.end}}

### 정적 메서드

정적 메소드(클래스 메소드)는 인스턴스 위에서 실행되지 않기 때문에 `this`에 접근 할 수 없지만,
정적 변수에 대한 접근은 가능합니다.
다음 코드는 클래스에서 직접 정적 메소드를 실행합니다:

<?code-excerpt "misc/lib/language_tour/classes/point_with_distance_method.dart"?>
```dart
import 'dart:math';

class Point {
  double x, y;
  Point(this.x, this.y);

  static double distanceBetween(Point a, Point b) {
    var dx = a.x - b.x;
    var dy = a.y - b.y;
    return sqrt(dx * dx + dy * dy);
  }
}

void main() {
  var a = Point(2, 2);
  var b = Point(4, 4);
  var distance = Point.distanceBetween(a, b);
  assert(2.8 < distance && distance < 2.9);
  print(distance);
}
```

{{site.alert.note}}
  범용적으로 자주 쓰이는 기능을 구현하기 위해서는 정적 메서드 대신 최상위 메서드를 사용하는 것을 고려해보세요.
{{site.alert.end}}

정적 메소드를 컴파일 타임 상수로 사용 할 수 있습니다.
예를 들어, 상수 생성자의 매개변수로 정적 메소드를 넘겨 줄 수 있습니다.


[`Object`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Object-class.html
[top-and-bottom]: /null-safety/understanding-null-safety#top-and-bottom
[Extension methods]: /language/extension-methods
[Class modifiers]: /language/class-modifiers
[constant constructors]: /language/constructors#constant-constructors
[`Type`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Type-class.html
[type test operator]: /language/operators#타입-테스트-연산자
[Getters and setters]: /language/methods#getter-setter
[initializer list]: /language/constructors#이니셜라이저-리스트
[factory constructor]: /language/constructors#factory-생성자
[late-final-ivar]: /guides/language/effective-dart/design#avoid-public-late-final-fields-without-initializers
