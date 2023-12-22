---
title: "효과적인 Dart: 사용법"
description: 언어 기능을 사용하여 유지 관리 가능한 코드를 작성하기 위한 가이드라인.
nextpage:
  url: /effective-dart/design
  title: 디자인
prevpage:
  url: /effective-dart/documentation
  title: 문서화
---
<?code-excerpt replace="/([A-Z]\w*)\d\b/$1/g"?>
<?code-excerpt path-base="misc/lib/effective_dart"?>

당신이 작성한 모든 Dart 코드에 해당 가이드라인을 적용할 수 있습니다.
라이브러리의 *사용자*는 이러한 지침에 대해 일부는 알 필요가 없지만,
*유지관리자*는 반드시 알아야합니다.

## 라이브러리

이 가이드라인은 여러 파일로 작성한 프로그램을 일관적이고 유지보수가 가능한 방향으로
구성하는 데 도움이 될 수 있습니다. 가이드라인을 간결하게 하기 위해 여기에서 "import"를
사용하여 `import`와 `export`를 모두 나타냅니다. 가이드라인은 이 두 가지에 동일하게 적용됩니다.

### `part of` 명령어에 문자열을 사용하십시오.

{% include linter-rule-mention.md rule="use_string_in_part_of_directives" %}

많은 Dart 개발자들은 `part`를 전혀 사용하지 않습니다. 개발자들은 라이브러리에 파일이 하나만
있을 때 코드를 더 쉽게 읽을 수 있다는 것을 깨달았습니다. `part`를 사용하여 라이브러리의 일부를
다른 파일로 분할하려는 경우, Dart는 다른 파일에 해당 라이브러리의 경로를 표시하도록 요청합니다.

이러한 이유들로, Dart는 `part of` 명령어가 속한 라이브러리의 *이름*을 사용할 수 있도록 허용합니다.
라이브러리를 명명하는 것은 이제 [권장하지 않는][discouraged] 레거시 피처입니다.
라이브러리의 이름의 사용은 해당 부분이 실제로 어떤 라이브러리에 속하는지 모호해질 수 있습니다.

[discouraged]: /effective-dart/style#dont-explicitly-name-libraries

권장하는 문법은 다른 명령어에서 사용하듯이 URI 문자열을 사용하여 라이브러리 파일을 직접 지정하는 것입니다.
`my_library.dart`라는 라이브러리가 있다고 하면, 다음과 유사할 것입니다:

<?code-excerpt "my_library.dart"?>
```dart
library my_library;

part 'some/other/file.dart';
```

그리고 부분이 되는 파일은 다음과 같이 라이브러리 파일의 URI 문자열을 사용해야합니다:

{:.good}
<?code-excerpt "some/other/file.dart"?>
```dart
part of '../../my_library.dart';
```

다음과 같이 라이브러리 이름을 사용하지마세요:

{:.bad}
<?code-excerpt "some/other/file_2.dart"?>
```dart
part of my_library;
```

### 다른 패키지의 `src` 디렉토리에 있는 라이브러리를 import하지 마십시오.

{% include linter-rule-mention.md rule="implementation_imports" %}

`lib` 아래의 `src` 디렉토리는 패키지가 자체적으로 구현한 private 라이브러리로 [지정됩니다][package guide].
패키지 관리자가 패키지를 버전화하는 방식은 이런 규칙을 고려합니다.
패키지를 손상시키지 않고 관리자는 `src` 디렉토리 아래의 코드를 자유롭게 수정할 수 있습니다.

[package guide]: /tools/pub/package-layout

즉, 다른 패키지의 private 라이브러리를 import 할 경우, 해당 패키지의 사소하고 이론적으로
브레이킹 포인트가 아닌 릴리즈가 당신의 코드를 손상시킬 수 있습니다.


### Import 경로를 `lib` 안팎으로 지정하지 마십시오.

{% include linter-rule-mention.md rule="avoid_relative_lib_imports" %}

`package:` import는 패키지가 컴퓨터의 어디에 저장되어 있든 상관없이
패키지의 `lib` 디렉토리 내부의 라이브러리에 접근할 수 있게 해줍니다.
이것이 가능하려면, `lib`가 다른 파일들과 비교하여 디스크의 상대적인 위치에 존재해야 하는 import가 불가능해야 합니다.
다시 말해, `lib` 내부 파일의 상대적인 import 경로는 `lib` 디렉토리 외부의 파일에
도달하거나 접근할 수 없으며 `lib` 외부의 라이브러리는 `lib` 디렉토리에 도달하기 위해 상대적인 경로를 사용할 수 없습니다.
위의 두 가지를 시도하면 혼란스러운 에러와 프로그램에 손상을 발생시킵니다.

예를 들어, 다음과 같은 디렉토리 구조가 있다고 생각해봅시다:

```text
my_package
└─ lib
   └─ api.dart
   test
   └─ api_test.dart
```

`api_test.dart`가 두가지 방법으로 `api.dart`를 import 합니다:

{:.bad}
```dart
import 'package:my_package/api.dart';
import '../lib/api.dart';
```

Dart는 위의 두가지 import가 전혀 관계가 없는 라이브러리에 대한 import로 간주합니다.
Dart와 개발자가 이러한 혼란을 피하기 위해서는, 다음과 같은 두가지 규칙을 지켜야합니다:

* Import 경로에 `/lib/`를 사용하지 마십시오.
* `lib` 디렉토리에서 벗어나기 위해 `../`를 사용하지 마십시오.

패키지의 `lib` 디렉토리에 접근하고 싶다면
(같은 패키지의 `test` 다른 최상위 디렉토리에서도 마찬가지),
`package:` import를 사용하세요.

{:.good}
```dart
import 'package:my_package/api.dart';
```

패키지는 `lib` 디렉토리에 접근해서는 안 되며
패키지의 다른 위치에서 라이브러리를 import 해서는 안 됩니다.


### 상대 경로를 사용하여 import하는 것을 지향하십시오.

{% include linter-rule-mention.md rule="prefer_relative_imports" %}

이전 규칙이 적용되지 않을 때, 항상 이 규칙을 따르세요.
Import가 `lib`에 도달하지 *않을* 때는, 상대 경로를 사용하여 import하는 것을 선호하십시오.
더 깔끔합니다.
예를 들어, 다음과 같은 디렉토리 구조가 있다고 생각해봅시다:

```text
my_package
└─ lib
   ├─ src
   │  └─ stuff.dart
   │  └─ utils.dart
   └─ api.dart
   test
   │─ api_test.dart
   └─ test_utils.dart
```

다음과 같이 라이브러리들을 import 해야합니다:

**lib/api.dart:**

{:.good}
```dart
import 'src/stuff.dart';
import 'src/utils.dart';
```

**lib/src/utils.dart:**

{:.good}
```dart
import '../api.dart';
import 'stuff.dart';
```

**test/api_test.dart:**

{:.good}
```dart
import 'package:my_package/api.dart'; // 'lib'에 도달하지 못함.

import 'test_utils.dart'; // 'test'라는 같은 폴더에 존재.
```


## Null


### 변수를 명시적으로 `null`로 초기화하지 마십시오.

{% include linter-rule-mention.md rule="avoid_init_to_null" %}

변수가 non-nullable 타입을 초기화하기 전에 사용한다면, Dart는 컴파일 에러를 리포트합니다.
변수가 nullable이라면, 암묵적으로 `null`로 초기화됩니다.
Dart에는 "초기화되지 않은 메모리"라는 개념이 없고
명시적으로 `null`로 초기화하지 않아도 됩니다.

{:.good}
<?code-excerpt "usage_good.dart (no-null-init)"?>
```dart
Item? bestDeal(List<Item> cart) {
  Item? bestItem;

  for (final item in cart) {
    if (bestItem == null || item.price < bestItem.price) {
      bestItem = item;
    }
  }

  return bestItem;
}
```

{:.bad}
<?code-excerpt "usage_bad.dart (no-null-init)" replace="/ = null/[!$&!]/g"?>
```dart
Item? bestDeal(List<Item> cart) {
  Item? bestItem[! = null!];

  for (final item in cart) {
    if (bestItem == null || item.price < bestItem.price) {
      bestItem = item;
    }
  }

  return bestItem;
}
```


### 디폴트 값으로 `null`을 명시적으로 사용하지 마십시오.

{% include linter-rule-mention.md rule="avoid_init_to_null" %}

디폴트 값이 없는 선택적인 nullable 매개변수를 사용하면,
Dart는 해당 변수의 디폴트 값을 암묵적으로 `null`로 사용하기 때문에
명시할 필요가 없습니다.

{:.good}
<?code-excerpt "usage_good.dart (default-value-null)"?>
```dart
void error([String? message]) {
  stderr.write(message ?? '\n');
}
```

{:.bad}
<?code-excerpt "usage_bad.dart (default-value-null)"?>
```dart
void error([String? message = null]) {
  stderr.write(message ?? '\n');
}
```

<a id="prefer-using--to-convert-null-to-a-boolean-value"></a>

### 항등 연산자에 `true` 또는 `false`을 사용하지 마십시오.

Boolean 리터럴에 대해 *non-nullable*인 boolean 표현식을 평가 할 때,
항등 연산자를 쓰는 것은 불필요합니다.
항등 연산자를 없애는 것이 항상 더 간단하며,
필요하다면 단항 부정 연산자인 `!`를 사용하세요:

{:.good}
<?code-excerpt "usage_good.dart (non-null-boolean-expression)"?>
```dart
if (nonNullableBool) { ... }

if (!nonNullableBool) { ... }
```

{:.bad}
<?code-excerpt "usage_bad.dart (non-null-boolean-expression)"?>
```dart
if (nonNullableBool == true) { ... }

if (nonNullableBool == false) { ... }
```

*Nullable*인 boolean 표현식을 평가하고 싶다면,
`??`을 사용하거나 명시적으로 `!= null` 확인을 해야합니다.

{:.good}
<?code-excerpt "usage_good.dart (nullable-boolean-expression)"?>
```dart
// 값이 null 일 때, 표현식이 false로 평가되도록 하고 싶다면:
if (nullableBool ?? false) { ... }

// 값이 null 일 때, 표현식이 false로 평가되고
// 변수의 타입 프로모션을 원한다면:
if (nullableBool != null && nullableBool) { ... }
```

{:.bad}
<?code-excerpt "usage_bad.dart (nullable-boolean-expression)"?>
```dart
// 값이 null이라면 정적 에러가 발생합니다:
if (nullableBool) { ... }

// 값이 null 일 때, false로 평가되도록 하고 싶다면:
if (nullableBool == true) { ... }
```

`nullableBool == true`은 실행 가능한 표현식이지만,
다음과 같은 이유로 사용해서는 안 됩니다:

* 코드가 `null`과 관련이 있음을 나타내지 않습니다.

* 이것은 명백하게 `null`과 관련이 있는 것이 아니기 때문에,
  항등 연산자가 중복되어 제거될 수 있는
  non-nullable의 경우로 쉽게 오인될 수 있습니다.
  왼쪽의 boolean 표현식이 null을 생성할 가능성이 없을 때만 true이고,
  null을 생성할 수 있을 때는 그렇지 않습니다.

* 이 표현식의 boolean 로직은 혼란을 야기합니다.
  `nullableBool`이 null이라면, `nullableBoll == true`는
  조건식이 `false`로 평가된다는 것을 의미합니다.

`??` 연산자로 null이 발생할 수도 있는 상황에서 더 명확한 표현이 가능하고,
중복된 연산자로 오인되지도 않습니다.
로직 또한 더 명확합니다;
표현식이 `null`이 될 때의 결과는 boolean 리터럴과 동일합니다.

`??`와 같은 null-aware 연산자를 조건식에 사용하는 것은
변수를 non-nullable 타입으로 변환하지 않습니다.
`if`문의 바디에서 변수의 타입을 변환하고 싶다면,
`??` 보다 명시적으로 `!= null` 확인을 하는 것이 더 바람직합니다.

### 초기화 여부를 확인해야하는 변수를 `late`로 선언하는 것을 피하십시오.

Dart는 `late` 변수가 초기화 또는 할당되었는지 알 수 있는 방법을
가지고 있지 않습니다. `late` 변수에 접근 했을 때,
initializer를 실행하거나, 예외를 발생시킵니다.
바로 초기화될 필요가 없는 상태는 `late`와 잘 어울립니다.
하지만 아직 초기화가 이루어지지 않았다면, 그 사실을 *알고 있어야* 합니다.

`late` 변수에 상태를 저장하고 변수의 설정을 추적하는 분리된 boolean 필드를 사용함으로써
변수의 초기화를 감지할 수 있지만, Dart는 `late` 변수의 초기화 상태를 *내부적으로* 유지하기 때문에
굳이 그럴 필요가 없습니다. non-`late` 또는 nullable 변수를 대신 사용하는 것이 더 명확할 수 있습니다.
그렇게 하면 변수가 `null`인지 확인함으로써 초기화 상태를 확인할 수 있습니다.

물론 `null` 값이 변수의 유효한 초기화 값이라면,
분리된 boolean 필드를 가지는 것이 타당할 것입니다.


### 타입 프로모션을 활성화하고 싶다면 지역 변수에 nullable 필드를 할당하는 것을 고려하십시오.

Nullable 변수가 `null`과 같지 않는 것을 확인하는 것은 변수를 non-nullable 타입으로 변환시킵니다.
이를 통해 변수의 멤버에 접근하고 non-nullable 타입을 전달받는 함수에 사용이 가능합니다.

안타깝게도 타입 프로모션은 지역 변수와 매개변수에 대해서만 유효하므로 필드와 최상위 변수는
[타입 변환 되지 않습니다][can't be type promoted].

일반적으로 권장하는 것처럼 멤버를 [private][]과 [final][]로 선언하는 것은
이러한 제한을 우회하기에 충분합니다. 그러나 이것이 항상 최적의 옵션은 아닙니다.
이 문제를 해결하는 한 가지 패턴은 필드의 값을 지역 변수에 할당하는 것입니다.
해당 변수에 대한 null 체킹은 형변환을 수행함으로, 안전하게 non-nullable 처럼 처리하는 것이 가능합니다.

{:.good}
<?code-excerpt "usage_good.dart (shadow-nullable-field)"?>
```dart
class UploadException {
  final Response? response;

  UploadException([this.response]);

  @override
  String toString() {
    final response = this.response;
    if (response != null) {
      return 'Could not complete upload to ${response.url} '
          '(error code ${response.errorCode}): ${response.reason}.';
    }

    return 'Could not upload (no response).';
  }
}
```

지역 변수에 할당하는 것은 필드 또는 최상위 변수가 사용되는 모든 위치에서
`!`를 사용하는 것보다 깔끔하고 안전할 수 있습니다:

{:.bad}
<?code-excerpt "usage_bad.dart (shadow-nullable-field)" replace="/!\./[!!!]./g"?>
```dart
class UploadException {
  final Response? response;

  UploadException([this.response]);

  @override
  String toString() {
    if (response != null) {
      return 'Could not complete upload to ${response[!!!].url} '
          '(error code ${response[!!!].errorCode}): ${response[!!!].reason}.';
    }

    return 'Could not upload (no response).';
  }
}
```

지역 변수를 사용할 때는 주의해야 합니다. 필드의 값을 갱신해야 할 때,
지역 변수를 대신해서 갱신하지 않도록 주의하세요 (지역 변수를 [`final`][]로 설정하는 것은 이런 실수를 예방합니다).
또한, 지역 변수가 스코프 안에 있을 때 필드가 변경될 수 있는 경우 지역 변수에는 오래된 값이 있을 수 있습니다.
때때로 필드에 [`!`][use `!`]를 사용하는 것이 가장 좋을 때가 있습니다.

[can't be type promoted]: /tools/non-promotion-reasons
[private]: /effective-dart/design#prefer-making-declarations-private
[final]: /effective-dart/design#prefer-making-fields-and-top-level-variables-final
[`final`]: /effective-dart/usage#do-follow-a-consistent-rule-for-var-and-final-on-local-variables
[use `!`]: /null-safety/understanding-null-safety#null-assertion-operator

## 문자열

다음은 Dart에서 문자열을 다룰 때 알아두어야 할 몇 가지 모범 사례입니다.

### 인접 문자열을 사용하여 문자열 리터럴을 연결하십시오.

{% include linter-rule-mention.md rule="prefer_adjacent_string_concatenation" %}

두 개의 문자열 리터럴 (값이 아니라 실제 따옴표로 묶은 리터럴 형식)을 연결하는 경우에 `+`를 사용할
필요가 없습니다. C 그리고 C++ 처럼 두 문자열을 나란히 배치시키면 됩니다.
이것은 한 줄을 넘어가는 긴 단일 문자열을 만드는 좋은 방법입니다.

{:.good}
<?code-excerpt "usage_good.dart (adjacent-strings-literals)"?>
```dart
raiseAlarm('ERROR: Parts of the spaceship are on fire. Other '
    'parts are overrun by martians. Unclear which are which.');
```

{:.bad}
<?code-excerpt "usage_bad.dart (adjacent-strings-literals)"?>
```dart
raiseAlarm('ERROR: Parts of the spaceship are on fire. Other ' +
    'parts are overrun by martians. Unclear which are which.');
```

### 문자열과 값들을 합성하고 싶다면, 보간(interporation)의 사용을 지향하십시오.

{% include linter-rule-mention.md rule="prefer_interpolation_to_compose_strings" %}

이전에 다른 언어를 사용해 봤다면, 문자열을 리터럴과 다른 값들로 구성할 때 `+`로 이루어진
긴 체인을 만들어봤을 것입니다. Dart에서도 가능하지만, 보간을 사용하는 것이 거의 항상
더 깔끔하며 간결합니다:

{:.good}
<?code-excerpt "usage_good.dart (string-interpolation)"?>
```dart
'Hello, $name! You are ${year - birth} years old.';
```

{:.bad}
<?code-excerpt "usage_bad.dart (string-interpolation)"?>
```dart
'Hello, ' + name + '! You are ' + (year - birth).toString() + ' y...';
```

이 가이드라인은 *다수*의 리터럴과 값의 결합에 보간을 적용한 것을 알아두세요.
오직 하나의 객체를 문자열로 바꿀 때 `.toString()`를 사용해도 됩니다.

### 보간에 불필요한 중괄호 사용을 피하십시오.

{% include linter-rule-mention.md rule="unnecessary_brace_in_string_interps" %}

더 많은 영숫자 텍스트가 바로 뒤에 오지 않는 단순 식별자를 보간하는 경우에는
`{}`를 생략해야 합니다.

{:.good}
<?code-excerpt "usage_good.dart (string-interpolation-avoid-curly)"?>
```dart
var greeting = 'Hi, $name! I love your ${decade}s costume.';
```

{:.bad}
<?code-excerpt "usage_bad.dart (string-interpolation-avoid-curly)"?>
```dart
var greeting = 'Hi, ${name}! I love your ${decade}s costume.';
```

## 컬렉션

별도의 설치나 구성없이 Dart에서 바로 사용할 수 있는 컬렉션 타입은 list, map, queue, set이 존재합니다.
다음은 컬렉션에 적용할 수 있는 모범 사례입니다.

### 가능하다면 컬렉션 리터럴을 사용하십시오.

{% include linter-rule-mention.md rule="prefer_collection_literals" %}

Dart는 세 개의 핵심 컬렉션 타입을 가지고 있습니다: List, Map 그리고 Set.
Map과 Set 클래스는 대부분의 클래스처럼 unamed 생성자를 가지고 있습니다.
하지만 이런 컬렉션들은 자주 사용되므로, Dart는 컬렉션 생성을 위한 더 훌륭한 내장 문법을 가지고 있습니다:

{:.good}
<?code-excerpt "usage_good.dart (collection-literals)"?>
```dart
var points = <Point>[];
var addresses = <String, Address>{};
var counts = <int>{};
```

{:.bad}
<?code-excerpt "usage_bad.dart (collection-literals)"?>
```dart
var addresses = Map<String, Address>();
var counts = Set<int>();
```

이 가이드라인은 해당 클래스들의 *named* 생성자에 적용하지 않았다는 것을 알아두세요.
`List.from()`, `Map.fromIterable()` 그리고 이와 비슷한 생성자들은 저마다
사용법이 존재합니다. (List 클래스 또한 unnamed 생성자가 존재하지만, null safe Dart에서는
사용이 금지되었습니다)

컬렉션 리터럴은 다른 컬렉션의 요소들을 추가할 때 [전개 연산자][spread]에
접근이 가능하고 요소를 빌드 하는 동안 [`if` 그리고 `for`]를 사용하여
흐름 제어를 수행할 수 있기 때문에 Dart에서 특히 유용합니다:

[spread]: /language/collections#spread-operators
[control]: /language/collections#control-flow-operators

{:.good}
<?code-excerpt "usage_good.dart (spread-etc)"?>
```dart
var arguments = [
  ...options,
  command,
  ...?modeFlags,
  for (var path in filePaths)
    if (path.endsWith('.dart')) path.replaceAll('.dart', '.js')
];
```

{:.bad}
<?code-excerpt "usage_bad.dart (spread-etc)"?>
```dart
var arguments = <String>[];
arguments.addAll(options);
arguments.add(command);
if (modeFlags != null) arguments.addAll(modeFlags);
arguments.addAll(filePaths
    .where((path) => path.endsWith('.dart'))
    .map((path) => path.replaceAll('.dart', '.js')));
```


### 컬렉션이 비어있는지 확인할 때 `.length`를 사용하지 마십시오.

{% include linter-rule-mention.md rule1="prefer_is_empty" rule2="prefer_is_not_empty" %}

[Iterable][]을 사용한 컬렉션의 구현은 자신의 길이를 알고 있거나
상수 시간 안에 제공하도록 되어있지 않습니다. *단지* 컬렉션에 포함된 것이 있는지
확인하기 위해 `.length`를 호출하는 것은 매우 느릴 수 있습니다.

[iterable]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Iterable-class.html

대신, 더 빠르고 가독성이 높은 getter인 `.isEmpty`와 `.isNotEmpty`를 사용하세요.
둘 중에 결과를 부정할 필요가 없는 getter를 사용하세요.

{:.good}
<?code-excerpt "usage_good.dart (dont-use-length)"?>
```dart
if (lunchBox.isEmpty) return 'so hungry...';
if (words.isNotEmpty) return words.join(' ');
```

{:.bad}
<?code-excerpt "usage_bad.dart (dont-use-length)"?>
```dart
if (lunchBox.length == 0) return 'so hungry...';
if (!words.isEmpty) return words.join(' ');
```


### `Iterable.forEach()`를 함수 리터럴과 함께 사용하는 것을 피하십시오.

{% include linter-rule-mention.md rule="avoid_function_literals_in_foreach_calls" %}

JavaScript에서 내장된 `for-in` 루프로 원하는 효과를 얻지 못하기 때문에
`forEach()`를 보통 사용합니다. Dart에서는 시퀀스를 반복할 때
`for-in` 루프를 사용하는 것이 관용적인 방법입니다.

{:.good}
<?code-excerpt "usage_good.dart (avoid-forEach)"?>
```dart
for (final person in people) {
  ...
}
```

{:.bad}
<?code-excerpt "usage_bad.dart (avoid-forEach)"?>
```dart
people.forEach((person) {
  ...
});
```

이 가이드라인은 대상을 "함수 *리터럴*"로 특정 지어 설명하고 있습니다.
각 요소에 *이미 존재하는* 함수를 호출한다면, `forEach()`를 사용해도 됩니다.

{:.good}
<?code-excerpt "usage_good.dart (forEach-over-func)"?>
```dart
people.forEach(print);
```

`Map.forEach()`을 사용하는 것도 가능하다는 것을 알아두세요. Map은 iterable이 아니기에
이 가이드라인을 적용할 수 없습니다.

### 결과 값의 타입을 바꾸려는 것이 아니라면, `List.from()`을 사용하지 마십시오.

주어진 Iterable를 사용해서 동일한 요소를 가지는
새로운 List를 만드는 확실한 방법은 두 가지가 있습니다:

<?code-excerpt "../../test/effective_dart_test.dart (list-from-1)"?>
```dart
var copy1 = iterable.toList();
var copy2 = List.from(iterable);
```

둘의 명백한 차이는 첫 번째가 더 간결하다는 것입니다.
*중요한* 차이점은 첫 번째가 원래 객체의 타입 인자를 보존한다는 것입니다:

{:.good}
<?code-excerpt "../../test/effective_dart_test.dart (list-from-good)"?>
```dart
// Creates a List<int>:
var iterable = [1, 2, 3];

// Prints "List<int>":
print(iterable.toList().runtimeType);
```

{:.bad}
<?code-excerpt "../../test/effective_dart_test.dart (list-from-bad)"?>
```dart
// Creates a List<int>:
var iterable = [1, 2, 3];

// Prints "List<dynamic>":
print(List.from(iterable).runtimeType);
```

타입을 바꾸고 *싶다면*, `List.from()`을 호출하는 것이 좋습니다:

{:.good}
<?code-excerpt "../../test/effective_dart_test.dart (list-from-3)"?>
```dart
var numbers = [1, 2.3, 4]; // List<num>.
numbers.removeAt(1); // Now it only contains integers.
var ints = List<int>.from(numbers);
```

타입에 대해 신경쓰지 않거나, 단지 iterable를 복사하고 원래 타입을 보존하는 것이 목적이라면,
`toList()`를 사용하세요.


### 컬렉션을 타입으로 필터하고 싶다면, `whereType()`을 사용하십시오.

{% include linter-rule-mention.md rule="prefer_iterable_whereType" %}

여러가지 타입의 객체를 가지는 리스트에서 정수만 가져오고 싶다면,
`where()`을 다음과 같이 사용하세요:

{:.bad}
<?code-excerpt "usage_bad.dart (where-type)"?>
```dart
var objects = [1, 'a', 2, 'b', 3];
var ints = objects.where((e) => e is int);
```

이것은 장황할 뿐만 아니라, 원하지 않는 타입의 iterable을 반환할 수도 있습니다.
이 예제에서 우리는 `Iterable<int>`가 반환되길 기대하지만,
`Iterable<Object>`가 반환됩니다.

가끔씩 `cast()`를 추가해 해당 에러를 "고치려는" 코드를 볼 수 있습니다:

{:.bad}
<?code-excerpt "usage_bad.dart (where-type-2)"?>
```dart
var objects = [1, 'a', 2, 'b', 3];
var ints = objects.where((e) => e is int).cast<int>();
```

그런 코드는 장황하며 두 개의 간접 계층과
중복된 런타임 검사를 동반하며 두 개의 래퍼(wrapper)가 생성되도록 합니다.
다행히도 정확히 이런 상황을 위해 핵심 라이브러리는 [`whereType()`][where-type]
메서드를 가지고 있습니다:

[where-type]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Iterable/whereType.html

{:.good}
<?code-excerpt "../../test/effective_dart_test.dart (whereType)"?>
```dart
var objects = [1, 'a', 2, 'b', 3];
var ints = objects.whereType<int>();
```

`whereType()`은 간결하며 불필요한 래퍼의 계층이 없이 원하는 타입의 [Iterable][]
을 생성할 수 있습니다.


### 코드 주변에서 `cast()`와 같은 역할을 하는 연산이 있다면, `cast()`를 사용하지 마십시오.

Iterable 또는 stream을 다룰 때, 종종 변형을 수행하곤 합니다.
특정 타입 인자를 사용하여 객체를 생성할 때, `cast()`를 무작정 호출하기 보다
이미 존재하는 변형이 해당 타입으로의 변환을 수행하는지 먼저 확인하세요.

`toList()`를 호출한다면, 해당 호출을 원하는 객체의 타입 `T`를 지정하여
[`List<T>.from()`][list-from]로 대체하세요.

[list-from]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/List/List.from.html

{:.good}
<?code-excerpt "usage_good.dart (cast-list)"?>
```dart
var stuff = <dynamic>[1, 2];
var ints = List<int>.from(stuff);
```

{:.bad}
<?code-excerpt "usage_bad.dart (cast-list)"?>
```dart
var stuff = <dynamic>[1, 2];
var ints = stuff.toList().cast<int>();
```

`map()`을 호출한다면, 원하는 타입을 명시적으로 타입 인자로 넘겨주어
해당 타입의 iterable를 생성하세요. 타입 추론이 `map()`으로 넘겨준 함수를 기반으로
옳은 타입을 선택하지만, 종종 명시해야 할 필요가 있습니다.

{:.good}
<?code-excerpt "usage_good.dart (cast-map)" replace="/\(n as int\)/n/g"?>
```dart
var stuff = <dynamic>[1, 2];
var reciprocals = stuff.map<double>((n) => 1 / n);
```

{:.bad}
<?code-excerpt "usage_bad.dart (cast-map)" replace="/\(n as int\)/n/g"?>
```dart
var stuff = <dynamic>[1, 2];
var reciprocals = stuff.map((n) => 1 / n).cast<double>();
```


### `cast()` 사용을 피하십시오.

이 가이드라인은 이전 규칙에 대한 느슨한 정의입니다.
때때로 객체의 타입을 고정시킬만한 명령이 없을 수 있습니다.
그런 경우라도 가능하다면 컬렉션의 타입을 "변경"하기 위해 `cast()`를
사용하는 것은 피해야 합니다.

`cast()`의 대안으로 다음 옵션들을 고려해보세요:

*   **올바른 타입으로 생성하세요.** 컬렉션이 처음 생성되는 코드를 수정하여 올바른 타입을 가지게 하세요.

*   **요소에 접근할 때 캐스팅을 시도하세요.** 바로 해당 컬렉션에 반복을 수행한다면, 반복 내부에서
    각 요소를 캐스팅하세요.

*   **`List.from()`을 사용하여 캐스팅하도록 노력하세요.** 최종적으로 컬렉션의 거의 모든 요소에 접근을 끝냈고
    원본 객체가 더 이상 필요하지 않다면, `List.from()`을 사용하여 타입을 변환하세요.

    `cast()` 메서드는 *모든 명령*에 대해 요소의 타입을 확인하는 지연(lazy) 컬렉션을 반환합니다.
    적은 요소에 대해 적은 명령을 수행한다면, 그로인해 발생하는 지연은 견딜 만할 것입니다. 하지만 대부분의 경우에
    래핑과 지연 검증의 오버헤드가 모든 이점을 능가해버립니다.


**올바른 타입으로 생성**하는 예제입니다:

{:.good}
<?code-excerpt "usage_good.dart (cast-at-create)"?>
```dart
List<int> singletonList(int value) {
  var list = <int>[];
  list.add(value);
  return list;
}
```

{:.bad}
<?code-excerpt "usage_bad.dart (cast-at-create)"?>
```dart
List<int> singletonList(int value) {
  var list = []; // List<dynamic>.
  list.add(value);
  return list.cast<int>();
}
```

다음은 **요소에 접근할 때 캐스팅하는 방법**입니다:

{:.good}
<?code-excerpt "usage_good.dart (cast-iterate)" replace="/\(n as int\)/[!$&!]/g"?>
```dart
void printEvens(List<Object> objects) {
  // List에 int만 존재한다는 것을 알고 있습니다.
  for (final n in objects) {
    if ([!(n as int)!].isEven) print(n);
  }
}
```

{:.bad}
<?code-excerpt "usage_bad.dart (cast-iterate)"?>
```dart
void printEvens(List<Object> objects) {
  // List에 int만 존재한다는 것을 알고 있습니다.
  for (final n in objects.cast<int>()) {
    if (n.isEven) print(n);
  }
}
```

다음은 **`List.from()`을 사용한 캐스팅**의 예제입니다:

{:.good}
<?code-excerpt "usage_good.dart (cast-from)"?>
```dart
int median(List<Object> objects) {
  // List에 int만 존재한다는 것을 알고 있습니다.
  var ints = List<int>.from(objects);
  ints.sort();
  return ints[ints.length ~/ 2];
}
```

{:.bad}
<?code-excerpt "usage_bad.dart (cast-from)"?>
```dart
int median(List<Object> objects) {
  // List에 int만 존재한다는 것을 알고 있습니다.
  var ints = objects.cast<int>();
  ints.sort();
  return ints[ints.length ~/ 2];
}
```

물론 이런 대안들이 항상 문제를 하결하는 것은 아니며, 이럴 때는 `cast()` 방식을 택해야
합니다. 그러나 이 방법의 위험과 단점을 고려할 때, 잘못 사용하면 실행이 느려지고 실패할 수도 있습니다.


## 함수

Dart에서 함수는 객체입니다. 다음은 함수 사용에 대한
모범 사례입니다.


### 함수의 이름과 함수를 바인드 하려면 함수 선언식을 사용하십시오.

{% include linter-rule-mention.md rule="prefer_function_declarations_over_variables" %}

현대 언어들은 지역 중첩 함수와 클로저가 얼마나 유용한지 깨달았습니다.
함수 안에 함수가 선언되는 것은 흔한 일입니다.
흔히 이런 함수들은 콜백으로 사용되고 이름이 없습니다.
함수 표현식은 이런 경우에 적합합니다.

하지만, 함수에 이름을 부여하고 싶다면 변수에 람다를 바인딩 하지 말고
함수 선언문을 사용하세요.

{:.good}
<?code-excerpt "usage_good.dart (func-decl)"?>
```dart
void main() {
  void localFunction() {
    ...
  }
}
```

{:.bad}
<?code-excerpt "usage_bad.dart (func-decl)"?>
```dart
void main() {
  var localFunction = () {
    ...
  };
}
```

### Tear-off 대신 람다를 사용하지 마십시오.

{% include linter-rule-mention.md rule="unnecessary_lambdas" %}

함수, 메서드 또는 named 생성자를 참조할 때 괄호를 생략하면, Dart는
동일한 매개 변수 쌍을 사용하고 호출 시 기본 함수를 호출하는 _tear-off_ 클로저를 생성합니다.

클로저가 받아들이는 매개변수와 동일한 매개변수를 사용하는 named 함수를 호출하는 클로저를
원한다면, 해당 호출을 람다로 래핑하지 마세요.

{:.good}
<?code-excerpt "usage_good.dart (use-tear-off)"?>
```dart
var charCodes = [68, 97, 114, 116];
var buffer = StringBuffer();

// 함수:
charCodes.forEach(print);

// 메서드:
charCodes.forEach(buffer.write);

// Named 생성자:
var strings = charCodes.map(String.fromCharCode);

// Unnamed 생성자:
var buffers = charCodes.map(StringBuffer.new);
```

{:.bad}
<?code-excerpt "usage_bad.dart (use-tear-off)"?>
```dart
var charCodes = [68, 97, 114, 116];
var buffer = StringBuffer();

// 함수:
charCodes.forEach((code) {
  print(code);
});

// 메서드:
charCodes.forEach((code) {
  buffer.write(code);
});

// Named 생성자:
var strings = charCodes.map((code) => String.fromCharCode(code));

// Unnamed 생성자:
var buffers = charCodes.map((code) => StringBuffer(code));
```


## 변수

다음은 Dart의 변수를 사용하는 모범 사례에 대해 설명합니다.

### 지역 변수에 `var`과 `final`을 사용할 때 일관된 규칙을 따르십시오.

대부분의 지역 변수는 타입 어노테이션이 없어야 하며, `var` 또는 `final`만을
사용하여 선언해야합니다. `var`과 `final`을 사용할 때 널리 사용되는 두 가지 규칙이 있습니다:

*   재할당 될 필요가 없는 지역 변수는 `final`을 사용하고, 그렇지 않은 경우에는 `var`을 사용하세요.

*   재할당 될 필요가 없는 지역 변수까지 모두 `var`로 선언하세요. 지역 변수에 `final`을 사용하지 마세요.
    (물론 필드나 최상위 변수는 `final`로 선언하는 것을 권장합니다.)

두 가지 규칙 모두 일리가 있으며, *한 가지*를 선택하여 코드에 통일성을 유지하세요.
그렇게 함으로써 코드를 읽는 사람이 `var`를 확인했을 때, 이 변수가 함수 내에서
추후에 변경될 수 있는지의 여부를 알 수 있습니다.


### 계산이 가능한 값들을 저장하는 것을 피하십시오.

클래스를 디자인 할 때, 같은 상태를 공유하는 여러가지 계산의 결과 값을 생성자에서 모두 계산하고
저장하는 경우가 있습니다:

{:.bad}
<?code-excerpt "usage_bad.dart (calc-vs-store1)"?>
```dart
class Circle {
  double radius;
  double area;
  double circumference;

  Circle(double radius)
      : radius = radius,
        area = pi * radius * radius,
        circumference = pi * 2.0 * radius;
}
```

이 코드에는 두 가지 문제점이 있습니다. 첫 번째, 이런 작업은 메모리를 낭비합니다.
area와 circumference는 엄밀히 말하면 *캐시*입니다. 이 둘은 이미 가지고 있는
다른 데이터로부터 다시 계산할 수 있는 저장된 계산 결과입니다. 이것은 CPU 사용량을 줄이지만
메모리 사용량을 늘립니다. 이러한 트레이드오프에는 성능 문제가 있다는 것을 알고 있나요?

더군다나, 코드가 *잘못*되었습니다. 캐시의 문제는 *무효화*입니다. 캐시가 오래되어 다시
계산해야 할 때를 어떻게 알 수 있을까요? 위의 코드에서 `radius`가 변할 수 있는 변수라도
해당 작업을 수행하지 않습니다. `radius`에 다른 값을 할당할 수 있고 그렇게 하면
`area`와 `circumference`은 틀린 이전의 값을 가지게 됩니다.

캐시 무효화를 해결하려면, 다음과 같이 해야 합니다:

{:.bad}
<?code-excerpt "usage_bad.dart (calc-vs-store2)"?>
```dart
class Circle {
  double _radius;
  double get radius => _radius;
  set radius(double value) {
    _radius = value;
    _recalculate();
  }

  double _area = 0.0;
  double get area => _area;

  double _circumference = 0.0;
  double get circumference => _circumference;

  Circle(this._radius) {
    _recalculate();
  }

  void _recalculate() {
    _area = pi * _radius * _radius;
    _circumference = pi * 2.0 * _radius;
  }
}
```

위의 코드는 유지, 디버깅, 그리고 읽고 쓰기에 너무 많은 코드입니다.
대신, 초기의 구현을 다음과 같이 진행하세요:

{:.good}
<?code-excerpt "usage_good.dart (calc-vs-store)"?>
```dart
class Circle {
  double radius;

  Circle(this.radius);

  double get area => pi * radius * radius;
  double get circumference => pi * 2.0 * radius;
}
```

이 코드는 더 짧고 적은 메모리를 사용하며 에러 발생률이 낮습니다. 원을 나타내기위해 필요한
최소의 데이터를 저장합니다. 데이터의 소스가 하나뿐이므로 동기화 해야할 필드가 존재하지 않습니다.

경우에 따라 느린 계산 결과를 캐시해야 할 수도 있지만, 성능 문제가 있다는 것을 알고
신중하게 수행한 후 최적화를 설명하는 주석을 남겨야 합니다.


## 멤버

Dart의 객체는 함수 (메서드) 또는 데이터 (인스턴스 변수)를 멤버로 가질수 있습니다.
다음은 객체의 멤버에 적용할 수 있는 모범 사례입니다.

### 필드에 불필요한 getter와 setter를 생성하지 마십시오.

{% include linter-rule-mention.md rule="unnecessary_getters_setters" %}

Java와 C#에서는 구현이 해당 필드를 가리키는 경우에도 getter와 setter 메서드 (C#에서는 프로퍼티라고 함)
로 모든 필드를 숨기는 것이 일반적입니다. 이렇게 하면 이러한 멤버에 대해 아무리 많은 작업을 수행하더라도
직접 액세스할 필요가 없습니다. 이는 Java에서 getter 메서드를 호출하는 것이 필드에 직접 액세스하는 것과
다르기 때문입니다. C#에서 프로퍼티 액세스는 필드 액세스와 이진 호환(binary-compatible)되지 않습니다.

Dart는 이런 제한이 없습니다. 필드와 getter/setter는 완전히 구분할 수 없습니다.
클래스의 필드를 노출한 다음 해당 필드를 사용하는 코드에 영향을 주지 않고 getter와
setter로 래핑할 수 있습니다.

{:.good}
<?code-excerpt "usage_good.dart (dont-wrap-field)"?>
```dart
class Box {
  Object? contents;
}
```

{:.bad}
<?code-excerpt "usage_bad.dart (dont-wrap-field)"?>
```dart
class Box {
  Object? _contents;
  Object? get contents => _contents;
  set contents(Object? value) {
    _contents = value;
  }
}
```


### 읽기 전용인 프로퍼티를 생성 할 때, `final` 키워드 사용을 지향하십시오.

변수를 읽을 수만 있고 외부 코드로 수정할 수 없는 변수를
생성하는 쉬운 방법은 `final` 키워드를 사용하는 것입니다.

{:.good}
<?code-excerpt "usage_good.dart (final)"?>
```dart
class Box {
  final contents = [];
}
```

{:.bad}
<?code-excerpt "usage_bad.dart (final)"?>
```dart
class Box {
  Object? _contents;
  Object? get contents => _contents;
}
```

물론 내부에서 할당하고 외부에서 접근할 수 있는 필드를 구성해야 한다면 이 "private 필드,
public getter" 패턴이 필요할 수 있지만 꼭 필요한 경우가 아니라면 사용하지 마세요.


### 단순한 멤버를 선언 할 떄, `=>` 사용을 고려하십시오.

{% include linter-rule-mention.md rule="prefer_expression_function_bodies" %}

`=>`를 함수 표현식으로 사용하는 것 외에도, Dart에서는 멤버를 정의하는 데 사용할 수도 있습니다.
이 스타일은 계산을 수행하고 결과를 반환하는 단순한 멤버에게 적합합니다.

{:.good}
<?code-excerpt "usage_good.dart (use-arrow)"?>
```dart
double get area => (right - left) * (bottom - top);

String capitalize(String name) =>
    '${name[0].toUpperCase()}${name.substring(1)}';
```

코드를 *작성하는* 사람들은 `=>` 구문을 선호하는 것 같지만 쉽게 남용될 수 있고
*읽기* 쉽지 않은 코드가 완성될 수 있습니다. 선언이 두 줄 이상이거나 깊게 중첩된
표현식(계단식, 조건부 연산자가 보통 범인)을 포함하는 경우 누구도 이런 코드를
읽고 싶지 않습니다. 이렇게 코드를 작성하는 대신에 코드 블록과 명령문을 사용하세요.

{:.good}
<?code-excerpt "usage_good.dart (arrow-long)"?>
```dart
Treasure? openChest(Chest chest, Point where) {
  if (_opened.containsKey(chest)) return null;

  var treasure = Treasure(where);
  treasure.addAll(chest.contents);
  _opened[chest] = treasure;
  return treasure;
}
```

{:.bad}
<?code-excerpt "usage_bad.dart (arrow-long)"?>
```dart
Treasure? openChest(Chest chest, Point where) => _opened.containsKey(chest)
    ? null
    : _opened[chest] = (Treasure(where)..addAll(chest.contents));
```

값을 반환하지 않는 멤버에도 `=>`를 사용할 수 있습니다.
여기서 setter와 getter가 모두 상대적으로 단순할 때 `=>`를 사용하는 것이 관용적입니다.

{:.good}
<?code-excerpt "usage_good.dart (arrow-setter)"?>
```dart
num get x => center.x;
set x(num value) => center = Point(value, center.y);
```


### Named 생성자를 리디렉션하고 충돌을 피하는 경우를 제외하고 `this`를 사용하지 마십시오. {#dont-use-this-when-not-needed-to-avoid-shadowing}

{% include linter-rule-mention.md rule="unnecessary_this" %}

JavaScript는 객체의 멤버 변수를 참조하기 위해 `this.`를 사용해야하지만,
Dart, C++, Java, 및 C#에는 이러한 제한이 없습니다.

`this.`가 필요한 두 가지 상황이 있습니다. 그 중 하나는 접근할 지역 변수가
멤버 변수와 같은 이름을 가진 경우입니다:

{:.bad}
<?code-excerpt "usage_bad.dart (this-dot)"?>
```dart
class Box {
  Object? value;

  void clear() {
    this.update(null);
  }

  void update(Object? value) {
    this.value = value;
  }
}
```

{:.good}
<?code-excerpt "usage_good.dart (this-dot)"?>
```dart
class Box {
  Object? value;

  void clear() {
    update(null);
  }

  void update(Object? value) {
    this.value = value;
  }
}
```

다른 경우는 named 생성자로 리디렉션할 때입니다:

{:.bad}
<?code-excerpt "usage_bad.dart (this-dot-constructor)"?>
```dart
class ShadeOfGray {
  final int brightness;

  ShadeOfGray(int val) : brightness = val;

  ShadeOfGray.black() : this(0);

  // 이 라인은 분석하거나 컴파일할 수 없습니다!
  // ShadeOfGray.alsoBlack() : black();
}
```

{:.good}
<?code-excerpt "usage_good.dart (this-dot-constructor)"?>
```dart
class ShadeOfGray {
  final int brightness;

  ShadeOfGray(int val) : brightness = val;

  ShadeOfGray.black() : this(0);

  // 이제는 가능합니다!
  ShadeOfGray.alsoBlack() : this.black();
}
```

생성자 initializer list의 필드는 생성자 매개변수와 충돌할 수 없습니다:

{:.good}
<?code-excerpt "usage_good.dart (param-dont-shadow-field-ctr-init)"?>
```dart
class Box extends BaseBox {
  Object? value;

  Box(Object? value)
      : value = value,
        super(value);
}
```

이것이 놀라울 수 있지만, 원하는 대로 작동합니다.
다행스럽게도 initializing formal과 super initializer 덕분에 이런 코드는 상대적으로 드뭅니다.


### 가능하다면 필드의 선언과 함께 초기화를 진행하십시오.

필드가 생성자 매개변수에 의존하지 않는 경우 선언 시 초기화해야 합니다. 이렇게 하면
클래스에 생성자가 여러 개 있을 때 코드가 짧아지고 중복을 방지합니다.

{:.bad}
<?code-excerpt "usage_bad.dart (field-init-at-decl)"?>
```dart
class ProfileMark {
  final String name;
  final DateTime start;

  ProfileMark(this.name) : start = DateTime.now();
  ProfileMark.unnamed()
      : name = '',
        start = DateTime.now();
}
```

{:.good}
<?code-excerpt "usage_good.dart (field-init-at-decl)"?>
```dart
class ProfileMark {
  final String name;
  final DateTime start = DateTime.now();

  ProfileMark(this.name);
  ProfileMark.unnamed() : name = '';
}
```

예를 들어 메서드를 호출하거나 다른 필드에 접근하기 위해 `this`를 참조해야하는 경우에
몇몇 필드는 선언과 초기화가 동시에 진행될 수 없습니다. 그러나, 필드를 `late`로
표시하면, initializer는 `this`에 접근할 수 *있습니다*.

물론 필드가 생성자 매개변수에 의존하거나, 생성자마다
다르게 초기화되는 경우에는 이 가이드라인이 적용되지 않습니다.


## 생성자

다음은 클래스 생성자에 대한 모범 사례입니다.

### 가능하다면 initializing formal을 사용하십시오.

{% include linter-rule-mention.md rule="prefer_initializing_formals" %}

다음과 같이 필드는 생성자 매개변수로 즉시 초기화될 수 있습니다:

{:.bad}
<?code-excerpt "usage_bad.dart (field-init-as-param)"?>
```dart
class Point {
  double x, y;
  Point(double x, double y)
      : x = x,
        y = y;
}
```

위에서 필드를 초기화하려면 `x`를 _4_ 번 반복해서 사용해야합니다.
다음과 같이 사용하는 것이 좋습니다:

{:.good}
<?code-excerpt "usage_good.dart (field-init-as-param)"?>
```dart
class Point {
  double x, y;
  Point(this.x, this.y);
}
```

생성자 매개변수 앞의 이 `this.` 문법을 "initializing formal"이라고 합니다.
이 문법을 항상 이용할 수 있는 것은 아닙니다. 초기화하려는 필드의 이름과 named 매개변수의
이름이 매치되지 않을 때가 그러합니다. 하지만 initializing formal을 사용할 수 *있는* 상황이라면,
사용해야 합니다.


### 생성자 initializer list가 변수의 값을 초기화해준다면, `late`를 사용하지 마십시오.

Sound null safety을 위해 Dart는 non-nullabel 필드를 읽기 전에 초기화해야 합니다.
필드는 생성자 바디 안에서 읽을 수 있기 때문에, 생성자의 바디가 실행되기 전에
non-nullable 필드를 초기화하지 않으면 에러가 발생합니다. 

필드를 `late`로 표시하면 이런 에러를 해결할 수 있습니다. `late`는 초기화되지 않은
필드에 접근하면 해당 에러를 컴파일 타임 에러에서 *런타임* 에러로 전환합니다. `late`가
필요한 경우도 있지만 생성자 initializer list로 필드를 초기화하는 것이 올바른 해결책입니다:

{:.good}
<?code-excerpt "usage_good.dart (late-init-list)"?>
```dart
class Point {
  double x, y;
  Point.polar(double theta, double radius)
      : x = cos(theta) * radius,
        y = sin(theta) * radius;
}
```

{:.bad}
<?code-excerpt "usage_bad.dart (late-init-list)"?>
```dart
class Point {
  late double x, y;
  Point.polar(double theta, double radius) {
    x = cos(theta) * radius;
    y = sin(theta) * radius;
  }
}
```


Initializer list는 생성자 매개변수에 접근할 수 있고 필드를 읽기 전에 초기화할 수 있습니다.
그러므로 initializer list를 사용하는 것이 가능하다면, 정적인 안정성과 성능을 감소시키는
`late` 대신 사용하세요.


### 비어있는 생성자 바디에 `{}` 대신에 `;`을 사용하십시오.

{% include linter-rule-mention.md rule="empty_constructor_bodies" %}

Dart에서 바디가 비어있는 생성자는 세미콜론으로 끝낼 수 있습니다.
(사실 const 생성자가 필요합니다.)

{:.good}
<?code-excerpt "usage_good.dart (semicolon-for-empty-body)"?>
```dart
class Point {
  double x, y;
  Point(this.x, this.y);
}
```

{:.bad}
<?code-excerpt "usage_bad.dart (semicolon-for-empty-body)"?>
```dart
class Point {
  double x, y;
  Point(this.x, this.y) {}
}
```

### `new`를 사용하지 마십시오.

{% include linter-rule-mention.md rule="unnecessary_new" %}

생성자를 호출할 때 `new` 키워드 사항은 선택사항입니다.
Factory 생성자에서 `new`를 호출한다고 해서 반드시 새 객체가 반환된다는 의미가 아닐 수 있기 때문입니다.

Dart는 여전히 `new` 사용을 허용하고 있지만, 되도록이면 사용하지 마세요.

{:.good}
<?code-excerpt "usage_good.dart (no-new)"?>
```dart
Widget build(BuildContext context) {
  return Row(
    children: [
      RaisedButton(
        child: Text('Increment'),
      ),
      Text('Click!'),
    ],
  );
}
```

{:.bad}
<?code-excerpt "usage_bad.dart (no-new)" replace="/new/[!$&!]/g"?>
```dart
Widget build(BuildContext context) {
  return [!new!] Row(
    children: [
      [!new!] RaisedButton(
        child: [!new!] Text('Increment'),
      ),
      [!new!] Text('Click!'),
    ],
  );
}
```


### `const`를 불필요하게 사용하지 마십시오.

{% include linter-rule-mention.md rule="unnecessary_const" %}

표현식이 *상수여야* 하는 컨텍스트에서 `const` 키워드는 암묵적이므로,
작성할 필요가 없으며 그렇게 해서도 안 됩니다. 컨텍스트란 다음을 의미합니다.
In contexts where an expression *must* be constant, the `const` keyword is
implicit, doesn't need to be written, and shouldn't. Those contexts are any
expression inside:

* 상수 컬렉션 리터럴
* 상수 생성자 호출
* 메타데이터 어노테이션
* 상수 변수 선언을 위한 initializer
* switch case 표현식 - `case`의 바디가 아니라 `case`와 `:` 사이 부분 입니다.

(상수가 아닌 디폴트 값은 Dart의 향후 버전에서 지원될 수 있으므로 디폴트 값은 이 목록에 포함하지 않습니다.)

기본적으로 `const` 대신 `new`를 쓰는 것이 오류가 되는 곳이라면 어느 곳이든 `const`를 생략할 수 있다.

{:.good}
<?code-excerpt "usage_good.dart (no-const)"?>
```dart
const primaryColors = [
  Color('red', [255, 0, 0]),
  Color('green', [0, 255, 0]),
  Color('blue', [0, 0, 255]),
];
```

{:.bad}
<?code-excerpt "usage_bad.dart (no-const)" replace="/ (const)/ [!$1!]/g"?>
```dart
const primaryColors = [!const!] [
  [!const!] Color('red', [!const!] [255, 0, 0]),
  [!const!] Color('green', [!const!] [0, 255, 0]),
  [!const!] Color('blue', [!const!] [0, 0, 255]),
];
```

## 에러 핸들링

Dart는 예외를 사용하여 프로그램 실행 오류를 나타냅니다.
다음은 예외를 catch하고 throw하는 방법에 대한 모범 사례입니다:

### `on` 없이 에러를 캐치하는 것을 피하십시오.

{% include linter-rule-mention.md rule="avoid_catches_without_on_clauses" %}

`on`이 없는 catch 문은 try 블록 내에서 발생하는 *모든* 예외를 catch합니다.
[포켓몬 예외 처리][pokemon]가 원하는 것이 아닐 가능성이 높습니다.
당신이 작성한 코드가 [StackOverflowError][] 또는
[OutOfMemoryError][]를 적절하게 처리하고 있나요? 잘못된 인수로 try 블록의 함수를 호출하는 경우
디버거가 실수를 알려주길 원하나요? 아니면 도움이 되는 [ArgumentError][]가 삼켜지길 원하나요?
[AssertionError][] 예외를 catch했으므로 try 블록의 모든 `assert()`문이 유효하지 않습니다.
이것이 우리가 원하는 결과일까요?

아마도 아닐 것이며, 이 경우에 캐치한 타입을 필터링해야 합니다.
대부분의 경우에, `on` 절이 있어야 런타임에 사용자가 인지하고 처리하길 원하는 예외를
포착하고 적절히 처리할 수 있습니다.

드물게 모든 런타임 에러를 캐치하고 싶을 수도 있습니다. 이는 일반적으로 임의의 애플리케이션 코드가
문제를 일으키지 않도록 차단하는 프레임워크나 낮은 수준의 코드의 경우입니다.
이런 경우에도 모든 타입을 캐치하는 것보다 [Exception][]을 캐치하는 것이 더 좋습니다.
Exception은 모든 *런티임* 에러의 베이스 클래스이며 코드의 *프로그래밍* 버그를 나타내는
에러는 포함하지 않습니다.


### `on` 문으로 캐치되지 않은 에러를 버리지 마십시오.

코드 내에서 *모든* 예외를 캐치하고 싶다면 예외가 캐치된 곳에서 *조치를 취하세요*.
이를 기록하여 사용자에게 표시하거나 예외 정보를 rethrow하여 버리지 않도록 주의하세요.


### 프로그래밍 오류를 나타내는 경우에만 `Error`를 구현하는 객체를 throw 하십시오.

[Error][] 클래스는 모든 *프로그래밍* 에러의 베이스 클래스입니다. 이 타입의 객체나 [ArgumentError][]
같은 서브 인터페이스가 발생되면, 코드에 *버그*가 있다는 것을 의미합니다. API가 호출자에게
사용법이 잘못되었음을 알리고자 하는 경우 오류를 발생시켜 의도를 나타낼 수 있습니다.

마찬가지로 예외가 코드 버그가 아니라 런타임 예외를 나타내는 경우 Error를 throw하는 것은
호출자를 오도할 수 있습니다. 그런 경우에는 Exception 클래스 또는 코어에 의해 정의된 다른
타입을 throw해야 합니다.


### `Error` 또는 이를 구현하는 타입을 명시적으로 캐치하지 마십시오.

{% include linter-rule-mention.md rule="avoid_catching_errors" %}

위의 내용과 이어지는 가이드라인입니다. 에러는 코드의 버그를 나타내므로 전체 호출 스택을
펼치며 프로그램을 중지하고 스택 트레이스를 출력하여 버그를 찾아 수정할 수 있습니다.

이러한 에러를 캐치하면 처리 흐름이 중단되고 해당 버그를 마스크(mask)합니다.
여기에서 에러 처리 코드를 *추가*하지 말고, 에러가 발생한 곳으로 이동하여 코드를 수정하세요.


### 캐치된 예외를 rethrow 하고 싶다면, `rethrow`를 사용하십시오.

{% include linter-rule-mention.md rule="use_rethrow_when_possible" %}

예외를 rethrow하고 싶다면, `throw`하는 것보다 `rethrow` 문을 사용하는 것이 좋습니다.
`rethrow`는 원래 예외의 스택 트레이스를 유지합니다. 반면에 `throw`는 스택 트레이스를 마지막으로
throw된 위치로 재설정합니다.

{:.bad}
<?code-excerpt "usage_bad.dart (rethrow)"?>
```dart
try {
  somethingRisky();
} catch (e) {
  if (!canHandle(e)) throw e;
  handle(e);
}
```

{:.good}
<?code-excerpt "usage_good.dart (rethrow)" replace="/rethrow/[!$&!]/g"?>
```dart
try {
  somethingRisky();
} catch (e) {
  if (!canHandle(e)) [!rethrow!];
  handle(e);
}
```


## 비동기

Dart에는 비동기 프로그래밍을 지원하는 여러가지 언어 기능이 있습니다.
다음은 비동기 코딩에 대한 모범 사례입니다.

### Future을 async/await와 같이 사용하는 것을 지향하십시오.

비통기 코드는 future와 같은 훌륭한 추상화를 사용하더라도 읽고 디버그하기 까다롭습니다.
`async`/`await` 문법은 가독성을 향상시키고 비동기 코드에서 Dart의 모든 흐름 제어문을
사용할 수 있습니다.

{:.good}
<?code-excerpt "usage_good.dart (async-await)" replace="/async|await/[!$&!]/g"?>
```dart
Future<int> countActivePlayers(String teamName) [!async!] {
  try {
    var team = [!await!] downloadTeam(teamName);
    if (team == null) return 0;

    var players = [!await!] team.roster;
    return players.where((player) => player.isActive).length;
  } catch (e) {
    log.error(e);
    return 0;
  }
}
```

{:.bad}
<?code-excerpt "usage_bad.dart (async-await)"?>
```dart
Future<int> countActivePlayers(String teamName) {
  return downloadTeam(teamName).then((team) {
    if (team == null) return Future.value(0);

    return team.roster.then((players) {
      return players.where((player) => player.isActive).length;
    });
  }).catchError((e) {
    log.error(e);
    return 0;
  });
}
```

### 유의미한 영향이 없다면 `async`를 사용하지 마십시오.

습관이 되면 아마 모든 비동기 관련 함수에서 `async`를 사용하게 될 것입니다.
그러나 메서드의 동작을 변경하지 않고 `async`를 생략할 수 있다면 그래야합니다.

{:.good}
<?code-excerpt "usage_good.dart (unnecessary-async)"?>
```dart
Future<int> fastestBranch(Future<int> left, Future<int> right) {
  return Future.any([left, right]);
}
```

{:.bad}
<?code-excerpt "usage_bad.dart (unnecessary-async)"?>
```dart
Future<int> fastestBranch(Future<int> left, Future<int> right) async {
  return Future.any([left, right]);
}
```

다음은 `async`가 *유용한* 몇 가지 케이스입니다:
Cases where `async` *is* useful include:

* `await`를 사용할 때 (명백합니다.)

* 에러를 비동기적으로 반환할 때. `async`와 `throw`를 함게 사용하는 것이
  `return Future.error(...)` 보다 간결합니다.

* 값을 반환하고 있지만 명시적으로 Future 사용을 원할 때.
  `async`는 `Future.value(...)` 보다 간결합니다.

{:.good}
<?code-excerpt "usage_good.dart (async)"?>
```dart
Future<void> usesAwait(Future<String> later) async {
  print(await later);
}

Future<void> asyncError() async {
  throw 'Error!';
}

Future<String> asyncValue() async => 'value';
```

### 고차 메서드를 사용하여 스트림을 변환하는 것을 고려하십시오.

이 가이드라인은 iterable에 대한 위의 제안과 비슷합니다. Stream은 많은 동일한 메서드를
지원하며 에러 전송, 닫기 등을 올바르게 처리합니다.

### Completer를 직접적으로 사용하는 것을 피하십시오.

비동기 프로그래밍이 처음인 사람들은 future를 생성하는 코드를 작성하고 싶어합니다.
Future 생성자는 그들의 요구를 충족하지 않고 결국에 Completer 클래스를 사용하게 됩니다.

{:.bad}
<?code-excerpt "usage_bad.dart (avoid-completer)"?>
```dart
Future<bool> fileContainsBear(String path) {
  var completer = Completer<bool>();

  File(path).readAsString().then((contents) {
    completer.complete(contents.contains('bear'));
  });

  return completer.future;
}
```

새로운 비동기 원시(primitive) 코드와 future를 사용하지 않는 비동기 코드와의
인터페이싱(interfacing), 이 두 가지 종류의 낮은 수준의 코드에서 Completer가 필요합니다.
다른 코드들은 더 간결하고 에러 처리가 쉬운 async/await 또는
[`Future.then()`][then]을 사용해야 합니다.

[then]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-async/Future/then.html

{:.good}
<?code-excerpt "usage_good.dart (avoid-completer)"?>
```dart
Future<bool> fileContainsBear(String path) {
  return File(path).readAsString().then((contents) {
    return contents.contains('bear');
  });
}
```

{:.good}
<?code-excerpt "usage_good.dart (avoid-completer-alt)"?>
```dart
Future<bool> fileContainsBear(String path) async {
  var contents = await File(path).readAsString();
  return contents.contains('bear');
}
```


### `FutureOr<T>`의 매개변수 `T`가 `Object` 타입일 수 있음을 명확히 하기 위해 `Future<T>`를 테스트 하십시오.

`FutureOr<T>`로 유용한 작업을 수행하기 전에 일반적으로 `Future<T>` 또는 빈 `T`가 있는지
확인해야 합니다. 타입 매개변수가 특정 타입 (예: `FutureOr<int>`)인 경우 `is int` 또는
`is Future<int>`와 같은 테스트가 작동합니다. 두 타입은 분리되어 있기 때문에 둘 다 작동합니다.

그러나, 값의 타입이 `Object`이거나 `Object`로 인스턴스화할 수 있는 타입 매개변수인 경우는 서로 겹칩니다.
`Future<Object>`는 `Object`를 구현합니다. 그렇기 때문에 `Object`로 인스턴스화 될 수 있는
`T`를 가지는 `is T`와 `is Object`는 객체가 future일 때도 true를 반환합니다.
대신에 `Future`의 경우를 위해 명시적으로 테스트하세요:

{:.good}
<?code-excerpt "usage_good.dart (test-future-or)"?>
```dart
Future<T> logValue<T>(FutureOr<T> value) async {
  if (value is Future<T>) {
    var result = await value;
    print(result);
    return result;
  } else {
    print(value);
    return value;
  }
}
```

{:.bad}
<?code-excerpt "usage_bad.dart (test-future-or)"?>
```dart
Future<T> logValue<T>(FutureOr<T> value) async {
  if (value is T) {
    print(value);
    return value;
  } else {
    var result = await value;
    print(result);
    return result;
  }
}
```

잘못된 예제에서 `Future<Object>`를 넘겨주면, 이를 빈 동기화 값으로 잘못 취급합니다.

[pokemon]: https://blog.codinghorror.com/new-programming-jargon/
[Error]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Error-class.html
[StackOverflowError]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/StackOverflowError-class.html
[OutOfMemoryError]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/OutOfMemoryError-class.html
[ArgumentError]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/ArgumentError-class.html
[AssertionError]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/AssertionError-class.html
[Exception]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Exception-class.html
