---
title: 함수
description: Dart 언어의 함수에 대한 모든 것
js: [{url: '/assets/js/inject_dartpad.js', defer: true}]
prevpage:
  url: /language/pattern-types
  title: Pattern types
nextpage:
  url: /language/loops
  title: Loops
---

Dart는 객체 지향 언어이므로, 함수도
[Function][Function API reference]
이라는 타입을 가지는 객체로 존재합니다.
이건 함수가 변수나 다른 함수의 인자로 전달할 수 있다는 것을 의미합니다.
Dart 클래스의 인스턴스를 함수인 것처럼 호출할 수 있습니다.
더 자세한 정보를 원한다면, [호출 가능한 객체][Callable objects]를 참고하세요.

다음은 함수를 구현하는 예제입니다:

<?code-excerpt "misc/lib/language_tour/functions.dart (function)"?>
```dart
bool isNoble(int atomicNumber) {
  return _nobleGases[atomicNumber] != null;
}
```

비록 Effective Dart에서
[public APIs를 위한 타입 어노테이션][type annotations for public APIs]
을 추천하지만, 타입을 생략해도 함수는 제대로 작동합니다:

<?code-excerpt "misc/lib/language_tour/functions.dart (function-omitting-types)"?>
```dart
isNoble(atomicNumber) {
  return _nobleGases[atomicNumber] != null;
}
```

하나의 표현식만을 가지는 함수를 선언 할 때 약칭(shorthand) 문법의 사용이 가능합니다:

<?code-excerpt "misc/lib/language_tour/functions.dart (function-shorthand)"?>
```dart
bool isNoble(int atomicNumber) => _nobleGases[atomicNumber] != null;
```

<code>=> <em>표현식</em></code> 문법은
<code>{ return <em>표현식</em>; }</code>의 약칭입니다.
`=>` 노테이션은 _화살표_ 문법으로도 불립니다.

:::note
  *문(statement)*이 아닌 오직 *식(expression)*만이 화살표 (=\>)와
  세미콜론 (;) 사이에 올 수 있습니다. 예를 들어, [if 문][if statement]은 불가능하지만,
  [조건식][conditional expression]은 가능합니다.
:::

## 매개변수

함수는 *required positional* 매개변수를 얼마든지 가질 수 있습니다.
이 매개변수들은 *named* 매개변수 또는 *optional positional* 매개변수의 뒤에 나올 수 있습니다.
(둘 다 사용하는 것은 불가능합니다.)

:::note
  [Flutter][] 위젯 생성자 처럼 몇몇 API들은 필수 매개변수에 대해서도
  named 매개 변수만 사용합니다. 다음 섹션에서 자세히 살펴봅시다.
:::

함수에 인자를 넘겨줄 때나 함수의 매개변수를 정의할 때
[trailing commas][]를 사용할 수 있습니다.


### Named 매개변수

Named 매개변수는 `required`로 표시되지 않는 이상
선택적인 매개변수입니다.

함수를 정의할 때,
<code>{<em>매개변수1</em>, <em>매개변수2</em>, …}</code>
를 사용하여 named 매개변수를 표시하세요.
디폴트 값을 제공하지 않거나
named 매개변수를 `required`로 표시하지 않으면
해당 매개 변수의 타입은 디폴트 값이 `null`이 되므로
nullable로 지정해야 합니다:

<?code-excerpt "misc/lib/language_tour/functions.dart (specify-named-parameters)"?>
```dart
/// Sets the [bold] and [hidden] flags ...
void enableFlags({bool? bold, bool? hidden}) {...}
```

함수를 호출할 때,
<code><em>매개변수이름</em>: <em>값</em></code> 을
사용하여 넘겨줄 named 인자를 특정할 수 있습니다.
예제:

<?code-excerpt "misc/lib/language_tour/functions.dart (use-named-parameters)"?>
```dart
enableFlags(bold: true, hidden: false);
```

<a id="default-parameters"></a>
`Null`이 아닌 값으로 named 매개변수의 디폴트 값을 정의하려면 `=`를 사용하세요.
디폴트 값은 반드시 컴파일 타임 상수로 지정되야합니다.
예제:

<?code-excerpt "misc/lib/language_tour/functions.dart (named-parameter-default-values)"?>
```dart
/// [bold] 그리고 [hidden] 플래그 설정 ...
void enableFlags({bool bold = false, bool hidden = false}) {...}

// bold는 true로 설정됩니다; hidden은 false로 설정됩니다..
enableFlags(bold: true);
```

일반적으로 positional 매개변수를 맨 앞에 두는 것이 더 합리적이지만,
named 매개변수를 매개변수 목록의 임의의 위치에 두어 호출 방식이 API에 더
적합하게 보이도록 할 수도 있습니다:

<?code-excerpt "misc/lib/language_tour/functions.dart (required-named-parameters)" replace="/required/[!$&!]/g"?>
```dart
const Scrollbar({super.key, [!required!] Widget child});
```

사용자가 `child` 인수를 명시하지 않고 `Scrollbar`를 생성하려 하면 분석 도구는 문제가 있음을 보고하게 됩니다.

:::note
  `required` 가 있는 매개변수는 여전히 nullable한 값을 지닐 수 있습니다.

<?code-excerpt "misc/lib/language_tour/functions.dart (required-named-parameters-nullable)" replace="/Widget\?/[!$&!]/g; /ScrollbarTwo/Scrollbar/g;"?>
```dart
const Scrollbar({super.key, required [!Widget?!] child});
```
:::

positional 매개변수를 맨 앞에 위치하고 싶을수도 있지만, 다트에서 이는 필수적인 사항이 아닙니다.
다트에서는 여러분의 API에 맞게 매개면수 목록 어디에나 named 매개변수를 배치할 수 있습니다.

<?code-excerpt "misc/lib/language_tour/functions.dart (named-arguments-anywhere)"?>
```dart
repeat(times: 2, () {
  ...
});
```

### Optional positional 매개변수

함수 매개변수들의 세트를 `[]`로 감싸는 것은
해당 매개변수들을 optional positional 매개변수로 표시합니다.
디폴트 값을 제공하지 않으면, 매개변수의 디폴트 값이
`null`이 되므로 타입은 반드시 nullable이 되어야 합니다:

<?code-excerpt "misc/test/language_tour/functions_test.dart (optional-positional-parameters)"?>
```dart
String say(String from, String msg, [String? device]) {
  var result = '$from says $msg';
  if (device != null) {
    result = '$result with a $device';
  }
  return result;
}
```

다음은 optional 매개변수 없이 함수를 호출하는 예제입니다:

<?code-excerpt "misc/test/language_tour/functions_test.dart (call-without-optional-param)"?>
```dart
assert(say('Bob', 'Howdy') == 'Bob says Howdy');
```

다음은 3번째 매개변수를 포함하여 함수를 호출하는 예제입니다:

<?code-excerpt "misc/test/language_tour/functions_test.dart (call-with-optional-param)"?>
```dart
assert(say('Bob', 'Howdy', 'smoke signal') ==
    'Bob says Howdy with a smoke signal');
```

`Null`이 아닌 값으로 optional positional 매개변수의 디폴트 값을 정의하려면 `=`을 사용하세요.
디폴트 값은 반드시 컴파일 타임 상수로 지정되야합니다.
예제:

<?code-excerpt "misc/test/language_tour/functions_test.dart (optional-positional-param-default)"?>
```dart
String say(String from, String msg, [String device = 'carrier pigeon']) {
  var result = '$from says $msg with a $device';
  return result;
}

assert(say('Bob', 'Howdy') == 'Bob says Howdy with a carrier pigeon');
```


## main() 함수

모든 앱은 엔트리 포인트 역할을 하는 최상위 `main()` 함수를 반드시 가지고 있어야 합니다.
`main()` 함수는 `void`를 반환하고 인자로 optional `List<String>` 매개변수를 가집니다.

다음은 `main()` 함수의 예제입니다:

<?code-excerpt "misc/test/samples_test.dart (hello-world)"?>
```dart
void main() {
  print('Hello, World!');
}
```

다음은 인자를 가지는 커맨드 라인 앱의 `main()` 함수 예제입니다:

<?code-excerpt "misc/test/language_tour/functions_test.dart (main-args)"?>
```dart title="args.dart"
// 다음과 같이 앱을 실행하세요: dart args.dart 1 test
void main(List<String> arguments) {
  print(arguments);

  assert(arguments.length == 2);
  assert(int.parse(arguments[0]) == 1);
  assert(arguments[1] == 'test');
}
```

커맨드 라인 인자를 정의, 파싱하기 위해
[args library]({{site.pub-pkg}}/args)를 사용해도 됩니다.

## 일급 객체로서의 함수

다음과 같이 다른 함수의 인자로 함수를 넘기는 것이 가능합니다:

<?code-excerpt "misc/lib/language_tour/functions.dart (function-as-param)"?>
```dart
void printElement(int element) {
  print(element);
}

var list = [1, 2, 3];

// printElement를 매개변수로 넘깁니다.
list.forEach(printElement);
```

다음과 같이 변수에 함수를 할당하는 것도 가능합니다:

<?code-excerpt "misc/test/language_tour/functions_test.dart (function-as-var)"?>
```dart
var loudify = (msg) => '!!! ${msg.toUpperCase()} !!!';
assert(loudify('hello') == '!!! HELLO !!!');
```

위 예제에서는 익명 함수를 사용합니다.
익명 함수에 대해서는 다음 섹션에서 살펴봅시다.

## 익명 함수

대부분의 함수들은 `main()`나 `printElement()` 처럼 이름이 있습니다.
하지만 _익명 함수_, _람다_, _클로져_ 같이 이름이 없는 함수가 있습니다.
익명 함수를 변수에 선언해서 컬렉션에 추가하고 제거하는 것도 가능합니다.

괄호 안에 콤마로 분리된 매개변수들, optional 타입 어노테이션,
0개 또는 그 이상의 매개변수 같이 익명 함수가 가지는 특징들이 named 함수와 비슷해보입니다.

다음 코드 블럭은 함수 바디를 포함합니다:

<code>
([[<em>Type</em>] <em>param1</em>[, …]]) { <br>
&nbsp;&nbsp;<em>codeBlock</em>; <br>
}; <br>
</code>

다음 예제는 타입을 명시하지 않은 매개변수 `item`을 가지는 익명 함수를
`map` 함수에 넘기는 예제입니다.
이 함수는 list의 모든 아이템을 순회하며, 각 문자열을 대문자로 변환합니다.
그 다음 `forEach`로 넘겨지는 익명 함수에서,
변환된 문자열과 문자열의 길이를 출력합니다.

<?code-excerpt "misc/test/language_tour/functions_test.dart (anonymous-function)"?>
```dart
const list = ['apples', 'bananas', 'oranges'];
list.map((item) {
  return item.toUpperCase();
}).forEach((item) {
  print('$item: ${item.length}');
});
```

코드를 실행하려면 **Run**을 클릭하세요.

<?code-excerpt "misc/test/language_tour/functions_test.dart (anonymous-function-main)"?>
```dart:run-dartpad:height-400px:ga_id-anonymous_functions
void main() {
  const list = ['apples', 'bananas', 'oranges'];
  list.map((item) {
    return item.toUpperCase();
  }).forEach((item) {
    print('$item: ${item.length}');
  });
}
```

함수가 하나의 표현식이나 반환문을 가진다면,
화살표 노테이션을 사용하여 이를 줄일 수 있습니다.
다음 라인을 DartPad에 붙혀넣은 후 **Run**을 클릭하면,
이것이 기능적으로 동일한지 확인할 수 있습니다.

<?code-excerpt "misc/test/language_tour/functions_test.dart (anon-func)"?>
```dart
list
    .map((item) => item.toUpperCase())
    .forEach((item) => print('$item: ${item.length}'));
```


## 렉시컬 스코프

Dart는 `lexically scoped` 언어로,
변수의 범위가 코드의 레이아웃에 따라 정적으로 결정된다는 것을 의미합니다.
변수의 범위를 확인하고 싶다면 "중괄호의 끝을 따라가면" 됩니다.

다음은 각 스코프 레벨에 있는 변수를 포함하는 중첩 함수의 예제입니다:

<?code-excerpt "misc/test/language_tour/functions_test.dart (nested-functions)"?>
```dart
bool topLevel = true;

void main() {
  var insideMain = true;

  void myFunction() {
    var insideFunction = true;

    void nestedFunction() {
      var insideNestedFunction = true;

      assert(topLevel);
      assert(insideMain);
      assert(insideFunction);
      assert(insideNestedFunction);
    }
  }
}
```

`nestedFunction()`가 모든 레벨에서 변수를 어떻게 사용할 수 있는지 주목하세요.
최상위 수준까지 모든 수준의 변수 사용이 가능합니다.


## 렉시컬 클로저

*클로저* 함수 객체이며, 함수 객체의 호출이 원래 스코프
밖에서 발생하더라도 렉시컬 스코프 내의 변수에 여전히 접근할 수 있습니다.

함수는 주변 스코프에 정의된 변수를 포함합니다.
다음의 예제에서, `makeAdder()`는 `addBy` 변수를 캡쳐합니다.
함수가 반환되는 시간과 상관없이, 캡처된 `addBy` 변수를 사용할 수 있습니다.

<?code-excerpt "misc/test/language_tour/functions_test.dart (function-closure)"?>
```dart
/// [addBy]를 함수의 인자에 더하는 함수를 반환합니다.
Function makeAdder(int addBy) {
  return (int i) => addBy + i;
}

void main() {
  // 2를 더하는 함수를 생성합니다.
  var add2 = makeAdder(2);

  // 4를 더하는 함수를 생성합니다.
  var add4 = makeAdder(4);

  assert(add2(3) == 5);
  assert(add4(3) == 7);
}
```


## 동등성 테스트 함수

다음은 최상위 함수, 정적 메서드, 인스턴스 메서드의 동등성을 확인하는 테스트 코드 입니다:

<?code-excerpt "misc/lib/language_tour/function_equality.dart"?>
```dart
void foo() {} // 최상위 함수

class A {
  static void bar() {} // 정적 메서드
  void baz() {} // 인스턴스 메서드
}

void main() {
  Function x;

  // 최상위 함수를 비교.
  x = foo;
  assert(foo == x);

  // 정적 메서드를 비교.
  x = A.bar;
  assert(A.bar == x);

  // 인스턴스 메서드를 비교.
  var v = A(); // A의 인스턴스 #1
  var w = A(); // B의 인스턴스 #2
  var y = w;
  x = w.baz;

  // 두 클로저들은 같은 인스턴스 (#2)를 참조하므로 동일합니다.
  assert(y.baz == x);

  // 두 클로저들은 다른 인스턴스를 참조하므로 동일하지 않습니다.
  assert(v.baz != w.baz);
}
```


## 반환 값

모든 함수는 값을 반환합니다. 반환 값이 명시되어 있지 않으면,
`return null;`이 암묵적으로 함수의 바디에 추가됩니다.

<?code-excerpt "misc/test/language_tour/functions_test.dart (implicit-return-null)"?>
```dart
foo() {}

assert(foo() == null);
```

To return multiple values in a function, aggregate the values in a [record][].

```dart
(String, int) foo() {
  return ('something', 42);
}
```

[record]: /language/records#multiple-returns

## 제너레이터

데이터의 시퀀스를 지연하여(lazily) 생성하고 싶다면,
_제너레이터 함수_ 를 사용하세요. Dart는 두가지 내장 제너레이터 함수를 가지고 있습니다:

* **동기식** 제너레이터: [`Iterable`] 객체를 반환합니다.
* **비동기식** 제너레이터: [`Stream`] 객체를 반환합니다.

**동기식** 제너레이터 함수를 구현하려면, 함수의 바디를
`sync*`로 표시하고 `yield` 문으로 값을 생성하세요:

<?code-excerpt "misc/test/language_tour/async_test.dart (sync-generator)"?>
```dart
Iterable<int> naturalsTo(int n) sync* {
  int k = 0;
  while (k < n) yield k++;
}
```

**비동기식** 제너레이터 함수를 구현하려면, 함수의 바디를
`async*`로 표시하고 `yield` 문으로 값을 생성하세요:

<?code-excerpt "misc/test/language_tour/async_test.dart (async-generator)"?>
```dart
Stream<int> asynchronousNaturalsTo(int n) async* {
  int k = 0;
  while (k < n) yield k++;
}
```

제너레이터가 재귀적이라면,
`yield*`를 사용하여 성능을 향상시킬 수 있습니다:

<?code-excerpt "misc/test/language_tour/async_test.dart (recursive-generator)"?>
```dart
Iterable<int> naturalsDownFrom(int n) sync* {
  if (n > 0) {
    yield n;
    yield* naturalsDownFrom(n - 1);
  }
}
```


[`Iterable`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Iterable-class.html
[`Stream`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-async/Stream-class.html

## External functions {:#external}

An external function is a function whose body is implemented separately from its
declaration. Include the `external` keyword before a function declaration, like so:

```dart
external void someFunc(int i);
```

An external function's implementation can come from another Dart library,
or, more commonly, from another language. In interop contexts, `external`
introduces type information for foreign functions or values,
making them usable in Dart. Implementation and usage is
heavily platform specific, so check out the interop docs on, for example,
[C][] or [JavaScript][] to learn more.

External functions can be top-level functions, [instance methods][],
[getters or setters][], or [non-redirecting constructors][].
An [instance variable][] can be `external` too,
which is equivalent to an external getter and (if the variable
is not `final`) an external setter.

[instance methods]: /language/methods#instance-methods
[getters or setters]: /language/methods#getters-and-setters
[non-redirecting constructors]: /language/constructors#redirecting-constructors
[instance variable]: /language/classes#instance-variables
[C]: /interop/c-interop
[JavaScript]: /interop/js-interop

[Function API reference]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Function-class.html
[Callable objects]: /language/callable-objects
[type annotations for public APIs]: /effective-dart/design#do-type-annotate-fields-and-top-level-variables-if-the-type-isnt-obvious
[if statement]: /language/branches#if
[conditional expression]: /language/operators#conditional-expressions
[Flutter]: {{site.flutter}}
[trailing commas]: /language/collections#lists
