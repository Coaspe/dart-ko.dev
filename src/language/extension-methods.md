---
title: 확장 메서드
description: 존재하는 API에 기능을 추가하는 법을 배웁니다.
prevpage:
  url: /language/enums
  title: 열거
nextpage:
  url: /language/callable-objects
  title: 호출 가능한 객체
---

{% include yt_shims.liquid %}

Extension methods add functionality to existing libraries.
You might use extension methods without even knowing it.
For example, when you use code completion in an IDE,
it suggests extension methods alongside regular methods.

If watching videos helps you learn,
check out this overview of extension methods.

<iframe
  {{yt-std-size}}
  title="Learn about extension methods in Dart"
  src="{{yt-embed}}/D3j0OSfT9ZI"
  {{yt-set}}>
</iframe>

_비디오를 통해 학습하고 싶다면 위의 영상을 참고하세요._

## 개요

다른 사람들이 개발한 API를 사용하거나
많이 쓰이지 않는 라이브러리를 구현할 때,
해당 API를 변경하는 것은 실용적이지 않거나 불가능할 때가 있습니다.
하지만 기능을 추가하고 싶다면 어떻게 해야 할까요?

예를 들어, 다음과 같이 문자열을 정수로 파싱하는 코드를 생각해 봅시다:

```dart
int.parse('42')
```

`String`에 다음과 같은 기능이 있다면 더 짧고 쉽게 처리가 가능할 것입니다:

```dart
'42'.parseInt()
```

위의 코드를 사용하려면
`String` 클래스의 확장이 포함된 라이브러리를 임포트하면 됩니다:

<?code-excerpt "extension_methods/lib/string_extensions/usage_simple_extension.dart (basic)" replace="/  print/print/g"?>
```dart
import 'string_apis.dart';
// ···
print('42'.parseInt()); // Extension 메서드 사용.
```

확장은 메서드뿐만 아니라,
getter, setter, 연산자 같은 멤버에도 사용이 가능합니다.
또한, 확장은 이름을 가지고 있어서 API 충돌이 발생할 때 유용합니다.
다음은 문자열에 사용 가능한 `NumberParsing`이라는 extension을 사용해,
extension 메서드 `parseInt()`를 구현하는 예제입니다:

<?code-excerpt "extension_methods/lib/string_extensions/string_apis.dart (parseInt)"?>
```dart
extension NumberParsing on String {
  int parseInt() {
    return int.parse(this);
  }
  // ···
}
```
<div class="prettify-filename">lib/string_apis.dart</div>

다음 섹션은 확장 메서드의 _사용법_,
그 섹션에서는 확장 메서드의 _구현법_ 을 설명합니다. 


## 확장 메서드 사용법

모든 Dart 코드와 마찬가지로 확장 메서드는 라이브러리 안에 있습니다.
우리는 이미 확장 메서드를 어떻게 사용하는지 배웠습니다.
해당 메서드가 있는 라이브러리를 임포트하고 일반 메서드처럼 사용하면 됩니다:

<?code-excerpt "extension_methods/lib/string_extensions/usage_simple_extension.dart (import-and-use)" replace="/  print/print/g"?>
```dart
// String에 사용 가능한 확장이 있는 라이브러리를 import 합니다.
import 'string_apis.dart';
// ···
print('42'.padLeft(5)); // String 메서드 사용.
print('42'.parseInt()); // Extension 메서드 사용.
```

확장 메서드를 사용하기 위해 알아야할 것은 모두 확인했습니다.
코드를 작성할 때, 메서드가 `dynamic`과 반대로 어떻게 정적 타입에 의존하는지
그리고 어떻게 [API 충돌](#api-충돌)을 해결하는지 알고 있어야 합니다.

### 정적 타입과 dynamic

`dynamic` 타입의 변수에 확장 메서드를 사용할 수 없습니다.
예를 들어, 다음 코드는 런타임 예외를 발생시킵니다.

<?code-excerpt "extension_methods/lib/string_extensions/usage_simple_extension.dart (dynamic)" plaster="none" replace="/  \/\/ print/print/g"?>
```dart
dynamic d = '2';
print(d.parseInt()); // 런타임 예외: NoSuchMethodError
```

확장 메스드는 Dart의 타입 추론을 _사용_ 합니다.
다음 코드의 `v`는 `String` 타입으로 추론되어 정상적으로 작동합니다:

<?code-excerpt "extension_methods/lib/string_extensions/usage_simple_extension.dart (var)"?>
```dart
var v = '2';
print(v.parseInt()); // Output: 2
```

리시버의 정적 타입에 대해 확장 메서드가 생성되기 때문에 `dynamic`에 사용할 수 없습니다.
확장 메서드는 정적으로 생성되기 때문에, static 함수를 호출하는 것만큼 빠릅니다.

정적 타입과 `dynamic`에 대해 더 알고 싶다면,
[Dart 타입 시스템](/language/type-system)을 참고하세요.

### API 충돌

확장 멤버가 인터페이스나 다른 확장 멤버와 충돌이 발생한다면,
다음과 같은 옵션이 존재합니다.

노출되는 API를 제한하는 `show` 또는 `hide`를 사용하여
충돌을 일으킨 확장을 import하는 방법을 바꾸는 것이 한 가지 옵션입니다:

<?code-excerpt "extension_methods/lib/string_extensions/usage_import.dart" replace="/  //g"?>
```dart
// String 확장 메서드인 parseInt()를 정의하는 라이브러리.
import 'string_apis.dart';

// parseInt()를 정의하는 또다른 라이브러리.
// hide를 사용하여 NumberParsing2의 확장 메서드를 숨깁니다.
import 'string_apis_2.dart' hide NumberParsing2;

// ···
// 'string_apis.dart'에 정의된 parseInt()를 사용합니다.
print('42'.parseInt());
```

두 번째로는 확장을 명시적으로 사용하는 것입니다.
해당 확장이 래퍼(wrapper) 클래스처럼 사용됩니다:

<?code-excerpt "extension_methods/lib/string_extensions/usage_explicit.dart" replace="/  //g"?>
```dart
// 두 라이브러리 모두 parseInt()를 가지는
// String에 대한 확장을 가지고 있고, 해당 확장들은 다른 이름을 가지고 있습니다.
import 'string_apis.dart'; // Contains NumberParsing extension.
import 'string_apis_2.dart'; // Contains NumberParsing2 extension.

// ···
// print('42'.parseInt()); // 작동하지 않습니다.
print(NumberParsing('42').parseInt());
print(NumberParsing2('42').parseInt());
```

두 확장이 같은 이름을 가지고 있다면,
프리픽스를 사용하여 import 해도 좋습니다:

<?code-excerpt "extension_methods/lib/string_extensions/usage_prefix.dart" replace="/  //g"?>
```dart
// 두 라이브러리 모두 parseInt() 확장 메서드를 가지는 NumberParsing 메서드를 가지고 있습니다.
// 'string_apis_3.dart'의 NumberParsingOne 확장도 parseNum()을 가지고 있습니다.
import 'string_apis.dart';
import 'string_apis_3.dart' as rad;

// ···
// print('42'.parseInt()); // Doesn't work.

// string_apis.dart의 ParseNumbers 확장을 사용합니다.
print(NumberParsing('42').parseInt());

// string_apis_3.dart의 ParseNumbers 확장을 사용합니다.
print(rad.NumberParsing('42').parseInt());

// string_apis_3.dart만 parseNum()을 가지고 있습니다.
print('42'.parseNum());
```

위의 예제처럼,
프리픽스를 사용해서 import 했더라도 암묵적으로 확장 메서드를 사용할 수 있습니다.
프리픽스는 확장 메서드를 명시적으로 사용하여 이름에 충돌이 생길 때 사용해야합니다.


## 확장 메서드 구현

다음 문법을 사용하여 확장을 생성하세요:

```
extension <extension name>? on <type> {
  (<member definition>)*
}
```

다음은 `String` 클래스에 대해 확장을 구현하는 예제입니다:

<?code-excerpt "extension_methods/lib/string_extensions/string_apis.dart"?>
```dart
extension NumberParsing on String {
  int parseInt() {
    return int.parse(this);
  }

  double parseDouble() {
    return double.parse(this);
  }
}
```
<div class="prettify-filename">lib/string_apis.dart</div>

연산자, getter, setter 또는 메서드가 확장의 멤버가 될 수 있습니다.
확장은 정적 필드와 정적 헬퍼 메서드를 가질 수 있습니다.
확장 선언 외부에서 정적 멤버에 접근하고 싶다면,
[클래스 변수와 메서드][class variables and methods]와 비슷하게 선언 이름을 호출하세요.

[class variables and methods]: /language/classes#class-variables-and-methods

### 이름 없는 확장

확장을 선언할 때, 이름을 생략할 수 있습니다.
이름 없는 확장은 선언된 라이브러리 안에서만 사용이 가능합니다.
이런 확장은 이름이 없기 때문에,
[API 충돌](#api-충돌)을 해결할 때
명시적으로 적용될 수 없습니다.

<?code-excerpt "extension_methods/lib/string_extensions/string_apis_unnamed.dart (unnamed)"?>
```dart
extension on String {
  bool get isBlank => trim().isEmpty;
}
```

{{site.alert.note}}
  이름 없는 확장의 정적 멤버는 확장 선언 안에서만 호출이 가능합니다.
{{site.alert.end}}

## 제네릭 확장 구현

확장은 제네릭 타입 매개변수를 가질 수 있습니다.
다음은 getter, 연산자, 메서드를 가지도록
내장 `List<T>`를 확장하는 예제입니다:

<?code-excerpt "extension_methods/lib/fancylist.dart"?>
```dart
extension MyFancyList<T> on List<T> {
  int get doubleLength => length * 2;
  List<T> operator -() => reversed.toList();
  List<List<T>> split(int at) => [sublist(0, at), sublist(at)];
}
```
타입 `T`는 메서드가 호출되는 리스트의 정적 타입을 기반으로 합니다.
{% comment %}
TODO (https://github.com/dart-lang/site-www/issues/2171):
Add more info about generic extensions. 
For example, in the following code, `T` is `PENDING` because PENDING:

[PENDING: example]

[PENDING: Explain why it matters in normal usage.]
{% endcomment %}

## 리소스

확장 메서드에 대한 더 많은 정보를 원한다면, 다음을 참고하세요:

* [Article: Dart Extension Methods Fundamentals][article]
* [Feature specification][specification]
* [Extension methods sample][sample]

[specification]: https://github.com/dart-lang/language/blob/main/accepted/2.7/static-extension-methods/feature-specification.md#dart-static-extension-methods-design
[article]: https://medium.com/dartlang/extension-methods-2d466cd8b308
[sample]: https://github.com/dart-lang/samples/tree/main/extension_methods
