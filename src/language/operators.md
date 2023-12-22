---
title: 연산자
description: Dart가 지원하는 연산자에 대해 학습합니다.
prevpage:
  url: /language/variables
  title: 변수
nextpage:
  url: /language/comments
  title: 주석
---

<?code-excerpt replace="/ *\/\/\s+ignore_for_file:[^\n]+\n//g; /(^|\n) *\/\/\s+ignore:[^\n]+\n/$1/g; /(\n[^\n]+) *\/\/\s+ignore:[^\n]+\n/$1\n/g; / *\/\/\s+ignore:[^\n]+//g; /([A-Z]\w*)\d\b/$1/g"?>

<a name="operators"></a>

Dart는 다음 표의 연산자들을 지원합니다.
표는 Dart 연산자들의 관계에 대한 **근사**인 Dart의 연산자 결합법칙과
[연산자 우선순위](#연산자-우선순위-예제)를 최고에서
최저의 순서로 알려줍니다.
[클래스 멤버로서 연산자][operators as class members]를 구현하는 것이 가능합니다.

|-----------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------|
| 설명                             | 연산자                                                                                                                                                                                          | 결합법칙 |
|-----------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------|
| unary postfix                           | <code><em>expr</em>++</code>    <code><em>expr</em>--</code>    `()`    `[]`    `?[]`    `.`    `?.`    `!`                                                                                       | None          |
| unary prefix                            | <code>-<em>expr</em></code>    <code>!<em>expr</em></code>    <code>~<em>expr</em></code>    <code>++<em>expr</em></code>    <code>--<em>expr</em></code>      <code>await <em>expr</em></code>    | None          |
| multiplicative                          | `*`    `/`    `%`    `~/`                                                                                                                                                                         | Left          |
| additive                                | `+`    `-`                                                                                                                                                                                        | Left          |
| shift                                   | `<<`    `>>`    `>>>`                                                                                                                                                                             | Left          |
| bitwise AND                             | `&`                                                                                                                                                                                               | Left          |
| bitwise XOR                             | `^`                                                                                                                                                                                               | Left          |
| bitwise OR                              | `|`                                                                                                                                                                                               | Left          |
| relational&nbsp;and&nbsp;type&nbsp;test | `>=`    `>`    `<=`    `<`    `as`    `is`    `is!`                                                                                                                                               | None          |
| equality                                | `==`    `!=`                                                                                                                                                                                      | None          |
| logical AND                             | `&&`                                                                                                                                                                                              | Left          |
| logical OR                              | `||`                                                                                                                                                                                              | Left          |
| if null                                 | `??`                                                                                                                                                                                              | Left          |
| conditional                             | <code><em>expr1</em> ? <em>expr2</em> : <em>expr3</em></code>                                                                                                                                     | Right         |
| cascade                                 | `..` &nbsp;&nbsp; `?..`                                                                                                                                                                           | Left          |
| assignment                              | `=`    `*=`    `/=`    `+=`    `-=`    `&=`    `^=`    <em>etc.</em>                                                                                                                              | Right         |
{:.table .table-striped}

{{site.alert.warning}}
  위의 표는 실용적인 가이드라인일 뿐이며,
  연산자 우선순위와 결합법칙에 대한 개념은 문법적인 사실을 기반으로 한
  근사입니다. [Dart 언어 설명서][Dart language specification]에 정의된 문법에서
  더 신뢰할 수 있는 설명을 찾을 수 있습니다.
{{site.alert.end}}

연산자를 사용할 때는 식을 만듭니다. 다음은 연산자 식의 예제입니다:

<?code-excerpt "misc/test/language_tour/operators_test.dart (expressions)" replace="/,//g"?>
```dart
a++
a + b
a = b
a == b
c ? a : b
a is T
```

## 연산자 우선순위 예제

[연산자 테이블](#연산자)에서 각 연산자는 그 뒤에 오는 행의 연산자보다
높은 우선 순위를 가집니다. 예를 들어, 곱셈 연산자 `%`는
등식 연산자 `==` 보다 높은 우선 순위를 가지고 더 먼저 실행됩니다.
그리고 `==`는 논리 AND 연산자인 `&&` 보다 높은 우선 순위를 가집니다.
이런 우선 순위는 다음 두 줄의 라인이 같은 방식으로 실행된다는 것을 의미합니다:

<?code-excerpt "misc/test/language_tour/operators_test.dart (precedence)"?>
```dart
// 괄호는 가독성을 높혀줍니다.
if ((n % i == 0) && (d % i == 0)) ...

// 가독성이 나쁘지만 위와 동일합니다.
if (n % i == 0 && d % i == 0) ...
```

{{site.alert.warning}}
  두 개의 피연산자를 가지는 연산자는, 맨 왼쪽 피연산자가 메서드를 결정합니다.
  예를 들어, `Vector` 객체와 `Point` 객체로 `aVector + aPoint`를 수행하면,
  `Vector`의 덧셈 (`+`)을 실행합니다.
{{site.alert.end}}


## 산술 연산자

Dart는 아래 표와 같이 일반적인 산술 연산자를 지원합니다.

|-----------------------------+-------------------------------------------|
| 연산자                        | 의미                                       |
|-----------------------------+-------------------------------------------|
| `+`                         | 더하기
| `-`                         | 빼기
| <code>-<em>expr</em></code> | 부정(negation)으로도 부르는 단항 빼기 (식 부호 역순)
| `*`                         | 곱하기
| `/`                         | 나누기
| `~/`                        | 정수를 반환하는 나누기
| `%`                         | 정수 나눗셈의 나머지를 반환 (modulo)
{:.table .table-striped}

예제:

<?code-excerpt "misc/test/language_tour/operators_test.dart (arithmetic)"?>
```dart
assert(2 + 3 == 5);
assert(2 - 3 == -1);
assert(2 * 3 == 6);
assert(5 / 2 == 2.5); // 결과는 double 타입
assert(5 ~/ 2 == 2); // 결과는 int 타입
assert(5 % 2 == 1); // 나머지

assert('5/2 = ${5 ~/ 2} r ${5 % 2}' == '5/2 = 2 r 1');
```

Dart는 prefix, postfix 증가 및 감소 연산자를 지원합니다.

|-----------------------------+-------------------------------------------|
| 연산자                        | 의미                                       |
|-----------------------------+-------------------------------------------|
| <code>++<em>var</em></code> | <code><em>var</em> = <em>var</em> + 1</code> (식의 값은 <code><em>var</em> + 1</code>)
| <code><em>var</em>++</code> | <code><em>var</em> = <em>var</em> + 1</code> (식의 값은 <code><em>var</em></code>)
| <code>--<em>var</em></code> | <code><em>var</em> = <em>var</em> - 1</code> (식의 값은 <code><em>var</em> - 1</code>)
| <code><em>var</em>--</code> | <code><em>var</em> = <em>var</em> - 1</code> (식의 값은 <code><em>var</em></code>)
{:.table .table-striped}

예제:

<?code-excerpt "misc/test/language_tour/operators_test.dart (increment-decrement)"?>
```dart
int a;
int b;

a = 0;
b = ++a; // b에 a의 값을 할당하기 전에 a를 증가시킵니다.
assert(a == b); // 1 == 1

a = 0;
b = a++; // b에 a의 값을 할당한 후에 a를 증가시킵니다.
assert(a != b); // 1 != 0

a = 0;
b = --a; // b에 a의 값을 할당하기 전에 a를 감소시킵니다.
assert(a == b); // -1 == -1

a = 0;
b = a--; // b에 a의 값을 할당한 후에 a를 감소시킵니다.
assert(a != b); // -1 != 0
```

## 동등, 관계 연산자

다음 표는 동등 및 관계 연산자의 뜻을 나열합니다.

|-----------+-------------------------------------------|
| 연산자      | 의미                                       |
|-----------+-------------------------------------------|
| `==`      |       동등; 아래 문단을 확인하세요
| `!=`      |       동등하지 않음
| `>`       |       큼
| `<`       |       작음
| `>=`      |       크거나 같음
| `<=`      |       작거나 같음
{:.table .table-striped}

두 객체 x와 y가 동일한 것인지 확인하고 싶다면, `==` 연산자를 사용하세요.
(두 객체가 정확하게 같은 것인지 확인하고 싶다면 [identical()][] 함수를 대신 사용하세요.)
`==` 연산자는 다음과 같이 작동합니다:

1.  *x* 또는 *y* 가 null 일 때, 모두 null이라면 true를, 둘 중에 하나만 null이라면
    false를 반환합니다.

2.  *y*를 인자로 사용하여 *x*에 대해 `==` 메서드를 호출한 결과를 반환합니다.
    (`==` 같은 연산자들은 첫 번째 피연산자에서 호출되는 메서드입니다.
    더 자세한 사항은 [연산자][Operators]를 참고하세요.)

다음은 각 동등 및 관계 연산자의 사용 예제입니다:

<?code-excerpt "misc/test/language_tour/operators_test.dart (relational)"?>
```dart
assert(2 == 2);
assert(2 != 3);
assert(3 > 2);
assert(2 < 3);
assert(3 >= 3);
assert(2 <= 3);
```


## 타입 테스트 연산자

`as`, `is`, 및 `is!` 연산자는 런타임에 타입을 간단하게 확인 할 수 있습니다.

|-----------+-------------------------------------------|
| 연산자      | 의미                                       |
|-----------+-------------------------------------------|
| `as`      | 타입캐스트 ([라이브러리 프리픽스 지정][library prefixes]할 때에도 사용)
| `is`      | 특정된 타입을 가지는 객체라면 True
| `is!`     | 특정된 타입을 가지는 객체가 아니라면 True
{:.table .table-striped}

`obj` 가 `T` 로 특정된 인터페이스의 구현체라면 `obj is T` 의 결과는 true 입니다.
예를 들어, `obj is Object?`는 항상 true 입니다.

어떤 객체가 캐스팅을 원하는 타입인 경우에만 `as` 연산자를 사용해여 캐스팅 할 수 있습니다. 예제:

<?code-excerpt "misc/lib/language_tour/classes/employee.dart (emp as Person)"?>
```dart
(employee as Person).firstName = 'Bob';
```

만약 객체가 타입 `T`라는 것을 확신하지 못한다면, 객체를 사용하기 전에 `is T`로 타입을 확인하세요.
<?code-excerpt "misc/lib/language_tour/classes/employee.dart (emp is Person)"?>
```dart
if (employee is Person) {
  // 타입 확인
  employee.firstName = 'Bob';
}
```

{{site.alert.note}}
  위의 두 코드는 같지 않습니다. 만약 `employee` 가 null이거나 `Person`이 아니라면,
  첫 번째 코드는 예외가 발생하지만, 두 번째는 그렇지 않습니다.
{{site.alert.end}}

## 할당 연산자

앞서 봤다시피, `=` 연산자를 사용해 값을 할당할 수 있습니다.
할당을 받는 변수가 null 일 때만 할당하고 싶다면, `??=` 연산자를 사용하면 됩니다.

<?code-excerpt "misc/test/language_tour/operators_test.dart (assignment)"?>
```dart
// a에 value를 할당합니다
a = value;
// b가 null이라면 value를 할당하고, 그렇지 않으면 그대로 둡니다.
b ??= value;
```

`+=` 같은 복합(compound) 할당 연산자는 할당과 연산을 결합합니다.

| `=`  | `*=`  | `%=`  | `>>>=` | `^=`
| `+=` | `/=`  | `<<=` | `&=`   | `|=`
| `-=` | `~/=` | `>>=`
{:.table}

복합 할당 연산자는 다음과 같이 작동합니다:

|-----------+----------------------+-----------------------|
|           | 복합 할당               | 동일한 식               |
|-----------+----------------------+-----------------------|
|**연산자 <em>op</em>:** | <code>a <em>op</em>= b</code> | <code>a = a <em>op</em> b</code>
|**예제:**                     |`a += b`                       | `a = a + b`
{:.table}

다음 예제에서 할당 및 복합 할당 연산자를 사용합니다:

<?code-excerpt "misc/test/language_tour/operators_test.dart (op-assign)"?>
```dart
var a = 2; // = 을 사용하여 할당
a *= 3; // 할당 및 곱셈: a = a * 3
assert(a == 6);
```


## 논리 연산자

논리 연산자를 사용하여 boolean 식을 반전하거나 결합하는 것이 가능합니다.

|-----------------------------+-------------------------------------------|
| 연산자                        | 의미                                       |
|-----------------------------+-------------------------------------------|
| <code>!<em>expr</em></code> | 뒤따르는 식을 반전합니다 (false -> true, true -> false)
| `||`                        | 논리 OR
| `&&`                        | 논리 AND
{:.table .table-striped}

다음은 논리 연산자를 사용하는 예제입니다:

<?code-excerpt "misc/lib/language_tour/operators.dart (op-logical)"?>
```dart
if (!done && (col == 0 || col == 3)) {
  // ...Do something...
}
```


## 비트 단위, 쉬프트 연산자

Dart에서는 숫자를 이루는 각각의 비트를 조작하는 것이 가능합니다.
주로 비트 단위 및 쉬프트 연산자는 정수와 함께 사용됩니다.

|-----------------------------+-------------------------------------------|
| 연산자                        | 의미                                       |
|-----------------------------+-------------------------------------------|
| `&`                         | AND
| `|`                         | OR
| `^`                         | XOR
| <code>~<em>expr</em></code> | 단항 비트 단위 보수 (0s -> 1s; 1s -> 0s)
| `<<`                        | Shift left
| `>>`                        | Shift right
| `>>>`                       | Unsigned shift right
{:.table .table-striped}

{{site.alert.note}}
  크거나 음의 피연산자에 대해 비트 단위 연산의 수행은 플랫폼 마다 다릅니다.
  더 자세한 정보는 [플랫폼 마다 다른 비트 단위 연산][Bitwise operations platform differences]
  을 참고하세요.
{{site.alert.end}}

다음은 비트 단위 및 쉬프트 연산자를 사용하는 예제입니다:

<?code-excerpt "misc/test/language_tour/operators_test.dart (op-bitwise)"?>
```dart
final value = 0x22;
final bitmask = 0x0f;

assert((value & bitmask) == 0x02); // AND
assert((value & ~bitmask) == 0x20); // AND NOT
assert((value | bitmask) == 0x2f); // OR
assert((value ^ bitmask) == 0x2d); // XOR

assert((value << 4) == 0x220); // Shift left
assert((value >> 4) == 0x02); // Shift right

// Shift right example that results in different behavior on web
// because the operand value changes when masked to 32 bits:
assert((-value >> 4) == -0x03);

assert((value >>> 4) == 0x02); // Unsigned shift right
assert((-value >>> 4) > 0); // Unsigned shift right
```

{{site.alert.version-note}}
  _Triple-shift_ 또는 _unsigned shift_ 로 불리는 `>>>` 연산자는
  적어도 2.14의 [language version][]이 필요합니다.
{{site.alert.end}}

[Bitwise operations platform differences]: /guides/language/numbers#bitwise-operations

Dart에는 [if-else][]문을 간결하게 표현 할 수 있는 두 개의 연산자가 있습니다:

<code><em>조건</em> ? <em>표현식1</em> : <em>표현식2</em></code>
: 만약 _condition_ 이 참이라면, _표현식1_ 의 값을 반환하고, 아니라면 _표현식2_ 의 값을 반환합니다.

<code><em>표현식1</em> ?? <em>표현식2</em></code>
: 만약 _표현식1_ 이 null이 아니라면, _표현식1_ 의 값을 반환하고 null이라면,
  _표현식2_ 의 값을 반환합니다.

Boolean 표현식으로 어떤 값을 할당하는 상황이라면, `?` 와 `:` 을 사용해보세요.

<?code-excerpt "misc/lib/language_tour/operators.dart (if-then-else-operator)"?>
```dart
var visibility = isPublic ? 'public' : 'private';
```

Boolean 표현식이 null인지 확인하고 싶다면, `??` 를 사용하세요.

<?code-excerpt "misc/test/language_tour/operators_test.dart (if-null)"?>
```dart
String playerName(String? name) => name ?? 'Guest';
```

위의 예는 다음과 같이 두 개의 방법으로 표현 될 수 있지만, 깔끔하진 않습니다:

<?code-excerpt "misc/test/language_tour/operators_test.dart (if-null-alt)"?>
```dart
// ?: 연산자보다 조금 긴 버젼
String playerName(String? name) => name != null ? name : 'Guest';

// if-else 문을 사용한 훨씬 긴 버젼.
String playerName(String? name) {
  if (name != null) {
    return name;
  } else {
    return 'Guest';
  }
}
```

## Cascade 노테이션

Cascades (`..`, `?..`) 는 같은 객체에 대해 연속적인 명령을 적용할 수 있게 해줍니다. 
인스턴스 멤버에 접근하거나 인스턴스 메서드 호출 또한 가능합니다.
이런 기능은 임시 변수를 만드는 과정을 줄이고 코드를 더 유동적으로 만들어 줍니다.

다음 코드를 살펴보세요:

<?code-excerpt "misc/lib/language_tour/cascades.dart (cascade)"?>
```dart
var paint = Paint()
  ..color = Colors.black
  ..strokeCap = StrokeCap.round
  ..strokeWidth = 5.0;
```

생성자 `Paint()`는 `paint` 객체를 반환합니다.
Casade 노테이션 뒤에 오는 코드들은 반환 될 값들을 무시하며,
해당 객체에 대해 작동합니다.

위의 코드는 아래의 코드와 동일합니다:

<?code-excerpt "misc/lib/language_tour/cascades.dart (cascade-expanded)"?>
```dart
var paint = Paint();
paint.color = Colors.black;
paint.strokeCap = StrokeCap.round;
paint.strokeWidth = 5.0;
```

만약 cascade를 사용하려는 객체의 필드가 null 일 수도 있다면,
_null-shorting_ cascade (?..)를 첫 번째 오퍼레이션으로 사용하세요.
`?..` 으로 시작하는 cascade는 뒤에 오는 cascade 오퍼레이션이
null 객체 일 수도 있는 객체에 대해 실행되지 않을 것을 보장합니다.

<?code-excerpt "misc/test/language_tour/browser_test.dart (cascade-operator)"?>
```dart
querySelector('#confirm') // 객체 찾기.
  ?..text = 'Confirm' // 객체의 멤버 사용.
  ..classes.add('important')
  ..onClick.listen((e) => window.alert('Confirmed!'))
  ..scrollIntoView();
```

{{site.alert.version-note}}
  `?..` 문법은 적어도 2.12의 [language version][]이 필요합니다.
{{site.alert.end}}

위의 코드는 다음과 동일합니다:

<?code-excerpt "misc/test/language_tour/browser_test.dart (cascade-operator-example-expanded)"?>
```dart
var button = querySelector('#confirm');
button?.text = 'Confirm';
button?.classes.add('important');
button?.onClick.listen((e) => window.alert('Confirmed!'));
button?.scrollIntoView();
```

다음과 같이 중첩 cascade도 가능합니다:

<?code-excerpt "misc/lib/language_tour/operators.dart (nested-cascades)"?>
```dart
final addressBook = (AddressBookBuilder()
      ..name = 'jenny'
      ..email = 'jenny@example.com'
      ..phone = (PhoneNumberBuilder()
            ..number = '415-555-0100'
            ..label = 'home')
          .build())
    .build();
```

실제 객체를 반환하는 함수에 대해서만 cascade를 사용해야 합니다.
다음과 같은 코드는 에러가 발생합니다:

<?code-excerpt "misc/lib/language_tour/operators.dart (cannot-cascade-on-void)" plaster="none"?>
```dart
var sb = StringBuffer();
sb.write('foo')
  ..write('bar'); // 에러: 메서드 'write'는 'void'에 정의되어 있지 않습니다.
```

The `sb.write()` 호출은 void를 반환하고,
`void`에는 cascade를 사용 할 수 없습니다.

{{site.alert.note}}
  엄밀히 말하자면, cascades를 위한 “double dot” 표기는 연산자가 아닙니다.
  이것은 그저 Dart 문법의 일부입니다.
{{site.alert.end}}

## 다른 연산자들

아마 다른 예제에서 아래의 연산자들을 본 경험이 있을 것 입니다:

|----------+------------------------------+--------------------|
| Operator     | 이름                          | 의미                |
|----------+------------------------------+--------------------|
| `()`     | Function application         | 함수 호출을 나타냅니다.
| `[]`     | Subscript access             | 재정의 할 수 있는 연산자 `[]`의 호출을 나타냅니다; 예제: `fooList[1]`는 `fooList`의 인덱스 `1`에 위치한 요소에 접근하기 위해 정수 `1`을 전달합니다.
| `?[]`    | Conditional subscript access | `[]`와 비슷하지만 맨 왼쪽 피연산자가 null일 수 있습니다; 예제: `fooList[1]`는 `fooList`가 null이 아니라면, `fooList`의 인덱스 `1`에 위치한 요소에 접근하기 위해 정수 `1`을 전달합니다. (만약 null이라면 표현식은 null로 평가됩니다.)
| `.`      | Member access                | 표현식의 프로퍼티를 참조합니다; 예제: `foo.bar`는 표현식 `foo`의 프로퍼티 `bar`를 선택합니다.
| `?.`     | Conditional member access    | `.`와 비슷하지만, 맨 왼쪽 피연산자가 null일 수 있습니다; 예제: `foo?.bar`는 `foo`가 null이 아니라면 표현식 `foo`의 프로퍼티 `bar`를 선택합니다. (만약 null이라면 `foo?.bar`의 값은 null입니다.)
| `!`      | Null assertion operator      | 표현식을 내제된 non-nullable 타입으로 캐스트합니다. 만약 캐스트에 실패하면 런타임 예외를 발생시킵니다; 예제: `foo!.bar`는 `foo`가 null이 아닌지 assert 하고, `foo`가 null이 아니라면 프로퍼티 `bar`를 선택합니다. (만약 null이라면 런타임 예외를 발생시킵니다.)
{:.table .table-striped}

`.`, `?.`, 그리고 `..` 연산자에 대한 더 많은 정보는,
[클래스][Classes]를 참고하세요.


[operators as class members]: /language/methods#연산자
[Dart language specification]: /guides/language/spec
[identical()]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/identical.html
[Operators]: /language/methods#연산자
[library prefixes]: /language/libraries#specifying-a-library-prefix
[if-else]: /language/branches#if
[language version]: /guides/language/evolution#language-versioning
[Classes]: /language/classes
