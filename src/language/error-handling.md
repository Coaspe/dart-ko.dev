---
title: 에러 핸들링
description: Dart 언어에서 에러와 예외를 핸들링하는 방법에 대해 학습합니다.
prevpage:
  url: /language/branches
  title: Branches
nextpage:
  url: /language/classes
  title: 클래스
---

## 예외

Dart 코드는 예외를 발생, 캐치할 수 있습니다. 
예외는 예상하지 못한 일이 발생했다는 것을 의미하는 에러입니다.
예외가 캐치되지 않았다면, 예외를 발생시키는 [isolate][]가
지연된 상태이고 보통 해당 isolate나 프로그램이 종료됩니다.

Java와 다르게, Dart의 모든 예외는 확인되지 않은 예외입니다.
메서드는 자신이 어떤 예외를 발생시킬지 선언하지 않고,
개발자에게 예외를 캐치하도록 요구하지도 않습니다.

Dart는 미리 정의된 다양한 서브타입과 함께 [`Exception`][] 그리고 [`Error`][]
타입을 제공합니다. 원하는 예외를 정의하는 것도 가능합니다. 그러나,
Dart 프로그램은 Exception이나 Error 객체 이외에도 모든 non-null 객체를
예외로 발생할 수 있습니다.

### Throw

다음 예제는 예외를 throwing 또는 *raising* 하는 코드입니다:

<?code-excerpt "misc/lib/language_tour/exceptions.dart (throw-FormatException)"?>
```dart
throw FormatException('Expected at least 1 section');
```

임의의 객체도 throw 할 수 있습니다:

<?code-excerpt "misc/lib/language_tour/exceptions.dart (out-of-llamas)"?>
```dart
throw 'Out of llamas!';
```

{{site.alert.note}}
  프로덕션 수준의 코드는 보통 [`Error`][] 또는 [`Exception`][]을 구현한 타입을 발생시킵니다.
{{site.alert.end}}

예외를 발생시키는 것은 표현식이기 때문에, 표현식을 사용할 수 있는 곳이라면, =\> 구문을
사용하여 예외를 발생시킬 수 있습니다:

<?code-excerpt "misc/lib/language_tour/exceptions.dart (throw-is-an-expression)"?>
```dart
void distanceTo(Point other) => throw UnimplementedError();
```


### Catch

예외를 Catching 또는 capturing 하는 것은 예외가 전파되는 것을 막아줍니다
(막지 않는다면 예외를 rethrow 합니다).
예외를 캐치하면 해당 예외를 처리할 수 있습니다:

<?code-excerpt "misc/lib/language_tour/exceptions.dart (try)"?>
```dart
try {
  breedMoreLlamas();
} on OutOfLlamasException {
  buyMoreLlamas();
}
```

한 개 이상의 예외 타입을 발생시키는 코드를 처리할 때,
다수의 catch 문을 사용할 수 있습니다. 발생된 객체의 타입을 매치하는 첫 번째 catch 절은
해당 예외를 처리합니다. catch 문에 타입을 명시하지 않는다면, 해당 코드는 발생되는 모든
타입의 예외를 처리할 수 있습니다:

<?code-excerpt "misc/lib/language_tour/exceptions.dart (try-catch)"?>
```dart
try {
  breedMoreLlamas();
} on OutOfLlamasException {
  // 특정한 예외
  buyMoreLlamas();
} on Exception catch (e) {
  // Exception 타입인 예외
  print('Unknown exception: $e');
} catch (e) {
  // 타입을 정하지 않은 catch 절로 모든 예외를 처리
  print('Something really unknown: $e');
}
```

위의 코드에서 볼 수 있듯이, `on` 또는 `catch`를 모두 사용할 수 있습니다.
예외 타입을 특정해야 할 때 `on`을 사용하세요. 예외 핸들러가 예외 객체를 필요로 할 때
`catch`를 사용하세요.

`catch()`에 하나 또는 두개의 매개변수를 전달 할 수 있습니다.
첫 번째는 발생될 예외이고,
두 번째는 스택 트레이스([`StackTrace`][] 객체)입니다.

<?code-excerpt "misc/lib/language_tour/exceptions.dart (try-catch-2)" replace="/\(e.*?\)/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
try {
  // ···
} on Exception catch [!(e)!] {
  print('Exception details:\n $e');
} catch [!(e, s)!] {
  print('Exception details:\n $e');
  print('Stack trace:\n $s');
}
{% endprettify %}

예외를 부분적으로 처리하기 위해,
`rethrow` 키워드를 사용하여 에러의 전파를 허용합니다.

<?code-excerpt "misc/test/language_tour/exceptions_test.dart (rethrow)" replace="/rethrow;/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
void misbehave() {
  try {
    dynamic foo = true;
    print(foo++); // 런타임 에러
  } catch (e) {
    print('misbehave() partially handled ${e.runtimeType}.');
    [!rethrow;!] // 호출자가 예외를 확인 할 수 있도록 허락
  }
}

void main() {
  try {
    misbehave();
  } catch (e) {
    print('main() finished handling ${e.runtimeType}.');
  }
}
{% endprettify %}


### Finally

예외 발생 여부와 상관 없이 어떤 코드를 실행하고 싶다면,
`finally` 절을 사용하세요. `catch` 절과 매치되는 예외가 없다면,
예외는 `finally` 절 실행 이후로 전파됩니다:

<?code-excerpt "misc/lib/language_tour/exceptions.dart (finally)"?>
```dart
try {
  breedMoreLlamas();
} finally {
  // 예외가 발생되더라도, 항상 실행됩니다.
  cleanLlamaStalls();
}
```

The `finally` clause runs after any matching `catch` clauses:

<?code-excerpt "misc/lib/language_tour/exceptions.dart (try-catch-finally)"?>
```dart
try {
  breedMoreLlamas();
} catch (e) {
  print('Error: $e'); // 예외를 먼저 처리합니다.
} finally {
  cleanLlamaStalls(); // 다음 실행
}
```

To learn more, check out the
[core library exception docs](/libraries/dart-core#exceptions).

## Assert

During development, use an assert 
statement— `assert(<condition>, <optionalMessage>);` —to
disrupt normal execution if a boolean condition is false. 

<?code-excerpt "misc/test/language_tour/control_flow_test.dart (assert)"?>
```dart
// Make sure the variable has a non-null value.
assert(text != null);

// Make sure the value is less than 100.
assert(number < 100);

// Make sure this is an https URL.
assert(urlString.startsWith('https'));
```

To attach a message to an assertion,
add a string as the second argument to `assert`
(optionally with a [trailing comma][]):

<?code-excerpt "misc/test/language_tour/control_flow_test.dart (assert-with-message)"?>
```dart
assert(urlString.startsWith('https'),
    'URL ($urlString) should start with "https".');
```

The first argument to `assert` can be any expression that
resolves to a boolean value. If the expression's value
is true, the assertion succeeds and execution
continues. If it's false, the assertion fails and an exception (an
[`AssertionError`][]) is thrown.

When exactly do assertions work?
That depends on the tools and framework you're using:

* Flutter enables assertions in [debug mode.][Flutter debug mode]
* Development-only tools such as [`webdev serve`][]
  typically enable assertions by default.
* Some tools, such as [`dart run`][] and [`dart compile js`][]
  support assertions through a command-line flag: `--enable-asserts`.

In production code, assertions are ignored, and
the arguments to `assert` aren't evaluated.

[trailing comma]: /language/collections#trailing-comma
[`AssertionError`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/AssertionError-class.html
[Flutter debug mode]: {{site.flutter-docs}}/testing/debugging#debug-mode-assertions
[`webdev serve`]: /tools/webdev#serve
[`dart run`]: /tools/dart-run
[`dart compile js`]: /tools/dart-compile#js

[isolate]: /language/concurrency
[`Error`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Error-class.html
[`Exception`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Exception-class.html
[`StackTrace`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/StackTrace-class.html
