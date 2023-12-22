---
title: Built-in types
description: Information on the types Dart supports.
prevpage:
  url: /language/keywords
  title: Keywords
nextpage:
  url: /language/records
  title: Records
---

Dart 언어는 다음과 같은 특수한 내장 타입을 지원합니다:

- [Numbers](#numbers) (`int`, `double`)
- [Strings](#strings) (`String`)
- [Booleans](#booleans) (`bool`)
- [Records][] (`(value1, value2)`)
- [Lists][] (`List`, *arrays*로도 부릅니다.)
- [Sets][] (`Set`)
- [Maps][] (`Map`)
- [Runes](#runes-과-grapheme-clusters) (`Runes`; 때때로 `characters` API로 대체됩니다.)
- [Symbols](#symbols) (`Symbol`)
- `null` 값 (`Null`)

이런 타입들은 리터럴을 사용하여 객체를 생성할 수 있습니다.
예를 들어, `'this is a string'`은 문자열 리터럴이고,
`true`는 boolean 리터럴입니다.

Dart의 모든 변수는 객체 (*클래스*의 인스턴스)이기 때문에,
변수를 초기화 할 때 *생성자*를 사용할 수 있습니다.
몇몇 내장 타입은 자신만의 생성자를 가지고 있습니다.
예를 들어, `Map()` 생성자를 사용하여 map을 생성할 수 있습니다.

Dart 언어의 일부 타입들은 특수한 역할을 수행합니다:

* `Object`: `Null`을 제외한 모든 Dart 클래스의 부모 클래스.
* `Enum`: 모든 eunm의 부모 클래스.
* `Future`, `Stream`: [비동기 지원][asynchrony support]에서 사용됩니다.
* `Iterable`: [for-in 루프][iteration] 그리고
  동기식 [제너레이터 함수][generator functions]에서 사용됩니다.
* `Never`: 식(expression)의 평가를 완료할 수 없음을 나타냅니다.
  항상 예외를 발생시키는 함수에서 보통 사용됩니다.
* `dynamic`: 정적 타입 체킹의 비활성화를 의미합니다.
  대개 `Object` 또는 `Object?`를 대신 사용하세요.
* `void`: 값이 사용되지 않는다는 것을 의미합니다.
  보통 반환 타입으로 사용됩니다.

`Object`, `Object?`, `Null`, 그리고 `Never` 클래스는
[Understanding null safety][]의 [top-and-bottom][]
섹션에 묘사되어 있는 것처럼, 클래스 계층에서 특별한 역할을 수행합니다.

{% comment %}
If we decide to cover `dynamic` more,
here's a nice example that illustrates what dynamic does:
  dynamic a = 2;
  String b = a; // No problem! Until runtime, when you get an uncaught error.

  Object c = 2;
  String d = c;  // Problem!
{% endcomment %}


## Numbers

Dart의 숫자는 두 가지 유형이 있습니다:

[`int`][]

:   [사용하는 플랫폼에 따라서][dart-numbers]
    정수 값은 64비트 이하로 표현됩니다.
    네이티브 플랫폼에서는 -2<sup>63</sup> ~ 2<sup>63</sup> - 1
    까지 표현됩니다.
    웹에서는, Javascript numbers (가수부가 없는 64-bits 부동소수점 표현)
    -2<sup>53</sup> to 2<sup>53</sup> - 1 사이의 수로 표현됩니다.

[`double`][]

:   IEEE 754 standard를 따라 64-bit (배정도) 부동 소수점 표현을 사용합니다.

`int` 와 `double`는 모두 [`num`][]의 서브타입입니다.
num 타입은 +, -, /, \* 같은 기본적인 연산자 사용이 가능하고, `abs()`,` ceil()`,
그리고 `floor()` 같은 함수의 사용도 가능합니다.
(\>\> 같은 비트와이즈 연산자는 `int` 클래스에 정의되어 있습니다.)
당신이 원하는 타입을 num과 num의 서브타입이 가지고 있지 않다면, [dart:math][]
라이브러리를 참고하세요.

정수는 소수점이 없는 숫자입니다. 다음은 정수 리터럴을 정의하는 예제입니다:

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (integer-literals)"?>
```dart
var x = 1;
var hex = 0xDEADBEEF;
```

숫자가 소수점을 가지고 있다면, 그것은 double 입니다. 다음은 double 리터럴을 정의하는 예제입니다:

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (double-literals)"?>
```dart
var y = 1.1;
var exponents = 1.42e5;
```

변수를 num으로 선언할 수도 있습니다. 이렇게 선언하면, 해당 변수는
정수, double 값을 모두 가질 수 있습니다.

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (declare-num)"?>
```dart
num x = 1; // x는 int, double 둘 다 가능합니다.
x += 2.5;
```

Integer literals are automatically converted to doubles when necessary:

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (int-to-double)"?>
```dart
double z = 1; // double z = 1.0 와 동일합니다.
```

다음은 예제에서 문자열을 숫자로 그리고 그 반대의 변환도 수행합니다:

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (number-conversion)"?>
```dart
// String -> int
var one = int.parse('1');
assert(one == 1);

// String -> double
var onePointOne = double.parse('1.1');
assert(onePointOne == 1.1);

// int -> String
String oneAsString = 1.toString();
assert(oneAsString == '1');

// double -> String
String piAsString = 3.14159.toStringAsFixed(2);
assert(piAsString == '3.14');
```

`int` 타입은 비트 필드에서 플래그를 조작하고 마스킹하는 데 유용한
비트 단위 쉬프트 (`<<`, `>>`, `>>>`),
보수 (`~`), AND (`&`), OR (`|`), 그리고 XOR (`^`) 연산자를 지원합니다.
예제:

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (bit-shifting)"?>
```dart
assert((3 << 1) == 6); // 0011 << 1 == 0110
assert((3 | 4) == 7); // 0011 | 0100 == 0111
assert((3 & 4) == 0); // 0011 & 0100 == 0000
```

더 많은 예제를 보고 싶다면,
[비트 단위, 쉬프트 연산자][] 섹션을 참고하세요.

리터럴 숫자는 컴파일 타임 상수입니다.
피연산자가 숫자를 평가(evaluate)하는 컴파일 타임 상수인 이상,
산술 표현식(expression)도 컴파일 타임 상수 입니다.

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (const-num)"?>
```dart
const msPerSecond = 1000;
const secondsUntilRetry = 5;
const msUntilRetry = secondsUntilRetry * msPerSecond;
```

더 많은 정보를 원한다면, [Dart의 숫자][dart-numbers]를 참고하세요.


## Strings

Dart의 문자열(`String` 객체)은 UTRF-16 코드 유닛의 시퀀스를 홀드합니다.
문자열을 만들 때 작은 따옴표, 큰 따옴표 모두 사용이 가능합니다:

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (quoting)"?>
```dart
var s1 = 'Single quotes work well for string literals.';
var s2 = "Double quotes work just as well.";
var s3 = 'It\'s easy to escape the string delimiter.';
var s4 = "It's even easier to use the other delimiter.";
```

`${`*`표현식`*`}`을 사용하여 표현식 안에 값을 넣을 수 있습니다.
표현식이 식별자라면, {}을 생략할 수 있습니다. 객체와 일치하는 문자열을 얻기 위해,
Dart는 객체의 `toString()` 메서드를 호출합니다.

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (string-interpolation)"?>
```dart
var s = 'string interpolation';

assert('Dart has $s, which is very handy.' ==
    'Dart has string interpolation, '
        'which is very handy.');
assert('That deserves all caps. '
        '${s.toUpperCase()} is very handy!' ==
    'That deserves all caps. '
        'STRING INTERPOLATION is very handy!');
```

{{site.alert.note}}
  `==` 연산자는 두 객체가 동일한지 테스트합니다.
  두 문자열이 동일한 코드 유닛의 시퀀스를 포함한다면, 같은 문자열로 판단합니다.
{{site.alert.end}}

인접 문자열 리터럴 (adjacent string literal) 또는
`+` 연산자를 사용해 문자열을 합칠 수 있습니다:

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (adjacent-string-literals)"?>
```dart
var s1 = 'String '
    'concatenation'
    " works even over line breaks.";
assert(s1 ==
    'String concatenation works even over '
        'line breaks.');

var s2 = 'The + operator ' + 'works, as well.';
assert(s2 == 'The + operator works, as well.');
```

작은 따옴표 또는 큰 따옴표로 triple quote를 형성해
싱글 또는 멀티 라인 문자열을 만들 수 있습니다.

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (triple-quotes)"?>
```dart
var s1 = '''
You can create
multi-line strings like this one.
''';

var s2 = """This is also a
multi-line string.""";
```

`r`을 접두사로 사용하여 "로우" (raw) 문자열을 생성할 수 있습니다:

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (raw-strings)"?>
```dart
var s = r'In a raw string, not even \n gets special treatment.';
```

유니코드 문자로 문자열을 표현하는 방법에 대해 자세히 알고 싶다면,
[Runes와 grapheme clusters](#characters)을 참고하세요.

Null, 숫자, 문자열 또는 boolean 값을 평가하는
보간 표현식 (interpolated expression)이
컴파일 타임 상수인 이상, 리터럴 문자열은 컴파일 타임 상수입니다.

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (string-literals)"?>
```dart
// 이 값들은 const 문자열로 작동합니다.
const aConstNum = 0;
const aConstBool = true;
const aConstString = 'a constant string';

// 이 값들은 const 문자열로 작동하지 않습니다.
var aNum = 0;
var aBool = true;
var aString = 'a string';
const aConstList = [1, 2, 3];

const validConstString = '$aConstNum $aConstBool $aConstString';
// const invalidConstString = '$aNum $aBool $aString $aConstList';
```

문자열 사용에 대해 더 자세히 알고 싶다면,
[문자열과 정규 표현식](/guides/libraries/library-tour#strings-and-regular-expressions)을
참고하세요.


## Booleans

Dart는 boolean형 타입을 `bool`로 명명하였습니다. 
컴파일 타임 상수인 Boolean 리터럴:
`true`와 `false`가 유일한 bool 타입 객체입니다.

Dart의 타입 안전성은
<code>if (<em>nonbooleanValue</em>)</code> 또는
<code>assert (<em>nonbooleanValue</em>)</code>
같은 코드를 사용할 수 없다는 것을 의미합니다.
다음과 같이 명시적으로 값을 확인해야합니다:

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (no-truthy)"?>
```dart
// 빈 문자열인지 확인합니다.
var fullName = '';
assert(fullName.isEmpty);

// 0인지 확인합니다.
var hitPoints = 0;
assert(hitPoints <= 0);

// null인지 확인합니다.
var unicorn = null;
assert(unicorn == null);

// NaN인지 확인합니다.
var iMeantToDoThis = 0 / 0;
assert(iMeantToDoThis.isNaN);
```

## Runes과 grapheme clusters

Dart에서 [runes][]은 문자열의 유니코드 코드 포인트를 나타냅니다.
[characters package][]를 사용하여
[Unicode (extended) grapheme clusters][grapheme clusters]
라고도 부르는 사용자가 인식하는 문자를 보거나 조작할 수 있습니다.

유니코드는 세상의 모든 문자, 숫자, 기호 시스템에 대해 고유한 숫자 값을 정의합니다. 
Dart의 문자열은 UTF-16 코드 단위의 시퀀스이기 때문에
문자열 내에서 유니코드 코드 포인트를 표현하려면 특별한 문법이 필요합니다.
유니코드 코드 포인트를 표현하는 가장 흔한 방법은 `\uXXXX` 형태로 나타내는 것이고,
XXXX는 16진수 4-digit 값입니다. 예를 들면 하트 문자(♥)는 `\u2665`입니다.
4개의 16진수 보다 적거나, 많이 사용하고 싶다면, 중괄호 안에 값을 넣으면 됩니다.
예를 들면 웃는 이모지(😆)는 `\u{1f606}`으로 나타냅니다.

단일 유니코드 문자를 읽고 써야하는 경우
characters 패키지에 정의된 `characters` getter를 사용하세요.
반환된 [`Characters`][] 객체는 graphem clusters의 시퀀스로 이루어진 문자열 입니다.
아래는 characters API를 사용한 예제 입니다:

<?code-excerpt "misc/lib/language_tour/characters.dart"?>
```dart
import 'package:characters/characters.dart';

void main() {
  var hi = 'Hi 🇩🇰';
  print(hi);
  print('The end of the string: ${hi.substring(hi.length - 1)}');
  print('The last character: ${hi.characters.last}');
}
```

실행 환경에 따라 출력은 다음과 같습니다:

```terminal
$ dart run bin/main.dart
Hi 🇩🇰
The end of the string: ???
The last character: 🇩🇰
```

문자열 조작을 위한 characters 패키지 사용에 대해 더 자세히 알고 싶다면,
[예제][characters example]와 [API reference][characters API] 를 참고하세요.

## Symbols

[`Symbol`][] 객체는 Dart 프로그램에 선언된 연산자나 식별자를 나타냅니다.
Symbol은 거의 필요하지 않지만 코드 압축(minification) 후 식별자의 이름이 변경되더라도
symbol은 변경되지 않기 때문에 식별자를 통한 API 참조에 유용합니다.

식별자에 대한 symbol를 가져오려면 symbol 리터럴을 사용하면 됩니다.
Symbol 리터럴은 `#` 뒤에 식별자를 위치시키면 됩니다:

```nocode
#radix
#bar
```

{% comment %}
The code from the following excerpt isn't actually what is being shown in the page

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (symbols)"?>
```dart
void main() {
  print(Function.apply(int.parse, ['11']));
  print(Function.apply(int.parse, ['11'], {#radix: 16}));
}
```
{% endcomment %}

Symbol 리터럴은 컴파일 타임 상수입니다.



[Records]: /language/records
[Lists]: /language/collections#lists
[Sets]: /language/collections#sets
[Maps]: /language/collections#maps
[asynchrony support]: /language/async
[iteration]: /guides/libraries/library-tour#iteration
[generator functions]: /language/functions#제너레이터
[Understanding null safety]: /null-safety/understanding-null-safety#top-and-bottom
[`int`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/int-class.html
[`double`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/double-class.html
[`num`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/num-class.html
[dart:math]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-math
[비트 단위, 쉬프트 연산자]: /language/operators#비트-단위-쉬프트-연산자
[dart-numbers]: /guides/language/numbers
[runes]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Runes-class.html
[characters package]: {{site.pub-pkg}}/characters
[grapheme clusters]: https://unicode.org/reports/tr29/#Grapheme_Cluster_Boundaries
[`Characters`]: {{site.pub-api}}/characters/latest/characters/Characters-class.html
[characters API]: {{site.pub-api}}/characters
[characters example]: {{site.pub-pkg}}/characters/example
[`Symbol`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Symbol-class.html
