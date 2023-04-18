---
title: 제어문
description: Dart 코드의 흐름을 제어하는 방법에 대해 학습힙니다.
---

다음 키워드들을 사용하여 Dart 코드의 흐름을 제어할 수 있습니다:

-   `if` 그리고 `else`
-   `for` 루프
-   `while` 그리고 `do`-`while` 루프
-   `break` 그리고 `continue`
-   `switch` 그리고 `case`
-   `assert`

[예외][Exceptions]에 설명되어 있듯이, `try-catch` 그리고 `throw`를 사용해도
흐름 제어가 가능합니다.


## If, else

다음 예제가 보여주듯이, `if` 문에 `else` 문을 사용하는 것은 선택적입니다.
[조건 표현식][conditional expressions]도 살펴보세요.

<?code-excerpt "misc/lib/language_tour/control_flow.dart (if-else)"?>
```dart
if (isRaining()) {
  you.bringRainCoat();
} else if (isSnowing()) {
  you.wearJacket();
} else {
  car.putTopDown();
}
```

구문 조건은 반드시 boolean 값을 평가하는 표현식으로 제공되어야 합니다.
더 자세한 사항은 [Booleans][]을 살펴보세요.


## For 루프

표준 `for` 루프를 사용하여 반복을 수행할 수 있습니다. 예제:

<?code-excerpt "misc/test/language_tour/control_flow_test.dart (for)"?>
```dart
var message = StringBuffer('Dart is fun');
for (var i = 0; i < 5; i++) {
  message.write('!');
}
```

Dart의 `for` 루프 안에 있는 클로저는 JavaScript에서 흔하게 발생하는
위험을 피하면서 해당 인덱스의 _값_ 을 캡쳐합니다. 예제:

<?code-excerpt "misc/test/language_tour/control_flow_test.dart (for-and-closures)"?>
```dart
var callbacks = [];
for (var i = 0; i < 2; i++) {
  callbacks.add(() => print(i));
}

for (final c in callbacks) {
  c();
}
```

예상대로라면 `0`과 `1`을 출력합니다. 하지만, JavaScript에서
이 예제는 `2`와 `2`를 출력합니다.

반복하고 있는 객체가 List 또는 Set 같은 Iterable 이고
현재 반복 카운터를 알 필요가 없다면,
[iteration][]에 `for-in` 형태를 사용할 수 있습니다:

<?code-excerpt "misc/lib/language_tour/control_flow.dart (collection)"?>
```dart
for (final candidate in candidates) {
  candidate.interview();
}
```

{{site.alert.tip}}
  `for-in` 사용을 연습해보고 싶다면,
  [Iterable 컬렉션 코드랩](/codelabs/iterables)을 시도해보세요.
{{site.alert.end}}

Iterable 클래스는 다른 옵션으로 [forEach()][] 메서드 또한 가지고 있습니다:

<?code-excerpt "misc/test/language_tour/control_flow_test.dart (forEach)"?>
```dart
var collection = [1, 2, 3];
collection.forEach(print); // 1 2 3
```


## While, do-while

`while` 루프는 루프를 실행하기 전에 조건을 평가합니다:

<?code-excerpt "misc/lib/language_tour/control_flow.dart (while)"?>
```dart
while (!isDone()) {
  doSomething();
}
```

`do`-`while` 루프는 루프를 실행한 *뒤에* 조건을 평가합니다:

<?code-excerpt "misc/lib/language_tour/control_flow.dart (do-while)"?>
```dart
do {
  printLine();
} while (!atEndOfPage());
```


## Break, continue

루프를 멈추고 싶다면 `break`를 사용하세요:

<?code-excerpt "misc/lib/language_tour/control_flow.dart (while-break)"?>
```dart
while (true) {
  if (shutDownRequested()) break;
  processIncomingRequests();
}
```

다음 루프 반복으로 넘어가고 싶다면 `continue`를 사용하세요:

<?code-excerpt "misc/lib/language_tour/control_flow.dart (for-continue)"?>
```dart
for (int i = 0; i < candidates.length; i++) {
  var candidate = candidates[i];
  if (candidate.yearsExperience < 5) {
    continue;
  }
  candidate.interview();
}
```

List 또는 Set같은 [`Iterable`][]을 사용한다면 위의
예제를 다른 형태로 작성하는 것이 가능합니다:

<?code-excerpt "misc/lib/language_tour/control_flow.dart (where)"?>
```dart
candidates
    .where((c) => c.yearsExperience >= 5)
    .forEach((c) => c.interview());
```


## Switch, case

Dart의 Switch 문은 `==`를 사용해 정수, 문자열 그리고 컴파일 타임 상수를 비교합니다.
비교되는 객체는 반드시 동일한 클래스의 인스턴스이어야 하고 (서브타입도 불가능합니다),
해당 클래스는 `==`를 재정의해서는 안 됩니다.
[열거 타입][Enumerated types]은 `switch` 문에서 효과적입니다.

비어있지 않은 `case` 절은 `break` 문으로 끝나는 것이 규칙입니다.
비어있지 않은 `case` 절을 끝내는 또 다른 방법으로는 `continue`,
`throw`, 그리고 `return` 문이 있습니다.

모든 `case` 절에 해당하지 않는 코드를 실행하고 싶다면 `default` 절을 사용하세요:

<?code-excerpt "misc/lib/language_tour/control_flow.dart (switch)"?>
```dart
var command = 'OPEN';
switch (command) {
  case 'CLOSED':
    executeClosed();
    break;
  case 'PENDING':
    executePending();
    break;
  case 'APPROVED':
    executeApproved();
    break;
  case 'DENIED':
    executeDenied();
    break;
  case 'OPEN':
    executeOpen();
    break;
  default:
    executeUnknown();
}
```

다음 예제는 `case` 절에서 `break` 문을 생략해서
에러가 발생합니다:

<?code-excerpt "misc/lib/language_tour/control_flow.dart (switch-break-omitted)" plaster="none"?>
```dart
var command = 'OPEN';
switch (command) {
  case 'OPEN':
    executeOpen();
    // ERROR: Missing break

  case 'CLOSED':
    executeClosed();
    break;
}
```

그러나 Dart는 완성되지 않은 형태를 허용하며, 비어있는 `case` 문을 지원합니다:

<?code-excerpt "misc/lib/language_tour/control_flow.dart (switch-empty-case)"?>
```dart
var command = 'CLOSED';
switch (command) {
  case 'CLOSED': // Empty case falls through.
  case 'NOW_CLOSED':
    // Runs for both CLOSED and NOW_CLOSED.
    executeNowClosed();
    break;
}
```

완성되지 않은 case를 사용하고 싶다면, `continue` 문과 레이블을 같이 사용해도 됩니다:

<?code-excerpt "misc/lib/language_tour/control_flow.dart (switch-continue)"?>
```dart
var command = 'CLOSED';
switch (command) {
  case 'CLOSED':
    executeClosed();
    continue nowClosed;
  // nowClosed 레이블을 실행합니다.

  nowClosed:
  case 'NOW_CLOSED':
    // CLOSED 그리고 NOW_CLOSED에 대해 모두 실행합니다.
    executeNowClosed();
    break;
}
```

`case` 절은 해당 절의 범위에서만 사용 가능한 지역 변수를 가질 수 있습니다.


## Assert

개발하는 동안에 boolean 조건이 false 일 때 코드 진행을 멈추고 싶다면
<code>assert(<em>condition</em>, <em>optionalMessage</em>)</code>; 를 사용하세요.
이 페이지에서 많은 assert 문의 예제를 볼 수 있을 겁니다:

<?code-excerpt "misc/test/language_tour/control_flow_test.dart (assert)"?>
```dart
// 변수가 non-null 값을 가지도록 보장합니다.
assert(text != null);

// 값이 100 보다 작도록 보장합니다.
assert(number < 100);

// urlString이 https URL임을 보장합니다.
assert(urlString.startsWith('https'));
```

Assertion에 메시지를 더하고 싶다면,
`assert`의 두 번째 인자에 문자열을 추가하세요
([trailing comma][]를 사용해도됩니다):

<?code-excerpt "misc/test/language_tour/control_flow_test.dart (assert-with-message)"?>
```dart
assert(urlString.startsWith('https'),
    'URL ($urlString) should start with "https".');
```

`assert`의 첫 번째 인자에는 boolean 값을 평가하는 표현식이 주어져야 합니다.
표현식의 값이 true라면 assertion은 성공하고 다음 코드를 실행합니다.
False라면, assertion은 실패하고 예외([`AssertionError`][])
를 발생시킵니다.

Assertion의 역할이 정확하게 무엇일까요?
그건 사용하는 도구나 프레임워크에 따라 다릅니다:

* Flutter는 [debug mode][Flutter debug mode]에서만 assertion이 활성화됩니다.
* [`webdev serve`][] 같이 오직 개발을 위한 툴은
  보통 디폴트로 assertion이 활성화되어있습니다.
* [`dart run`][] and [`dart compile js`][] 같은 툴들은,
  커맨드 라인 플래그 `--enable-asserts`를 사용해 assertion을 지원합니다.

프로덕션 코드에서 assertion은 무시되고
`assert`의 인자는 평가되지 않습니다.

[Exceptions]: /language/error-handling#예외
[conditional expressions]: /language/operators#조건-표현식
[Booleans]: /language/built-in-types#booleans
[iteration]: /guides/libraries/library-tour#iteration
[forEach()]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Iterable/forEach.html
[`Iterable`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Iterable-class.html
[Enumerated types]: /language/enum
[trailing comma]: /language/collections#lists
[`AssertionError`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/AssertionError-class.html
[Flutter debug mode]: {{site.flutter-docs}}/testing/debugging#debug-mode-assertions
[`webdev serve`]: /tools/webdev#serve
[`dart run`]: /tools/dart-run
[`dart compile js`]: /tools/dart-compile#js
