---
title: Dart 언어 소개
description: Dart 프로그램과 주요 개념에 대한 간결한 소개.
short-title: Dart 언어 기초
nextpage:
  url: /language/variables
  title: 변수
---

이 페이지에서는 Dart 언어의 주요 기능에 대한 예제를 통해 Dart 언어를 간결하게 소개합니다.

Dart 언어에 대해 더 자세히 배우고 싶다면,
왼쪽 사이드 메뉴의 **언어**에 나열된 토픽들을 참고하세요.

For coverage of Dart's core libraries,
check out the [core library documentation](/libraries).
You can also try the [Dart cheatsheet codelab](/codelabs/dart-cheatsheet),
for a more hands-on introduction.


## Hello World

모든 앱은 코드의 실행이 시작되는 최상위 `main()` 함수를 가지고 있습니다.
명시적으로 값을 반환하지 않는 함수는 `void`를 반환 타입으로 가집니다.
콘솔에 텍스트를 띄우고 싶다면, 최상위 `print()` 함수를 사용하세요:

<?code-excerpt "misc/test/samples_test.dart (hello-world)"?>
```dart
void main() {
  print('Hello, World!');
}
```


커맨드 라인 인수에 대한 선택적 매개 변수를 포함하여 Dart의 [`main()` 함수][]에
대해 자세히 알아보세요.

[`main()` 함수]: /language/functions#the-main-function

## 변수

[타입 안전성이 높은](https://dart.dev/language/type-system) Dart 코드일지라도,
`var`를 사용하여 명시적으로 타입을 지정하지 않고 변수를 선언할 수 있습니다.
타입 추론 덕분에 이런 변수들의 타입은 초기 값을 통해 결정됩니다.

<?code-excerpt "misc/test/samples_test.dart (var)"?>
```dart
var name = 'Voyager I';
var year = 1977;
var antennaDiameter = 3.7;
var flybyObjects = ['Jupiter', 'Saturn', 'Uranus', 'Neptune'];
var image = {
  'tags': ['saturn'],
  'url': '//path/to/saturn.jpg'
};
```

정적 타입, `final`, `const` 키워드 디폴트 값을 포함한 Dart의 변수에 대해 
[더보기](/language/variables).

## 흐름 제어문

Dart는 자주 사용하는 흐름 제어문을 지원합니다:

<?code-excerpt "misc/test/samples_test.dart (control-flow)"?>
```dart
if (year >= 2001) {
  print('21st century');
} else if (year >= 1901) {
  print('20th century');
}

for (final object in flybyObjects) {
  print(object);
}

for (int month = 1; month <= 12; month++) {
  print(month);
}

while (year < 2016) {
  year += 1;
}
```

Dart의 제어 흐름문에 대해 더 자세히 알고 싶다면,
[`break`와 `continue`](/language/loops),
[`switch`와 `case`](/language/branches),
[`assert`](/language/error-handling#assert)를 참고하세요.

## 함수

함수의 인자와 반환값의 타입을 지정하는 것을
[추천합니다](/effective-dart/design#타입):

<?code-excerpt "misc/test/samples_test.dart (functions)"?>
```dart
int fibonacci(int n) {
  if (n == 0 || n == 1) return n;
  return fibonacci(n - 1) + fibonacci(n - 2);
}

var result = fibonacci(20);
```

단일 표현식을 가지는 함수를 구현할 때 `=>` (_화살표_) 문법은 유용합니다.
이 문법은 익명 함수를 인자로 넘겨줄 때 특히 유용합니다:

<?code-excerpt "misc/test/samples_test.dart (arrow)"?>
```dart
flybyObjects.where((name) => name.contains('turn')).forEach(print);
```

이 코드는 익명 함수를 보여주는 것 외에도 (`where()`의 인자),
함수를 인자로 사용할 수 있음을 보여줍니다:
최상위 `print()` 함수는 `forEach()`의 인자가 됩니다.

Optional 매개변수, 디폴트 매개변수 값 그리고 렉시컬 스코프를 포함하는 Dart의 함수에 대해
[더보기](/language/functions).

## 주석

Dart의 주석은 보통 `//`로 시작합니다.

```dart
// 이것은 흔한 단일 주석입니다.

/// 이것은 문서 라이브러리, 클래스, 클래스의 멤버에 사용되는 문서화 주석입니다.
/// IDE 같은 툴이나 dartdoc은 문서화 주석를 특수하게 처리합니다.

/* 이런 형태의 주석도 지원합니다. */
```

문서화 도구의 작동 방식을 비록해 Dart의 주석에 대한 자세한 내용
[더보기](/language/comments)

## Imports

라이브러리에 정의된 API에 접근하고 싶다면, `import`를 사용하세요.

<?code-excerpt "misc/test/samples_test.dart (import)" plaster="none"?>
```
// 핵심 라이브러리 import
import 'dart:math';

// 외부 패키지의 라이브러리 import
import 'package:test/test.dart';

// 파일 import
import 'path/to/my_other_file.dart';
```

라이브러리 prefix, `show`와 `hide`, `deferred` 키워드를 사용한 지연 로딩을 비롯한
Dart 라이브러리와 가시성에 대해
[더보기](/language/libraries) 

## 클래스

다음은 메서드, 두개의 생성자, 세개의 프로퍼티를 가지는 클래스 예제입니다.
하나의 프로퍼티는 직접적으로 설정이 불가능하여,
변수가 아닌 getter 메서드를 사용하여 정의됩니다.
메소드는 문자열 리터럴 안에서 변수를 문자열 형태로 출력하기 위해 문자열 보간을 사용합니다.

<?code-excerpt "misc/lib/samples/spacecraft.dart (class)"?>
```dart
class Spacecraft {
  String name;
  DateTime? launchDate;

  // Read-only non-final 프로퍼티
  int? get launchYear => launchDate?.year;

  // 멤버 할당에 신택틱 슈가를 사용한 생성자
  Spacecraft(this.name, this.launchDate) {
    // 여기에 초기화 코드가 이어집니다.
  }

  // 디폴트 생성자로 포워드하는 Named 생성자.
  Spacecraft.unlaunched(String name) : this(name, null);

  // 메서드.
  void describe() {
    print('Spacecraft: $name');
    // Getter에는 타입 프로모션이 작동하지 않습니다.
    var launchDate = this.launchDate;
    if (launchDate != null) {
      int years = DateTime.now().difference(launchDate).inDays ~/ 365;
      print('Launched: $launchYear ($years years ago)');
    } else {
      print('Unlaunched');
    }
  }
}
```

문자열 보간, 리터럴, 표현식과 `toString()` 메소드를 포함하여 문자열에 대해 [더보기](/language/built-in-types#strings).
`Spacecraft` 클래스를 다음과 같이 사용할 수 있습니다:

<?code-excerpt "misc/test/samples_test.dart (use class)" plaster="none"?>
```dart
var voyager = Spacecraft('Voyager I', DateTime(1977, 9, 5));
voyager.describe();

var voyager3 = Spacecraft.unlaunched('Voyager III');
voyager3.describe();
```

Getter, setter, 리디렉팅 생성자, `factory` 생성자, 선택적인 `new`와 `const`, initializer list
를 비롯하여 Dart의 클래스에 대해 [더보기](/language/classes).

## Enum

Enum은 해당 타입의 다른 인스턴스가 없도록 미리 정의된 값 또는 인스턴스 집합을 열거하는 수단입니다.

다음은 미리 정의된 행성 타입의 간단한 목록을 정의하는 `enum`의 예제입니다:

<?code-excerpt "misc/lib/samples/spacecraft.dart (simple-enum)"?>
```dart
enum PlanetType { terrestrial, gas, ice }
```

다음은 정의된 상수 인스턴스 집합, 즉 태양계의 행성에 대해 설명하는
클래스의 발전된 enum 선언 예제입니다.

<?code-excerpt "misc/lib/samples/spacecraft.dart (enhanced-enum)"?>
```dart
/// 우리 태양계에 있는 행성들과 행성들의 프로퍼티에 대한 Enum
enum Planet {
  mercury(planetType: PlanetType.terrestrial, moons: 0, hasRings: false),
  venus(planetType: PlanetType.terrestrial, moons: 0, hasRings: false),
  // ···
  uranus(planetType: PlanetType.ice, moons: 27, hasRings: true),
  neptune(planetType: PlanetType.ice, moons: 14, hasRings: true);

  /// 상수를 생성하는 생성자
  const Planet(
      {required this.planetType, required this.moons, required this.hasRings});

  /// 모든 인스턴스 변수들은 final
  final PlanetType planetType;
  final int moons;
  final bool hasRings;

  /// 발전된 enum은 getter와 다른 메서드를 지원합니다.
  bool get isGiant =>
      planetType == PlanetType.gas || planetType == PlanetType.ice;
}
```

다음과 같이 `Planet` enum을 사용할 것입니다:

<?code-excerpt "misc/test/samples_test.dart (use enum)" plaster="none"?>
```dart
final yourPlanet = Planet.earth;

if (!yourPlanet.isGiant) {
  print('Your planet is not a "giant planet".');
}
```

발전된 enum의 요구 사항, 자동으로 도입된 프로퍼티, 열거된 값을 이름으로 접근,
switch문 지원 등을 포함한 Dart의 enum에 대해 
[더보기](/language/enum)


## 상속

Dart는 단일 상속이 가능합니다.

<?code-excerpt "misc/lib/samples/spacecraft.dart (extends)"?>
```dart
class Orbiter extends Spacecraft {
  double altitude;

  Orbiter(super.name, DateTime super.launchDate, this.altitude);
}
```

선택적인 `@override` 어노테이션, 클래스 확장 등에 대해 
[더보기](/language/extend) 


## Mixin

Mixin은 여러 클래스 계층에서 코드를 재사용하는 방법입니다.
다음은 mixin을 선언하는 코드입니다:

<?code-excerpt "misc/lib/samples/spacecraft.dart (mixin)"?>
```dart
mixin Piloted {
  int astronauts = 1;

  void describeCrew() {
    print('Number of astronauts: $astronauts');
  }
}
```

Mixin의 기능을 클래스에 추가하고 싶다면, 해당 클래스를 mixin으로 확장하면 됩니다.

<?code-excerpt "misc/lib/samples/spacecraft.dart (mixin-use)" replace="/with/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
class PilotedCraft extends Spacecraft [!with!] Piloted {
  // ···
}
{% endprettify %}

이제 `PilotedCraft`는 `astronauts` 필드와 `describeCrew()` 메서드를 가집니다.

Mixin에 대해
[더보기](/language/mixins).


## 인터페이스와 추상 클래스

대신, 모든 클래스가 암묵적으로 인터페이스를 정의합니다.
그리므로, 모든 클래스를 implement 할 수 있습니다.

<?code-excerpt "misc/lib/samples/spacecraft.dart (implements)"?>
```dart
class MockSpaceship implements Spacecraft {
  // ···
}
```

[암묵적 인터페이스](/language/classes#암묵적-인터페이스)와
명시적 [`interface` 키워드](/language/class-modifiers#interface)를 참고하세요.

구체적인 클래스로 확장 또는 구현되는 추항 클래스를 생성하는 것이 가능합니다.
추상 클래스는 바디가 비어있는 추상 메서드를 가지는 것이 가능합니다.
Abstract classes can contain abstract methods (with empty bodies).

<?code-excerpt "misc/lib/samples/spacecraft.dart (abstract)" replace="/abstract/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
[!abstract!] class Describable {
  void describe();

  void describeWithEmphasis() {
    print('=========');
    describe();
    print('=========');
  }
}
{% endprettify %}

`Describable`를 확장하는 모든 클래스는, 확장자의 `describe()` 구현을 호출하는
`describeWithEmphasis()` 메서드를 가집니다.

추상 클래스와 메서드에 대해
[더보기](/language/class-modifiers#추상-클래스).


## Async

`async`과 `await`를 사용해 콜백 지옥에서 벗어나고 코드의 가독성을 높힐 수 있습니다.

<?code-excerpt "misc/test/samples_test.dart (async)" replace="/async/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
const oneSecond = Duration(seconds: 1);
// ···
Future<void> printWithDelay(String message) [!async!] {
  await Future.delayed(oneSecond);
  print(message);
}
{% endprettify %}

위의 메서드는 다음과 같습니다:

<?code-excerpt "misc/test/samples_test.dart (Future.then)"?>
```dart
Future<void> printWithDelay(String message) {
  return Future.delayed(oneSecond).then((_) {
    print(message);
  });
}
```

다음 예제가 보여주듯이, `async`과 `await`는 가독성이 높은
비동기 코드를 작성하는 것을 도와줍니다.

<?code-excerpt "misc/test/samples_test.dart (await)"?>
```dart
Future<void> createDescriptions(Iterable<String> objects) async {
  for (final object in objects) {
    try {
      var file = File('$object.txt');
      if (await file.exists()) {
        var modified = await file.lastModified();
        print(
            'File for $object already exists. It was modified on $modified.');
        continue;
      }
      await file.create();
      await file.writeAsString('Start describing $object in this file.');
    } on IOException catch (e) {
      print('Cannot create description for $object: $e');
    }
  }
}
```
`async*`를 사용해 가독성 높은 코드로 스트림을 생성할 수 있습니다.

<?code-excerpt "misc/test/samples_test.dart (async*)"?>
```dart
Stream<String> report(Spacecraft craft, Iterable<String> objects) async* {
  for (final object in objects) {
    await Future.delayed(oneSecond);
    yield '${craft.name} flies by $object';
  }
}
```

`async` 함수, `Future`, `Stream` 그리고 비동기 루프 (`await for`)을 비롯한
비동기 지원에 대해
[더보기](/language/async)


## 예외

예외를 발생시키고 싶다면, `throw`를 사용하세요:

<?code-excerpt "misc/test/samples_test.dart (throw)"?>
```dart
if (astronauts == 0) {
  throw StateError('No astronauts.');
}
```

예외를 캐치하고 싶다면, `try`문을 `on` 또는 `catch` (혹은 모두)와 함께 사용하세요:

<?code-excerpt "misc/test/samples_test.dart (try)" replace="/on.*e\)/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
Future<void> describeFlybyObjects(List<String> flybyObjects) async {
  try {
    for (final object in flybyObjects) {
      var description = await File('$object.txt').readAsString();
      print(description);
    }
  } [!on IOException catch (e)!] {
    print('Could not describe object: $e');
  } finally {
    flybyObjects.clear();
  }
}
{% endprettify %}

위의 코드는 비동기적이라는 것을 알아두세요:
`try`는 `async` 함수 코드와 동기식 코드에서 모두 동작합니다.

스택 트레이스, `rethrow`, 그리고 `Error`와 `Exception`의 차이를 비롯하여
예외에 대해 
[더보기](/language/error-handling#예외).


## 주요 컨셉

Dart 언어를 학습 할 때 다음을 잘 기억하세요:

-   변수로 할당 할 수 있는 모든 것은 *객체*이고, 모든 객체는 *클래스*의 인스턴스입니다.
    숫자, 함수 그리고 `null`까지 모두 객체입니다.
    `null`을 제외하고 ([견고한 null 안전성][ns]를 활성화했다면),
     모든 객체들은 [`Object`][] 클래스를 상속받습니다.

    {{site.alert.version-note}}
      [Null 안전성][ns]는 Dart 2.12에 처음 도입되었습니다.
      null safety를 사용하려면 최소 2.12의 [language version][]이 필요합니다.
    {{site.alert.end}}

-   Dart는 타입에 엄격하지만, 추론할 수 있기 때문에 타입 어노테이션은 자율에 맡깁니다.
    `var number = 101`의 `number`는 `int` 타입으로 추론됩니다.

-   [Null 안전성][ns]을 활성화했다면,
    변수가 null 값을 갖는 것을 허락하지 않았을 때 
    해당 변수가 null 값을 가질 수 없습니다.
    타입의 끝에 물음표 (`?`)를 추가하면 해당 변수를
    nullable로 만들 수 있습니다.
    예를 들어, `int?` 타입의 변수는 정수 또는 `null` 값을 가집니다.
    만약 표현식이 null로 평가되지 않지만 Dart가 이에 동의하지 않는다는 것을
    _알고_ 있다면, `!`을 추가하여 null이 아니라고 명시 할 수 있습니다
    (null이라면 예외를 발생시킵니다).
    예시: `int x = nullableButNotNullInt!`

-   어떤 타입이든 적용이 가능하다고 명시하고 싶다면,
    `Object?` (null 안전성을 활성화했다면)
    또는 `Object`를 타입으로 설정하면 됩니다.
    런타임까지 타입 체킹을 미뤄야 한다면,
    [특수 타입인 `dynamic`][ObjectVsDynamic]을 사용하세요.

-   Dart는 `List<int>` (정수의 list)
    또는 `List<Object>` (아무 타입의 list) 같은 제네릭 타입을 지원합니다.

-   Dart는 클래스나 객체에 묶여있는 함수들
    (각각 *static*, *instance* 메서드) 뿐만 아니라,
    `main()` 같은 최상위 함수를 지원합니다.
    *중첩 함수* 또는 *지역 함수*처럼 함수 안에 함수를 생성할 수 있습니다.

-   유사하게, Dart는 클래스나 객체에 묶여있는 변수들 (각각 static, instance 변수)
    뿐만 아니라, 최상위 *변수* 또한 지원합니다. 
    Instance 변수들은 *필드*, *프로퍼티* 로도 알려져있습니다.

-   Java와 다르게, Dart는 `public`, `projected` 그리고 `private` 같은 키워드가 없습니다.
    식별자가 언더 스코어 (`_`)로 시작한다면, 이것은 해당 라이브러리에 귀속된(private) 것입니다.
    더 자세한 정보를 원한다면, [라이브러리와 임포트][Libraries and imports]
    를 참고하세요.

-   *식별자*는 문자 또는 언더 스코어 (`_`)로 시작 할 수 있고,
    어떠한 문자와 숫자의 결합이 가능합니다.

-   Dart는 런타임 값을 가지는 *식 (expression)*과
    그렇지 않은 *문 (statement)*을 가지고 있습니다.
    예를 들어, [조건 표현식][conditional expression]인
    `condition ? expr1 : expr2` 은 `expr1` 또는 `expr2`의 값을 가집니다.
    위의 표현식과 값을 가지지 않는 
    [if-else 문][if-else statement]을 비교해봅시다.
    문은 때로 하나 혹은 그 이상의 식을 포함하지만,
    식은 직접적으로 문을 포함할 수 없습니다.

-   Dart tools는 _경고_ 와 _에러_ 로 이슈를 리포트 해줍니다.
    경고는 코드가 제대로 작동하지 않을 수도 있다는 것을 의미하지만,
    프로그램의 실행을 막지 않습니다. 에러는 컴파일 타임 또는 런타임으로 구분됩니다.
    컴파일 타임 에러는 코드가 실행되는 것을 막습니다;
    런타임 에러는 코드가 실행되는 동안 [예외][exception]를 발생시킵니다.


## 추가적인 리소스

You can find more documentation and code samples in the
[core library documentation](/libraries/dart-core)
and the [Dart API reference]({{site.dart-api}}).
This site's code follows the conventions in the
[Dart style guide](/effective-dart/style).

[Dart language specification]: /guides/language/spec
[Comments]: /language/comments
[built-in types]: /language/built-in-types
[Strings]: /language/built-in-types#strings
[main() 함수]: /language/functions#main-함수
[ns]: /null-safety
[`Object`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Object-class.html
[language version]: /guides/language/evolution#language-versioning
[ObjectVsDynamic]: /effective-dart/design#avoid-using-dynamic-unless-you-want-to-disable-static-checking
[Libraries and imports]: /language/libraries
[conditional expression]: /language/operators#조건-표현식
[if-else statement]: /language/branches#if
[exception]: /language/error-handling#예외
