---
title: 변수
description: Dart의 변수에 대해 학습합니다.
---

<?code-excerpt replace="/ *\/\/\s+ignore_for_file:[^\n]+\n//g; /(^|\n) *\/\/\s+ignore:[^\n]+\n/$1/g; /(\n[^\n]+) *\/\/\s+ignore:[^\n]+\n/$1\n/g; / *\/\/\s+ignore:[^\n]+//g; /([A-Z]\w*)\d\b/$1/g"?>

아래는 변수를 생성하고 초기화하는 예제입니다:

<?code-excerpt "misc/lib/language_tour/variables.dart (var-decl)"?>
```dart
var name = 'Bob';
```

변수는 레퍼런스를 저장합니다. `name`이라는 변수는 "Bob"이라는 값을
가지고 있는 `String` 객체의 레퍼런스를 포함합니다.

`name`의 타입은 `String`으로 추론되지만, 구체적으로 명시하여 타입을 변경 할 수 있습니다.
만약 객체가 단일 타입으로 제한되지 않는다면,
`Object` 타입으로 명시하세요 (필요하다면 `dynamic` 사용).

<?code-excerpt "misc/lib/language_tour/variables.dart (type-decl)"?>
```dart
Object name = 'Bob';
```

추론될 타입으로 명시하여 선언하는 방법도 있습니다:

<?code-excerpt "misc/lib/language_tour/variables.dart (static-types)"?>
```dart
String name = 'Bob';
```

{{site.alert.note}}
  이 페이지에서는 [스타일 가이드 추천](/guides/language/effective-dart/design#types)
  에 따라 지역 변수에 대해 타입 어노테이션을 하지 않고, `var`을 사용합니다.
{{site.alert.end}}


## 디폴트 값

Nullable 타입을 가지는 초기화되지 않은 변수는
초기 값으로 `null`을 가질 수 있습니다.
Dart의 다른 모든 것과 마찬가지로 숫자도 객체이기 때문에,
숫자 타입의 변수도 처음에는 null 입니다.

<?code-excerpt "misc/test/language_tour/variables_test.dart (var-null-init)"?>
```dart
int? lineCount;
assert(lineCount == null);
```

{{site.alert.note}}
  프로덕션 코드는 `assert()` 호출을 무시합니다. 반면에 개발하는 동안에는, 
  _조건_ 이 false라면 <code>assert(<em>조건</em>)</code> 가 예외를
  throw 합니다. 더 자세한 것은, [Assert][]를 참고하세요.
{{site.alert.end}}

Null 안전성을 활성화 했다면, non-nullable 변수를 사용하기 전에
값들을 초기화해야만 합니다:

<?code-excerpt "misc/lib/language_tour/variables.dart (var-ns-init)"?>
```dart
int lineCount = 0;
```

지역 변수를 선언과 동시에 초기화 할 필요는 없지만,
사용하기 전에 값을 할당해야 합니다.
예를 들어, 다음 코드는 `lineCount`가 `print()`로 전달될 때까지
null이 아님을 알 수 있기 때문에 유효합니다:

<?code-excerpt "misc/lib/language_tour/variables.dart (var-ns-flow)"?>
```dart
int lineCount;

if (weLikeToCount) {
  lineCount = countLines();
} else {
  lineCount = 0;
}

print(lineCount);
```

최상위, 클래스 변수는 지연 초기화 됩니다;
변수가 처음 사용 될 때, 초기화 코드가 실행됩니다.


## Late 변수

'late' 수식어의 용례는 2가지가 있습니다:

* 선언 이후에 초기화되는 non-nullable 변수를 선언하는 것
* 변수를 초기화를 지연하는 것

종종 Dart의 제어 흐름 분석기는 non-nullable 변수가
non-null 값으로 설정되어 있는지 사용하기 전에 알아챌 수 있지만,
가끔 실패할 때도 있습니다.
가장 흔히 사용되는 두 가지 케이스는 최상위 변수와 인스턴스 변수입니다:
Dart는 종종 그 변수들이 설정되었는지 판단할 수 없기 때문에,
시도하지 않습니다.

변숫값의 설정이 사용 전에 보장되지만
Dart가 동의하지 않는다면,
해당 변수를 `late`로 표시하여 에러를 해결할 수 있습니다:

<?code-excerpt "misc/lib/language_tour/variables.dart (var-late-top-level)" replace="/late/[!$&!]/g"?>
```dart
[!late!] String description;

void main() {
  description = 'Feijoada!';
  print(description);
}
```

{{site.alert.warn}}
  `late` 변수의 초기화를 실패하였다면,
  해당 변수를 사용 할 때 런타임 에러가 발생합니다.
{{site.alert.end}}

`late`로 표시한 변수를 선언과 동시에 초기화하면,
변수가 처음 사용될 때 이니셜라이저가 실행됩니다.
지연 초기화는 다음과 같은 상황에 유용합니다:

* 변수가 당장 필요하진 않지만,
  초기화 비용이 비쌀 때.
* 인스턴스 변수를 초기화에 이니셜라이저가
  `this`에 대한 접근이 필요할 때.

다음 예제에서,
`temperature` 변수가 사용되지 않으면,
비싼 함수인 `readThermometer()`가 호출되지 않습니다:

<?code-excerpt "misc/lib/language_tour/variables.dart (var-late-lazy)" replace="/late/[!$&!]/g"?>
```dart
// 이 프로그램에서 readThermometer()에 대한 유일한 호출입니다.
[!late!] String temperature = readThermometer(); // 지연 초기화.
```


## Final, const

변수를 변경할 생각이 없다면, `var` 대신 `final`이나 `const`를 사용하거나,
지정한 타입에 추가하여 사용하세요.
final 변수는 오직 한 번만 설정될 수 있습니다; const 변수는 컴파일 타임 상수입니다
(const 변수는 내부적으로 final입니다).

{{site.alert.note}}
  [인스턴스 변수][Instance variables]는 `final`로 설정될 수 있지만, `const`는 될 수 없습니다.
{{site.alert.end}}

다음은 `final` 변수를 생성, 설정하는 예제입니다:

<?code-excerpt "misc/lib/language_tour/variables.dart (final)"?>
```dart
final name = 'Bob'; // 타입 어노테이션이 없음
final String nickname = 'Bobby';
```

`final` 변수의 값은 변경할 수 없습니다:

{:.fails-sa}
<?code-excerpt "misc/lib/language_tour/variables.dart (cant-assign-to-final)"?>
```dart
name = 'Alice'; // 에러: final 변수는 한 번만 설정될 수 있습니다.
```

**컴파일 타임 상수**인 변수를 생성할 때 `const`를 사용하세요.
const 변수가 클래스 레벨의 변수라면, `static const`로 표시하세요.
변수를 선언할 때, 숫자, 문자열 리터럴, 상수 변수, 
또는 상수 숫자에 대한 산술 연산의 결과 같은 값들은 컴파일 타임 상수로 선언하세요:

<?code-excerpt "misc/lib/language_tour/variables.dart (const)"?>
```dart
const bar = 1000000; // 압력의 단위 (dynes/cm2)
const double atm = 1.01325 * bar; // 표준 대기
```

`const` 키워드는 상수 변수를 선언할 때만 쓰이는 것이 아닙니다.
상수 _값_ 을 만드는 데 사용할 수 있을 뿐만 아니라,
상수 값을 _만드는_ 생성자를 선언할 수도 있습니다.
모든 변수는 상수 값을 가질 수 있습니다.

<?code-excerpt "misc/lib/language_tour/variables.dart (const-vs-final)"?>
```dart
var foo = const [];
final bar = const [];
const baz = []; // `const []`와 동일
```

위의 `baz`처럼, `const` 선언의 초기화 식에 `const`를 생략해도 됩니다.
더 자세히 알고 싶다면, [const를 불필요하게 사용하지 마십시오][DON’T use const redundantly]를 참고하세요.

이전에 `const` 값을 가지고 있었더라도,
non-final, non-const 변수의 값을 변경할 수 있습니다.

<?code-excerpt "misc/lib/language_tour/variables.dart (reassign-to-non-final)"?>
```dart
foo = [1, 2, 3]; // const [] 였음
```

`const` 변수의 값은 바꿀 수 없습니다:

{:.fails-sa}
<?code-excerpt "misc/lib/language_tour/variables.dart (cant-assign-to-const)"?>
```dart
baz = [42]; // 에러: 상수 변수는 값이 할당될 수 없습니다.
```

[타입 체크와 캐스트][type checks and casts] (`is` 그리고 `as`),
[컬렉션 `if`][collection `if`],
그리고 [전개 연산자][spread operators] (`...` 그리고 `...?`)를
사용하는 상수 정의가 가능합니다:

<?code-excerpt "misc/lib/language_tour/variables.dart (const-dart-25)"?>
```dart
const Object i = 3; // i는 정수 값을 가지는 const Object입니다.
const list = [i as int]; // 타입 캐스트를 사용하세요.
const map = {if (i is int) i: 'int'}; // is와 컬렉션 if를 사용하세요.
const set = {if (list is List<int>) ...list}; // ...를 사용하여 전개.
```

{{site.alert.note}}
  `final` 객체는 수정할 수 없지만,
  객체의 필드는 수정이 가능합니다.
  반면에, `const` 객체와 객체의 필드는 _불변_ 하기 때문에
  변경할 수 없습니다.
{{site.alert.end}}

상수 값을 만들 때 `const`를 사용하는 방법에 대한 더 자세한 내용은 
[Lists][], [Maps][], 그리고 [Classes][]를 참고하세요.


[Assert]: /language/control-flow#assert
[Instance variables]: /language/classes#인스턴스-변수
[DON’T use const redundantly]: /guides/language/effective-dart/usage#const를-불필요하게-사용하지-마십시오
[type checks and casts]: /language/operators#타입-체크와-캐스트
[collection `if`]: /language/collections#제어-흐름-연산자
[spread operators]: /language/collections#전개-연산자
[Lists]: /language/collections#lists
[Maps]: /language/collections#maps
[Classes]: /language/classes
