---
title: 생성자
description: Dart 생성자에 대한 모든 것.
js: [{url: 'https://dartpad.dev/inject_embed.dart.js', defer: true}]
prevpage:
  url: /language/classes
  title: Classes
nextpage:
  url: /language/methods
  title: Methods
---

<?code-excerpt replace="/ *\/\/\s+ignore_for_file:[^\n]+\n//g; /(^|\n) *\/\/\s+ignore:[^\n]+\n/$1/g; /(\n[^\n]+) *\/\/\s+ignore:[^\n]+\n/$1\n/g; / *\/\/\s+ignore:[^\n]+//g; /([A-Z]\w*)\d\b/$1/g"?>

클래스와 동일한 이름을 가지는 함수를 만들어 생성자를 선언할 수 있습니다.
(선택적으로 [명명된 생성자](#명명된-생성자)
에 명시되어 있는 식별자를 사용해도 됩니다.)

제너러티브 생성자는 생성자의 가장 흔한 형태로 새 인스턴스를 생성합니다:

Use the most common constructor, the generative constructor, to create a new
instance of a class, and [initializing formal parameters](#initializing-formal-parameters)
to instantiate any instance variables, if necessary:

<?code-excerpt "misc/lib/language_tour/classes/point_alt.dart (idiomatic-constructor)" plaster="none"?>
```dart
class Point {
  double x = 0;
  double y = 0;

  // Generative constructor with initializing formal parameters:
  Point(this.x, this.y);
}
```

`this` 키워드는 현재 인스턴스를 참조합니다.

{{site.alert.note}}
  이름 사이에 충돌이 있을 때만 `this`를 사용하세요.
  없다면, Dart 스타일에서는 `this`를 생략합니다.
{{site.alert.end}}


## 생성자 인자 초기화

Dart는 생성자의 인자를 사용해 인스턴스 변수를 할당하는 간단하 패턴인
*생성자 인자 초기화*가 가능합니다. 생성자 선언에 `this.propertyName`를
직접 사용하고 바디를 생략하세요.

매개변수 초기화는 초기화 되어야만 하거나 기본 값이 주어져야하는 non-nullable
또는 `final` 인스턴스 변수에만 사용이 가능합니다.

<?code-excerpt "misc/lib/language_tour/classes/point.dart (constructor-initializer)" plaster="none"?>
```dart
class Point {
  final double x;
  final double y;

  // 생성자 바디가 실행되기 전에 x와 y 인스턴스 변수 설정.
  Point(this.x, this.y);
}
```

Initializing formal로 주어진 변수는
[초기화 리스트]((/language/constructors#initializer-list))
스코프에서 암묵적으로 final 입니다.

초기화 리스트에 작성할 수 없는 로직을 수행하고 싶다면 [팩토리 생성자](#factory-생성자)
(또는 [static method][])에 해당 로직을 작성하고, 계산된 값을 일반 생성자로 넘겨주세요. 


## 디폴트 생성자

생성자를 선언하지 않았다면, 디폴트 생성자가 주어집니다.
디폴트 생성자는 인자가 없고, 인자가 없는 부모 클래스의 생성자를 호출합니다.


## 생성자는 상속되지 않습니다

자식 클래스는 부모 클래스로 부터 생성자를 상속받지 않습니다.
생성자를 선언하지 않은 자식 클래스는 이름과 인자가 없는 디폴트 생성자만을 가집니다.

## 명명된 생성자

다수의 생성자를 구현하거나, 코드의 명확성을 더하고 싶다면 명명된 생성자를 사용하세요:

<?code-excerpt "misc/lib/language_tour/classes/point.dart (named-constructor)" replace="/Point\.\S*/[!$&!]/g" plaster="none"?>
{% prettify dart tag=pre+code %}
const double xOrigin = 0;
const double yOrigin = 0;

class Point {
  final double x;
  final double y;

  Point(this.x, this.y);

  // 명명된 생성자
  [!Point.origin()!]
      : x = xOrigin,
        y = yOrigin;
}
{% endprettify %}

부모 클래스의 생성자는 자식 클래스로 상속되지 않는다는 것을 꼭 기억하세요.
부모 클래스의 명명된 생성자는 자식 클래스로 상속되지 않습니다.
자식 클래스에서 부모 클래스와 같은 명명된 생성자를 사용하고 싶다면, 자식 클래스에서도 똑같이 구현해야 합니다.


## 부모 클래스의 Non-default 생성자 호출

자식 클래스의 생성자는 부모 클래스의 이름이 없고(unnamed), 인자가 없는(no-argument) 생성자를 디폴트로 호출합니다.
부모 클래스의 생성자는 자식 클래스 생성자 바디의 처음에 호출됩니다.
[초기화 리스트](#초기화-리스트)가 사용되면, 부모 클래스가 호출되기 전에 실행됩니다.
요약하자면, 실행 순서는 다음과 같습니다:

1. 초기화 리스트
1. 부모 클래스의 인자가 없는 생성자
1. 메인 클래스의 인자가 없는 생성자

부모 클래스가 이름과 인자가 없는 생성자를 가지고 있지 않는다면,
반드시 부모 클래스의 생성자 중 하나를 선택해서 호출해야 합니다.
생성자 바디에 콜론(`:`)을 추가해 선택한 부모 클래스의 생성자를 명시하세요.

다음 예제에서 Employee는 자신의 부모 클래스인 Person의 명명된 생성자를 호출합니다.
코드를 실행하고 싶다면 **Run**을 클릭하세요.

<?code-excerpt "misc/lib/language_tour/classes/employee.dart (super)" plaster="none"?>
```dart:run-dartpad:height-450px:ga_id-non_default_superclass_constructor
class Person {
  String? firstName;

  Person.fromJson(Map data) {
    print('in Person');
  }
}

class Employee extends Person {
  // Person은 디폴트 생성자가 없습니다;
  // super.fromJson()를 반드시 호출해야합니다.
  Employee.fromJson(super.data) : super.fromJson() {
    print('in Employee');
  }
}

void main() {
  var employee = Employee.fromJson({});
  print(employee);
  // Prints:
  // in Person
  // in Employee
  // Instance of 'Employee'
}
```

생성자가 실행되기 전에 부모 클래스의 생성자로 전해지는 인자가
평가되기 때문에 인자는 함수 호출처럼 표현식이 될 수 있습니다:

<?code-excerpt "misc/lib/language_tour/classes/employee.dart (method-then-constructor)"?>
```dart
class Employee extends Person {
  Employee() : super.fromJson(fetchDefaultData());
  // ···
}
```

{{site.alert.warning}}
  부모 클래스의 생성자로 전달되는 인수는 `this`에 접근할 수 없습니다.
  예를 들면, 인자는 정적 메서드를 호출할 수 있지만, 인스턴스 메서드는 불가능합니다.
{{site.alert.end}}

### Super 매개변수

수동으로 부모 클래스의 생성자 매개변수를 넘겨주는 것을 피하고 싶다면,
super 초기화 매개변수를 부모 클래스의 생성자로 넘겨주면 됩니다.
이 피처를 리다이렉팅 생성자와 함께 사용하는 것은 불가능합니다.
Super 초기화 매개변수는
[생성자 인자 초기화](#생성자-인자-초기화)와 비슷한 문법과 의미를 가집니다:

<?code-excerpt "misc/lib/language_tour/classes/super_initializer_parameters.dart (positional)" plaster="none"?>
```dart
class Vector2d {
  final double x;
  final double y;

  Vector2d(this.x, this.y);
}

class Vector3d extends Vector2d {
  final double z;

  // 매개변수 x와 y를 디폴트 super 생성자로 넘겨줍니다:
  // Vector3d(final double x, final double y, this.z) : super(x, y);
  Vector3d(super.x, super.y, this.z);
}
```

super 생성자 호출이 positional 인자를 가지고 있다면,
super 초기화 매개변수는 positional이 될 수 없지만
명명된 매개변수는 언제나 가능합니다:

<?code-excerpt "misc/lib/language_tour/classes/super_initializer_parameters.dart (named)" plaster="none"?>
```dart
class Vector2d {
  // ...

  Vector2d.named({required this.x, required this.y});
}

class Vector3d extends Vector2d {
  // ...

  // 매개변수 y를 명명된 super 생성자로 넘겨줍니다:
  // Vector3d.yzPlane({required double y, required this.z})
  //       : super.named(x: 0, y: y);
  Vector3d.yzPlane({required super.y, required this.z}) : super.named(x: 0);
}
```

{{site.alert.version-note}}
  Super 초기화 매개변수를 사용하는 것은 최소 2.17의 [language version][]을 요구합니다.
  이전의 버전을 사용하고 있다면, 수동으로 모든 super 생성자 매개변수를 넘겨줘야 합니다.
{{site.alert.end}}

## 초기화 리스트

생성자 바디가 실행되기 전에 부모 클래스의 생성자를 호출할 뿐만 아니라
인스턴스 변수를 초기화할 수도 있습니다. 초기화는 쉼표로 구분합니다.

<?code-excerpt "misc/lib/language_tour/classes/point_alt.dart (initializer-list)"?>
```dart
// 초기화 리스트는 생성자 바디가 실행되기 전에 인스턴스 변수를 설정합니다.
Point.fromJson(Map<String, double> json)
    : x = json['x']!,
      y = json['y']! {
  print('In Point.fromJson(): ($x, $y)');
}
```

{{site.alert.warning}}
  초기화의 오른쪽은 `this`에 접근 할 수 없습니다.
{{site.alert.end}}

개발하는 동안 `assert`를 초기화 리스트 안에 넣어서 입력에 조건을 추가 할 수 있습니다.

<?code-excerpt "misc/lib/language_tour/classes/point_alt.dart (initializer-list-with-assert)" replace="/assert\(.*?\)/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
Point.withAssert(this.x, this.y) : [!assert(x >= 0)!] {
  print('In Point.withAssert(): ($x, $y)');
}
{% endprettify %}

초기화 리스트는 final 필드를 설정 할 때 유용합니다.
다음 예제에서는 세 개의 final 필드를 초기화 리스트로 초기화합니다.
**Run**을 클릭해 코드를 실행하세요.

<?code-excerpt "misc/lib/language_tour/classes/point_with_distance_field.dart"?>
```dart:run-dartpad:height-340px:ga_id-initializer_list
import 'dart:math';

class Point {
  final double x;
  final double y;
  final double distanceFromOrigin;

  Point(double x, double y)
      : x = x,
        y = y,
        distanceFromOrigin = sqrt(x * x + y * y);
}

void main() {
  var p = Point(2, 3);
  print(p.distanceFromOrigin);
}
```


## 리다이렉팅 생성자

생성자의 목적이 오직 같은 클래스 내의 다른 생성자로의 리다이렉트(redirect)인 경우가 있습니다.
리다이렉팅 생성자의 바디는 비어있고 콜론 (:) 뒤에 나오며 클래스 이름 대신 `this`를 사용한 생성자 호출로 구성됩니다.

<?code-excerpt "misc/lib/language_tour/classes/point_redirecting.dart"?>
```dart
class Point {
  double x, y;

  // 클래스의 메인 생성자.
  Point(this.x, this.y);

  // 메인 생성자로 리디렉트.
  Point.alongXAxis(double x) : this(x, 0);
}
```


## 상수 생성자

어떤 클래스가 절대 바뀌지 않는 객체를 생성한다면, 이 객체를 컴파일 타임 상수로 만들 수 있습니다.
생성자를 `const`로 정의하고 모든 인스턴스 변수를 `final`로 선언하면 됩니다.
<?code-excerpt "misc/lib/language_tour/classes/immutable_point.dart"?>
```dart
class ImmutablePoint {
  static const ImmutablePoint origin = ImmutablePoint(0, 0);

  final double x, y;

  const ImmutablePoint(this.x, this.y);
}
```

상수 생성자가 항상 상수를 생성하는 건 아닙니다.
더 자세히 알고 싶다면, [생성자 사용하기][using constructors]를 참고하세요.


## Factory 생성자

항상 클래스의 새로운 인스턴스를 생성하지 않는 생성자를 구현하고 싶다면, `factory` 키워드를 사용하세요.
예를 들어, factory 생성자는 인스턴스를 캐시에서 반환하거나 서브타입의 인스턴스를 반환할 수 있습니다.
Factory 생성자는 final 변수를 초기화 리스트에서 다루지 않는 로직을 사용하여 초기화하는 방법으로도 사용할 수 있습니다.

{{site.alert.tip}}
  final 변수의 late 초기화를 처리하는 다른 방법으로는
  [`late final` (사용 주의!)][late-final-ivar]가 있습니다.
{{site.alert.end}}

다음 예제에서 `Logger` factory 생성자는 캐시에서 객체를 반환하고,
`Logger.fromJson` factory 생성자는 final 변수를 JSON 객체로 부터 초기화 합니다.

<?code-excerpt "misc/lib/language_tour/classes/logger.dart"?>
```dart
class Logger {
  final String name;
  bool mute = false;

  // _cache는 맨 앞의 _ 덕분에 library-private입니다.
  static final Map<String, Logger> _cache = <String, Logger>{};

  factory Logger(String name) {
    return _cache.putIfAbsent(name, () => Logger._internal(name));
  }

  factory Logger.fromJson(Map<String, Object> json) {
    return Logger(json['name'].toString());
  }

  Logger._internal(this.name);

  void log(String msg) {
    if (!mute) print(msg);
  }
}
```

{{site.alert.note}}
  Factory 생성자는 `this`에 접근할 수 없습니다.
{{site.alert.end}}

다른 생성자를 호출 할 때 처럼 factory 생성자를 호출하세요:

<?code-excerpt "misc/lib/language_tour/classes/logger.dart (logger)"?>
```dart
var logger = Logger('UI');
logger.log('Button clicked');

var logMap = {'name': 'UI'};
var loggerJson = Logger.fromJson(logMap);
```

[language version]: /guides/language/evolution#language-versioning
[using constructors]: /language/classes#using-constructors
[late-final-ivar]: /effective-dart/design#avoid-public-late-final-fields-without-initializers
[static method]: /language/classes#static-methods
