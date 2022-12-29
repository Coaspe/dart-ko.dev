---
title: Dart로 떠나는 여행
description: Dart 언어의 중요한 기능에 대해 학습합니다.
short-title: Language tour
js: [{url: 'https://dartpad.dev/inject_embed.dart.js', defer: true}]
---
<?code-excerpt replace="/ *\/\/\s+ignore_for_file:[^\n]+\n//g; /(^|\n) *\/\/\s+ignore:[^\n]+\n/$1/g; /(\n[^\n]+) *\/\/\s+ignore:[^\n]+\n/$1\n/g; / *\/\/\s+ignore:[^\n]+//g; /([A-Z]\w*)\d\b/$1/g"?>

이 페이지에서는 다른 프로그래밍 언어를 사용 할 줄 안다는 가정 하에 
변수, 연산자, 클래스 및 라이브러리에 이르는 각 주요 Dart 기능을 사용하는 방법을 알려드립니다. 
언어에 대한 간략한 설명을 보고 싶다면, [샘플 페이지](/samples)를 참고하세요!

Dart의 핵심 라이브러리에 대해 학습하고 싶다면 [library tour](/guides/libraries/library-tour)를 참고하세요. 
언어의 기능에 대한 자세한 정보를 얻고 싶다면 [Dart 언어 설명서][]을 참고하세요.

{{site.alert.note}}
  DartPad를 사용하여 Dart 언어를 체험해볼 수 있습니다.
  ([DartPad란?](/tools/dartpad)).
  **<a href="{{site.dartpad}}" target="_blank" rel="noopener">
  DartPad 열기</a>**

  위의 페이지에서 Dart의 활용 예제들을 보여주기 위해 임베디드 DartPads를 사용합니다. 
  {% include dartpads-embedded-troubleshooting.md %}
{{site.alert.end}}

{% comment %}
[TODO #2950: Look for null, ?, !, late, optional, Map, List, Set.
(Anything else?)
Look for dynamic. Look for code that isn't auto-included (no code-excerpt.)]
{% endcomment %}

## 기본적인 Dart 프로그램

아래 코드는 Dart의 가장 기본적인 기능들을 사용합니다.

<?code-excerpt "misc/test/language_tour/basic_test.dart"?>
```dart
// 함수 정의.
void printInteger(int aNumber) {
  print('The number is $aNumber.'); // 콘솔에 프린트.
}

// 앱의 실행을 시작하는 부분.
void main() {
  var number = 42; // 변수의 선언과 초기화.
  printInteger(number); // 함수의 호출.
}
```

아래는 이 프로그램이 사용한 모든 (혹은 거의 모든) Dart 앱에 적용할 수 있는 
요소들입니다:

<code>// <em>이건 주석입니다.</em> </code>
:   단일행 주석입니다.
    Dart에서는 다중행 그리고 문서 주석도 지원합니다.
    주석에 대해 더 자세히 알고 싶다면, [주석](#comments)을 참고하세요.

`void`
:   사용하지 않을 값을 나타내는 특수한 타입입니다.
    `printInteger()` and `main()` 같이 명시적으로 값을 반환하지 않는 함수들은 
    `void` 반환 타입을 가집니다.
    
`int`
:   정수를 나타내는 또 다른 타입입니다.
    추가적인 [내장 타입](#built-in-types)으로
    `String`, `List`, 및 `bool`이 있습니다.

`42`
:   숫자 리터럴 입니다. 숫자 리터럴은 컴파일 타임 상수입니다.

`print()`
:   산출물을 출력하는 간단한 방법입니다.

`'...'` (또는 `"..."`)
:   문자열 리터럴입니다.

<code>$<em>변수이름</em></code> (또는 <code>${<em>표현식</em>}</code>)
:   문자열 보간(String interpolation): 문자열 리터럴 내부의 변수 또는 표현식의 문자열과
    동일한 값을 포함합니다. 더 자세한 정보를 원한다면,
    [Strings](#strings)을 참고하세요.

`main()`
:   앱의 실행이 시작되는, 특수하고 필수적인 최상위 함수입니다.
    더 자세한 정보를 원한다면,
    [main() 함수](#the-main-function)를 참고하세요.

`var`
:   타입을 특정하지 않고 변수를 선언하는 방법입니다.
    초기 값 (`42`)으로 의해 이 변수의 타입은 (`int`)로 결정됩니다.


{{site.alert.note}}
  이 사이트의 코드는 [Dart 스타일 가이드](/guides/language/effective-dart/style)의
  관습을 따릅니다.
{{site.alert.end}}


## 주요 컨셉

Dart 언어를 학습 할 때 다음을 잘 기억해야합니다:

-   변수로 할당 할 수 있는 모든 것은 *객체*이고, 모든 객체는 *클래스*의 instance 입니다.
    숫자, 함수 그리고 `null`까지 모두 객체입니다.
    `null`을 제외하고 (만약 [sound null safety][ns])를 활성화했다면),
     모든 객체들은 [`Object`][] 클래스를 상속받습니다.

    {{site.alert.version-note}}
      [Null safety][ns]는 in Dart 2.12에 처음 소개되었습니다..
      null safety를 사용하기 위해서 최소 2.12의 [language version][]이 필요합니다.
    {{site.alert.end}}

-   Dart는 타입에 엄격하지만, 추론할 수 있기 때문에 타입 어노테이션은 자율에 맡깁니다.
    위의 코드에서 `number`는 `int` 타입으로 추론됩니다.

-   [Null safety][ns]를 활성화했다면,
    어떤 변수가 null 값을 갖는 것을 허락하지 않았을 때 
    그 변수가 null 값을 가질 수 없게 할 수 있습니다.
    타입의 끝에 물음표 (`?`)를 추가하면 해당 변수를
    nullable로 만들 수 있습니다.
    예를 들어, `int?` 타입의 변수는 정수 또는 `null` 값을 가집니다.
    만약 표현식이 null로 평가되지 않지만 Dart가 이에 동의하지 않는다는 것을
    *알고* 있다면, `!`을 추가하여 null이 아니라고 주장(assert) 할 수 있습니다
    (만약 null이라면 예외를 throw 합니다).
    예시: `int x = nullableButNotNullInt!`

-   만약에 어떤 타입이든 적용이 가능하다고 명시하고 싶다면, 
    `Object?` ([null safety][ns-enable]를 활성화했다면)
    또는 `Object`를 타입으로 설정해주면 된다.
    먄약 런타임까지 타입 체킹을 미뤄야 한다면,
    [특수 타입인 `dynamic`][ObjectVsDynamic]를 사용하세요.

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
    더 자세한 정보를 원한다면, [Libraries and visibility](#libraries-and-visibility)
    를 참고하세요.

-   *식별자*는 문자 또는 언더 스코어 (`_`)로 시작 할 수 있고,
    어떠한 문자와 숫자의 결합이 가능합니다.

-   Dart는 런타임 값을 가지는 *식 (expression)*과
    그렇지 않은 *문 (statement)*를 가지고 있습니다.
    예를 들어, [조건식 (conditional expression)](#conditional-expressions)인
    `condition ? expr1 : expr2` 은 `expr1` 또는 `expr2`의 값을 가집니다.
    위의 expression과 값을 가지지 않는 
    [if-else 문](#if-and-else)를 비교해봅시다.
    문은 때로 하나 혹은 그 이상의 식을 포함하지만,
    식은 직접적으로 문을 포함할 수 없습니다.

-   Dart tools는 두가지 문제를 리포트 해줍니다: _경고_ 그리고 _에러_.
    경고는 코드가 제대로 작동하지 않을 수도 있다는 것을 의미하지만,
    프로그램의 실행을 막지 않습니다. 에러는 컴파일 타임 또는 런타임으로 구분됩니다.
    컴파일 타임 에러는 코드가 실행되는 것을 막습니다;
    런타임 에러는 코드가 실행되는 동안 [예외](#exceptions)를 발생시킵니다.


## 키워드

다음 테이블 리스트는 Dart 언어가 특별히 관리하는 단어들입니다.

{% assign ckw = '&nbsp;<sup title="contextual keyword" alt="contextual keyword">1</sup>' %}
{% assign bii = '&nbsp;<sup title="built-in-identifier" alt="built-in-identifier">2</sup>' %}
{% assign lrw = '&nbsp;<sup title="limited reserved word" alt="limited reserved word">3</sup>' %}
<div class="table-wrapper" markdown="1">
| [abstract][]{{bii}}   | [else][]              | [import][]{{bii}}     | [show][]{{ckw}}   |
| [as][]{{bii}}         | [enum][]              | [in][]                | [static][]{{bii}} |
| [assert][]            | [export][]{{bii}}     | [interface][]{{bii}}  | [super][]         |
| [async][]{{ckw}}      | [extends][]           | [is][]                | [switch][]        |
| [await][]{{lrw}}      | [extension][]{{bii}}  | [late][]{{bii}}       | [sync][]{{ckw}}   |
| [break][]             | [external][]{{bii}}   | [library][]{{bii}}    | [this][]          |
| [case][]              | [factory][]{{bii}}    | [mixin][]{{bii}}      | [throw][]         |
| [catch][]             | [false][]             | [new][]               | [true][]          |
| [class][]             | [final][]             | [null][]              | [try][]           |
| [const][]             | [finally][]           | [on][]{{ckw}}         | [typedef][]{{bii}}|
| [continue][]          | [for][]               | [operator][]{{bii}}   | [var][]           |
| [covariant][]{{bii}}  | [Function][]{{bii}}   | [part][]{{bii}}       | [void][]          |
| [default][]           | [get][]{{bii}}        | [required][]{{bii}}   | [while][]         |
| [deferred][]{{bii}}   | [hide][]{{ckw}}       | [rethrow][]           | [with][]          |
| [do][]                | [if][]                | [return][]            | [yield][]{{lrw}}  |
| [dynamic][]{{bii}}    | [implements][]{{bii}} | [set][]{{bii}}        |                   |
{:.table .table-striped .nowrap}
</div>

[abstract]: #abstract-classes
[as]: #type-test-operators
[assert]: #assert
[async]: #asynchrony-support
[await]: #asynchrony-support
[break]: #break-and-continue
[case]: #switch-and-case
[catch]: #catch
[class]: #instance-variables
[const]: #final-and-const
{% comment %}
  [TODO #2950: Make sure that points to a place that talks about const constructors,
  as well as const literals and variables.]
{% endcomment %}
[continue]: #break-and-continue
[covariant]: /guides/language/sound-problems#the-covariant-keyword
[default]: #switch-and-case
[deferred]: #lazily-loading-a-library
[do]: #while-and-do-while
[dynamic]: #important-concepts
[else]: #if-and-else
[enum]: #enumerated-types
[export]: /guides/libraries/create-library-packages
[extends]: #extending-a-class
[extension]: #extension-methods
[external]: https://spec.dart.dev/DartLangSpecDraft.pdf#External%20Functions
[factory]: #factory-constructors
[false]: #booleans
[final]: #final-and-const
[finally]: #finally
[for]: #for-loops
[Function]: #functions
[get]: #getters-and-setters
[hide]: #importing-only-part-of-a-library
[if]: #if-and-else
[implements]: #implicit-interfaces
[import]: #using-libraries
[in]: #for-loops
[interface]: #implicit-interfaces
[is]: #type-test-operators
[late]: #late-variables
[library]: #libraries-and-visibility
[mixin]: #adding-features-to-a-class-mixins
[new]: #using-constructors
[null]: #default-value
[on]: #catch
[operator]: #_operators
[part]: /guides/libraries/create-library-packages#organizing-a-library-package
[required]: #named-parameters
[rethrow]: #catch
[return]: #functions
[set]: #getters-and-setters
[show]: #importing-only-part-of-a-library
[static]: #class-variables-and-methods
[super]: #extending-a-class
[switch]: #switch-and-case
[sync]: #generators
[this]: #constructors
[throw]: #throw
[true]: #booleans
[try]: #catch
[typedef]: #typedefs
[var]: #variables
[void]: #built-in-types
{% comment %}
  TODO #2950: Add coverage of void to the language tour.
{% endcomment %}
[with]: #adding-features-to-a-class-mixins
[while]: #while-and-do-while
[yield]: #generators

이 단어들을 식별자로 사용하는 것을 지양하세요.
그러나, 만약 필요하다면, 윗첨자로 표시된 단어들은 식별자로 사용이 가능합니다.

* **1**로 표시된 단어들은 **맥락적인 키워드(contextual keywords)**로
  득정한 장소에서만 의미를 가집니다.
  어디서든 유효한 식별자로 하용이 가능합니다..

* **2**로 표시된 단어들은 **내장 식별자(built-in identifiers)**로
  이 키워드들은 거의 모든 곳에서 식별자로 사용이 가능하지만,
  클래스나 타입의 이름, import prefix로 사용은 불가능합니다.

* **3**으로 표시된 단어들은 [비동기 지원](#asynchrony-support)과
  관련된 제한된 단어들 입니다. `await` 또는 `yield`를
  `async`, `async*`, or `sync*`로 표시된 함수의 바디에서
  식별자로 사용 할 수 없습니다.

표의 나머지 단어들은 모두  **예약된 단어(reserved words)**들로,
식별자로 사용이 불가능 합니다.


## 변수

아래는 변수를 생성하고 초기화하는 예제입니다:

<?code-excerpt "misc/lib/language_tour/variables.dart (var-decl)"?>
```dart
var name = 'Bob';
```

변수는 레퍼런스(reference)를 저장합니다. `name` 이라는 변수는 "Bob"이라는 값을
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


### 디폴트 값

Nullable 타입을 가지는 초기화되지 않은 변수는
초기 값으로 `null`을 가질 수 있습니다.
([Null safety][ns]을 사용하지 않는다면,
모든 변수는 nullable 타입을 가집니다.)
Dart의 다른 모든 것과 마찬가지로 숫자도 객체이기 때문에,
숫자 타입의 변수도 처음에는 null 입니다.

<?code-excerpt "misc/test/language_tour/variables_test.dart (var-null-init)"?>
```dart
int? lineCount;
assert(lineCount == null);
```

{{site.alert.note}}
  프로덕션 코드는 `assert()` 호출을 무시합니다. 반면에 개발하는 동안에는, 
  _조건_이 false라면 <code>assert(<em>조건</em>)</code> 가 예외를
  throw 합니다. 더 자세한 것은, [Assert](#assert)를 참고하세요.
{{site.alert.end}}

Null safety를 활성화 했다면, non-nullable 변수를 사용하기 전에
값들을 초기화해야만합니다:

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


### Late 변수

Dart 2.12에서 `late` 수식어가 추가되었습니다. 두 가지 사용례가 있습니다:

* 선언 이후에 초기화되는 non-nullable 변수를 선언하는 것
* 변수를 초기화를 지연하는 것

보통 Dart의 제어 흐름 분석기는 non-nullable 변수가
non-null 값으로 설정되어 있는지 사용하기 전에 알아챌 수 있지만,
가끔 실패할 때도 있습니다.
가장 흔히 사용되는 두가지 케이스는 최상위 변수와 인스턴스 변수입니다:
Dart는 종종 그 변수들이 설정되었는지 판단할 수 없기 때문에,
시도하지 않습니다.

변숫값의 설정이 사용 전에 보장되지만,
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
변수가 처음 사용될 때 initializer가 실행됩니다.
지연 초기화는 다음과 같은 상황에 유용합니다:

* 변수가 당장 필요하진 않지만,
  초기화 비용이 비쌀 때.
* 인스턴스 변수를 초기화에 initializer가
  `this`에 대한 접근이 필요할 때.

다음 예제에서,
`temperature` 변수가 사용되지 않으면,
비싼 함수인 `readThermometer()`가 호출되지 않습니다:

<?code-excerpt "misc/lib/language_tour/variables.dart (var-late-lazy)" replace="/late/[!$&!]/g"?>
```dart
// 이 프로그램에서 readThermometer()에 대한 유일한 호출입니다.
[!late!] String temperature = readThermometer(); // 지연 초기화.
```


### Final, const

변수를 변경할 생각이 없다면, `var` 대신 `final`이나 `const`를 사용하거나,
지정한 타입에 추가하여 사용하세요.
final 변수는 오직 한 번만 설정될 수 있습니다; const 변수는 컴파일 타임 상수입니다.
(const 변수는 내부적으로 final입니다.)

{{site.alert.note}}
  [인스턴스 변수](#instance-variables)는 `final`로 설정될 수 있지만, `const`는 될 수 없습니다.
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
const bar = 1000000; // 압력의 단위(dynes/cm2)
const double atm = 1.01325 * bar; // 표준 대기
```

`const` 키워드는 상수 변수를 선언할 때만 쓰이는 것이 아닙니다.
상수 _값_을 만드는 데 사용할 수 있을 뿐만 아니라,
상수 값을 _만드는_ 생성자를 선언할 수도 있습니다.
모든 변수는 상수 값을 가질 수 있습니다.

<?code-excerpt "misc/lib/language_tour/variables.dart (const-vs-final)"?>
```dart
var foo = const [];
final bar = const [];
const baz = []; // `const []`와 동일
```

위의 `baz`처럼, `const` 선언의 초기화 식에 `const`를 생략해도 됩니다.
더 자세히 알고 싶다면, [const를 중복으로 사용하지 마십시오][]를 참고하세요.

이전에 `const` 값을 가지고 있었더라도,
non-final, non-const 변수의 값을 변경할 수 있습니다.

<?code-excerpt "misc/lib/language_tour/variables.dart (reassign-to-non-final)"?>
```dart
foo = [1, 2, 3]; // const [] 였음
```

You can't change the value of a `const` variable:

{:.fails-sa}
<?code-excerpt "misc/lib/language_tour/variables.dart (cant-assign-to-const)"?>
```dart
baz = [42]; // 에러: 상수 변수는 값이 할당될 수 없습니다.
```

[타입 체크와 캐스트](#type-test-operators) (`is` 그리고 `as`),
[컬렉션 `if`](#collection-operators),
그리고 [전개 연산자(spread operator)](#spread-operator) (`...` 그리고 `...?`)를
사용하는 상수 정의가 가능합니다.:

<?code-excerpt "misc/lib/language_tour/variables.dart (const-dart-25)"?>
```dart
const Object i = 3; // i는 정수 값을 가지는 const Object입니다.
const list = [i as int]; // 타입 캐스트를 사용하세요.
const map = {if (i is int) i: 'int'}; // is와 컬렉션 if를 사용하세요.
const set = {if (list is List<int>) ...list}; // ...를 사용하여 전개.
```

{{site.alert.note}}
  `final` 객체는 수정될 수 없지만,
  객체의 필드는 수정이 가능합니다.
  반면에, `const` 객체와 객체의 필드는 _불변_하기 때문에
  변경할 수 없습니다.
{{site.alert.end}}

상수 값을 만들기 위해 `const`를 사용하는 방법에 대한 더 자세한 내용은 
[Lists](#lists), [Maps](#maps), and [Classes](#classes)을 참고하세요.


## 내장 타입

Dart 언어는 다음과 같은 특수한 내장 타입을 지원합니다:

- [Numbers](#numbers) (`int`, `double`)
- [Strings](#strings) (`String`)
- [Booleans](#booleans) (`bool`)
- [Lists](#lists) (`List`, *arrays*로도 부릅니다.)
- [Sets](#sets) (`Set`)
- [Maps](#maps) (`Map`)
- [Runes](#characters) (`Runes`; 때로 `characters` API로 대체됩니다.)
- [Symbols](#symbols) (`Symbol`)
- `null` (`Null`)

이런 타입들은 리터럴을 사용하여 객체를 생성할 수 있습니다.
예를 들어, `'this is a string'`은 문자열 리터럴이고,
`true`는 boolean 리터럴입니다.

Dart의 모든 변수는 객체 (*클래스*의 인스턴스)이기 때문에,
변수를 초기화 할 때 *생성자*를 사용할 수 있습니다.
몇몇 내장 타입은 자신만의 생성자를 가지고 있습니다.
예를 들어, `Map()` 생성자를 사용하여 map을 생성할 수 있습니다.

Dart 언어의 일부 타입들은 특수한 역할을 수행합니다:

* `Object`: `Null`을 제외한 모든 Dart 클래스의 superclass.
* `Enum`: 모든 eunm의 superclass.
* `Future`, `Stream`: [비동기 지원](#asynchrony-support)에서 사용됩니다.
* `Iterable`: [for-in 루프][iteration] 그리고
  동기식 [제네레이터 함수](#generator)에서 사용됩니다.
* `Never`: 식(expression)의 평가(evaluating)를 완료할 수 없음을 나타냅니다.
  항상 예외를 throw하는 함수에서 보통 사용됩니다.
* `dynamic`: 정적 타입 체킹의 비활성화를 의미합니다.
  대개 `Object` 또는 `Object?`를 대신 사용하세요.
* `void`: 값이 사용되지 않는다는 것을 의미합니다.
  보통 반환 타입으로 사용됩니다.

{% comment %}
[TODO: move/add for-in coverage to language tour?]
{% endcomment %}

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


### Numbers

Dart의 숫자는 두 가지 유형이 있습니다:

[`int`][]

:   [사용하는 플랫폼에 따라서][dart-numbers]
    정수 값은 64비트 이하로 표현됩니다.
    네이티브 플랫폼에서는 -2<sup>63</sup> ~ 2<sup>63</sup> - 1
    까지 표현됩니다.
    웹에서는, Javascript numbers (가수부가 없는 64-bits 부동소수점 표현)
    -253 ~ 253 - 1 사이의 수로 표현됩니다.

[`double`][]

:   IEEE 754 standard를 따라 64-bit (배정도) 부동 소수점 표현을 사용합니다.

`int` 와 `double`는 모두 [`num`][]의 서브타입입니다.
num 타입은 +, -, /, * 같은 기본적인 연산자 사용이 가능하고, `abs()`,` ceil()`,
그리고 `floor()` 같은 함수의 사용도 가능합니다.
(\>\> 같은 Bitwise 연산자는 `int` 클래스에 정의되어 있습니다.)
만일 찾고있는 것이 num과 num의 서브타입이 가지고 있지 않다면, [dart:math][]
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

정수 리터럴은 필요하다면, 자동으로 double 변환됩니다:

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (int-to-double)"?>
```dart
double z = 1; // double z = 1.0 와 동일합니다.
```

다음은 예제에서 문자열을 숫자로 그리고 그 반대의 변환도 수행합니다.

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
전통 비트 단위 쉬프트 (`<<`, `>>`, `>>>`),
보수 (`~`), AND (`&`), OR (`|`), 그리고 XOR (`^`) 연산자를 지원합니다.
예제:

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (bit-shifting)"?>
```dart
assert((3 << 1) == 6); // 0011 << 1 == 0110
assert((3 | 4) == 7); // 0011 | 0100 == 0111
assert((3 & 4) == 0); // 0011 & 0100 == 0000
```

더 많은 예제를 보고 싶다면,
[비트 단위 및 쉬프트 연산자](#bitwise-and-shift-operators) 섹션을 참고하세요.

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

[dart-numbers]: /guides/language/numbers


### Strings

Dart의 문자열 (`String` 객체)는 UTRF-16 코드 유닛의 시퀀스를 홀드합니다.
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

작은 따옴표 또는 큰 따옴표로 Triple quote를 형성해
멀티 라인 문자열을 만들 수 있습니다.

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (triple-quotes)"?>
```dart
var s1 = '''
You can create
multi-line strings like this one.
''';

var s2 = """This is also a
multi-line string.""";
```

`r`을 사용하여 "로우" (raw) 문자열을 생성할 수 있습니다:

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (raw-strings)"?>
```dart
var s = r'In a raw string, not even \n gets special treatment.';
```

유니코드 문자로 문자열을 표현하는 방법에 대해 자세히 알고 싶다면,
[Runes and grapheme clusters](#characters)을 참고하세요.

Null, 숫자, 문자열 또는 boolean 값을 평가하는
보간된 표현식 (interpolated expression)이
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


### Booleans

Dart는 Boolean형 타입을 `bool`로 명명하였습니다. 
컴파일 타임 상수인 Boolean 리터럴:
`true`와 `false`가 유일한 bool 타입 객체입니다.

Dart의 type safety는
<code>if (<em>nonbooleanValue</em>)</code> 또는
<code>assert (<em>nonbooleanValue</em>)</code>
같은 코드를 사용할 수 없다는 것을 의미합니다.
대신, 다음과 같이 명시적으로 값을 확인해야합니다.

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (no-truthy)"?>
```dart
// 빈 문자열인지 확인합니다.
var fullName = '';
assert(fullName.isEmpty);

// 0인지 확인합니다.
var hitPoints = 0;
assert(hitPoints <= 0);

// null인지 확인합니다.
var unicorn;
assert(unicorn == null);

// NaN인지 확인합니다.
var iMeantToDoThis = 0 / 0;
assert(iMeantToDoThis.isNaN);
```


### Lists

아마 모든 프로그래밍 언어에서 가장 흔한 컬렉션은 *배열*이나 정렬된 객체의 그룹일 겁니다.
Dart에서 배열은 [`List`][] 객체로 존재하며, 대부분의 사람들이 *list*라고 부릅니다.

Dart list 리터럴은
쉼표로 구분된 식 또는 값 목록으로 표시되며,
대괄호('[]')로 둘러싸여 있습니다.
다음은 간단한 Dart list 입니다:

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (list-literal)"?>
```dart
var list = [1, 2, 3];
```

{{site.alert.note}}
  Dart는 위의 `list`를 `List<int>` 타입이라고 추정합니다. 정수가 아닌 객체를
  이 list에 추가를 시도하면, analyzer나 런타임이 에러를 발생시킵니다.
  더 많은 정보를 원한다면,
  [타입 추론](/guides/language/type-system#type-inference)
  을 참고하세요.
{{site.alert.end}}

<a name="trailing-comma"></a>
Dart 컬렉션 리터럴의 마지막 아이템 뒤에 쉼표를 추가할 수 있습니다.
_trailing comma_ 는 컬렉션에 영향을 미치진 않지만,
복사-붙여넣기 에러 예방을 도와줍니다.

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (trailing-commas)"?>
```dart
var list = [
  'Car',
  'Boat',
  'Plane',
];
```

리스트는 0 부터 시작하는 제로 베이스 인덱싱을 사용하고,
`list.length - 1`가 list의 마지막 인덱스입니다.
`.length` 프로퍼티를 사용하여 list의 길이를 구할 수 있고,
서브스크립트 연산자 (`[]`)를 사용하여 list의 값에 접근할 수 있습니다:

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (list-indexing)"?>
```dart
var list = [1, 2, 3];
assert(list.length == 3);
assert(list[1] == 2);

list[1] = 1;
assert(list[1] == 1);
```

컴파일 타임 상수인 리스트를 생성하고 싶다면,
list 리터럴 앞에 `const`를 추가하세요:

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (const-list)"?>
```dart
var constantList = const [1, 2, 3];
// constantList[1] = 1; // 이 라인은 에러를 발생시킵니다.
```

<a id="spread-operator"> </a>

Dart는 컬렉션에 여러 값들을 간편하게 삽입해주는
**전개 연산자** (`...`)와 **null-aware 전개 연산자**
를 지원합니다.

예를들면 list의 모든 값들을 다른 list에 삽입하기 위해
전개 연산자(...) 를 사용할 수 있습니다.

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (list-spread)"?>
```dart
var list = [1, 2, 3];
var list2 = [0, ...list];
assert(list2.length == 4);
```

전개 연산자의 오른편 표현식의 값이 null 일 수 있다면,
null-aware 전개 연산자 (`...?`)를 사용하여 예외를 피할 수 있습니다:

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (list-null-spread)"?>
```dart
var list2 = [0, ...?list];
assert(list2.length == 1);
```

더 많은 전개 연산자 예제와 정보를 원한다면,
[전개 연산자 제안서][spread proposal]을 참고하세요.

<a id="collection-operators"> </a>
Dart는 조건 (`if`)과 반복 (`for`)을 사용하여
컬렉션을 빌드할 수 있는 **컬렉션 if** 와 **컬렉션 for**
을 제공합니다.

다음은 **컬렉션 if**를 사용하여 3개 또는 4개의 항목이 있는 리스트를 생성한는 예제입니다:

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (list-if)"?>
```dart
var nav = ['Home', 'Furniture', 'Plants', if (promoActive) 'Outlet'];
```

다음은 **컬렉션 for**을 사용하여 list 항목을
다른 목록에 추가하기 전에 해당 항목을 조작하는 예제입니다:

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (list-for)"?>
```dart
var listOfInts = [1, 2, 3];
var listOfStrings = ['#0', for (var i in listOfInts) '#$i'];
assert(listOfStrings[1] == '#1');
```

컬렉션 `if` 와 `for`에 대한 더 자세한 정보와 예제를 원한다면,
[control flow collections proposal][collections proposal]을 참고하세요.

[collections proposal]: https://github.com/dart-lang/language/blob/master/accepted/2.3/control-flow-collections/feature-specification.md

[spread proposal]: https://github.com/dart-lang/language/blob/master/accepted/2.3/spread-collections/feature-specification.md

List 타입은 리스트를 조작하는 다양하고 간편한 메서드들을 가지고 있습니다.
리스트에 대한 더 많은 정보를 원한다면,
[제네릭](#generics) 그리고
[컬렉션](/guides/libraries/library-tour#collections)을 참고하세요.


### Sets

Dart의 set은 유니크한 항목들로 이루어진 정렬되지 않은 컬렉션입니다.
Dart는 set 리터럴과 [`Set`][] 타입을 지원합니다.

다음은 set 리터럴을 사용하여 Dart의 set을 생성하는 코드입니다:

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (set-literal)"?>
```dart
var halogens = {'fluorine', 'chlorine', 'bromine', 'iodine', 'astatine'};
```

{{site.alert.note}}
  Dart는 위의 `halogens`을 `Set<String>` 타입으로 추정합니다. 알맞지 않는 타입을
  set에 추가하면, analyzer 또는 런타임이 에러를 발생시킵니다.
  더 많은 정보를 원한다면,
  [타입 추론](/guides/language/type-system#type-inference)을
  참고하세요.
{{site.alert.end}}

빈 set을 생성하고 싶다면, 타입 인자 앞에 `{}`을 사용하거나,
`Set` 타입의 변수에 `{}`을 할당하세요:

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (set-vs-map)"?>
```dart
var names = <String>{};
// Set<String> names = {}; // 이 코드도 작동합니다.
// var names = {}; // Set이 아닌 map을 생성합니다.
```

{{site.alert.info}}
  **Set 또는 map?** Map 리터럴 문법은 set 리터럴 문법과 유사합니다.
  Map 리터럴을 우선으로 고려하기 때문에, `{}`의 디폴트는 `Map` 타입입니다.
  만약 `{}` 또는 이것이 할당될 변수에 타입을 명시하는 것을 잊었다면,
  Dart는 `Map<dynamic, dynamic>` 타입의 객체를 생성합니다. 
{{site.alert.end}}

`add()` 또는 `addAll()` 메서드를 사용해 존재하는 set에 항목을 추가하세요:

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (set-add-items)"?>
```dart
var elements = <String>{};
elements.add('fluorine');
elements.addAll(halogens);
```

Set에 있는 항목들의 수를 알고 싶다면, `.length`를 사용하세요:

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (set-length)"?>
```dart
var elements = <String>{};
elements.add('fluorine');
elements.addAll(halogens);
assert(elements.length == 5);
```

컴파일 타임 상수인 set을 생성하고 싶다면,
set 리터럴 앞에 `const`를 추가하세요:

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (const-set)"?>
```dart
final constantSet = const {
  'fluorine',
  'chlorine',
  'bromine',
  'iodine',
  'astatine',
};
// constantSet.add('helium'); // 이 라인은 에러를 발생시킵니다.
```

Set은 list 처럼 전개 연산자 (`...` 그리고 `...?`)와
컬렉션 `if` and `for`을 지원합니다.
더 많은 정보를 원한다면,
[list 전개 연산자](#spread-operator) 그리고
[list 컬렉션 연산자](#collection-operators)를 참고하세요.

Set에 대한 더 많은 정보를 원한다면,
[제네릭](#generics) 과
[Sets](/guides/libraries/library-tour#sets)을 참고하세요.

### Maps

일반적으로, map은 key와 value로 구성된 객체입니다.
key와 value 모두 어떤 타입의 객체든 할당이 가능합니다.
각 *key*들은 유일하지만 *value*는 중복될 수 있습니다.
Dart는 map 리터럴과 [`Map`][] 타입으로 map을 지원합니다.

다음은 map 리터럴을 사용하여 Dart의 map을 생성하는 코드입니다.

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (map-literal)"?>
```dart
var gifts = {
  // Key:    Value
  'first': 'partridge',
  'second': 'turtledoves',
  'fifth': 'golden rings'
};

var nobleGases = {
  2: 'helium',
  10: 'neon',
  18: 'argon',
};
```

{{site.alert.note}}
  Dart는 `gifts`의 타입을 `Map<String, String>`로 그리고
  `nobleGases`의 타입을 `Map<int, String>`로 추정합니다.
  Map에 알맞지 않는 타입의 값을 추가하면, analyzer나 런타임이
  에러를 발생시킵니다. 더 많은 정보를 원한다면,
  [타입 추론](/guides/language/type-system#type-inference)을
  참고하세요.
{{site.alert.end}}

Map 생성자를 사용하여 생성하는 것도 가능합니다:

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (map-constructor)"?>
```dart
var gifts = Map<String, String>();
gifts['first'] = 'partridge';
gifts['second'] = 'turtledoves';
gifts['fifth'] = 'golden rings';

var nobleGases = Map<int, String>();
nobleGases[2] = 'helium';
nobleGases[10] = 'neon';
nobleGases[18] = 'argon';
```

{{site.alert.note}}
  C# 또는 Java를 할 줄 안다면, `Map()` 대신 `new Map()`을
  기대했을 겁니다. Dart에서 `new` 키워드의 사용은 선택입니다.
  더 자세한 사항은 [생성자 사용하기](#using-constructors)를 참고하세요.
{{site.alert.end}}

서브스크립트 할당 연산자 (`[]=`)를 사용하여
기존의 map에 key-value 쌍을 추가하세요:

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (map-add-item)"?>
```dart
var gifts = {'first': 'partridge'};
gifts['fourth'] = 'calling birds'; // key-value 쌍 추가
```


서브스크립트 연산자 (`[]`)를 사용하여 map에서 원하는 값에 접근하세요:
Retrieve a value from a map using the subscript operator (`[]`):

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (map-retrieve-item)"?>
```dart
var gifts = {'first': 'partridge'};
assert(gifts['first'] == 'partridge');
```

Map에 존재하지 않는 key로 접근하면, `null`을 반환합니다:

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (map-missing-key)"?>
```dart
var gifts = {'first': 'partridge'};
assert(gifts['fifth'] == null);
```

Map에 있는 key-value 쌍의 개수를 알고 싶다면, `.length`을 사용하세요:

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (map-length)"?>
```dart
var gifts = {'first': 'partridge'};
gifts['fourth'] = 'calling birds';
assert(gifts.length == 2);
```

컴파일 타임 상수인 map을 생성하고 싶다면,
map 리터럴 앞에 `const`를 추가하세요:

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (const-map)"?>
```dart
final constantMap = const {
  2: 'helium',
  10: 'neon',
  18: 'argon',
};

// constantMap[2] = 'Helium'; // 이 라인은 에러를 발생시킵니다.
```

Map은 list 처럼 전개 연산자 (`...` 그리고 `...?`)와
컬렉션 `if` 그리고 `for`을 지원합니다.
더 자세한 사항과 예제를 보고 싶다면,
[전개 연산자 제안서][spread proposal] 와
[흐름 제어 컬렉션 제안서][collections proposal]를 참고하세요.

Map에 대한 더 많은 정보를 원한다면,
[제네릭](#generics) 섹션과
라이브러리 투어의
[`Maps` API](/guides/libraries/library-tour#maps)를
참고하세요.

<a id="characters"></a>

### Runes, grapheme clusters

Dart에서 [runes][]는 문자열의 유니코드 코드 포인트를 나타냅니다.
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

만약 유니코드 문자 각각을 읽고 써야한다면,
characters 패키지에 의해 String에 정의 되어 있는 characters getter를 사용하세요.
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


### Symbols

[`Symbol`][] 객체는 Dart 프로그램에 선언된 연산자나 식별자를 나타냅니다.
아마 Symbol을 사용할 필요가 없을지도 모릅니다.
하지만 축소(minification)를 수행하면 식별자의 이름은 변경되지만,
식별자의 symbol은 변경되지 않기 때문에 symbol은 이름으로 식별자를 참조하는 API에 매우 유용합니다.

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
// MOVE TO library tour?

void main() {
  print(Function.apply(int.parse, ['11']));
  print(Function.apply(int.parse, ['11'], {#radix: 16}));
  print(Function.apply(int.parse, ['11a'], {#onError: handleError}));
  print(Function.apply(
      int.parse, ['11a'], {#radix: 16, #onError: handleError}));
}

int handleError(String source) {
  return 0;
}
```
{% endcomment %}

Symbol 리터럴은 컴파일 타임 상수입니다.


## 함수

Dart는 진정항 객체 지향 언어이므로, 함수도
[Function.][Function API reference]
라는 타입을 가지는 객체로 존재합니다.
이건 함수가 변수나 다른 함수의 인자로 전달할 수 있다는 것을 의미합니다.
또한 함수인 것처럼 Dart 클래스의 인스턴스를 호출할 수 있습니다.
더 자세한 사항을 원한다면, [호출가능한 클래스](#callable-classes)를 참고하세요.

다음은 함수를 구현하는 예제입니다:

<?code-excerpt "misc/lib/language_tour/functions.dart (function)"?>
```dart
bool isNoble(int atomicNumber) {
  return _nobleGases[atomicNumber] != null;
}
```

비록 Effective Dart에서
[public APIs를 위한 타입 어노테이션](/guides/language/effective-dart/design#do-type-annotate-fields-and-top-level-variables-if-the-type-isnt-obvious)
을 추천하지만, 타입을 생략해도 함수는 제대로 작동합니다:

<?code-excerpt "misc/lib/language_tour/functions.dart (function-omitting-types)"?>
```dart
isNoble(atomicNumber) {
  return _nobleGases[atomicNumber] != null;
}
```

하나의 표현식만 가지는 함수를 선언 할 때 약칭(shorthand) 문법의 사용이 가능합니다:

<?code-excerpt "misc/lib/language_tour/functions.dart (function-shorthand)"?>
```dart
bool isNoble(int atomicNumber) => _nobleGases[atomicNumber] != null;
```

<code>=> <em>표현식</em></code> 문법은
<code>{ return <em>표현식</em>; }</code>의 약칭입니다.
`=>` 노테이션은 _화살표_ 문법으로도 불립니다.

{{site.alert.note}}
  *문(statement)*이 아닌 오직 *식(expression)*만이 화살표 (=\>)와
  세미콜론 (;) 사이에 올 수 있습니다. 예를 들어, [if 문](#if-and-else)은 불가능하지만,
  [조건식](#conditional-expressions)은 가능합니다.
{{site.alert.end}}

### 매개변수

함수는 *required positional* 매개변수를 얼마든지 가질 수 있습니다.
이 매개변수들은 *named* 매개변수 또는 *optional positional* 매개변수의 뒤에 나올 수 있습니다.
(둘 다 사용하는 것은 불가능합니다.)

{{site.alert.note}}
  [Flutter][] 위젯 생성자 처럼 몇몇 API들은 필수 매개변수에 대해서도
  named 매개 변수만 사용합니다. 다음 섹션에서 자세히 살펴봅시다.
{{site.alert.end}}

함수에 인자를 넘겨줄 때나 함수의 매개변수를 정의 할 때
[trailing commas][]를 사용할 수 있습니다.


#### Named 매개변수

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
/// [bold] 그리고 [hidden] 플래그 설정 ...
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
Named 매개변수를 호출자가 반드시 값을 전달하게 하고 싶다면,
`required`로 어노테이트 하세요:

<?code-excerpt "misc/lib/language_tour/functions.dart (required-named-parameters)" replace="/required/[!$&!]/g"?>
```dart
const Scrollbar({super.key, [!required!] Widget child});
```

`child` 인자 없이 `Scollbar`의 생성을 시도하면,
analyzer가 이슈를 보고합니다.

{{site.alert.note}}
  `required`로 표시된 매개변수는
  여전피 nullable 입니다:

  <?code-excerpt "misc/lib/language_tour/functions.dart (required-named-parameters-nullable)" replace="/Widget\?/[!$&!]/g; /ScrollbarTwo/Scrollbar/g;"?>
  ```dart
  const Scrollbar({super.key, required [!Widget?!] child});
  ```
{{site.alert.end}}

Positional 인자들을 먼저 배치하고 싶을 수 있지만,
Dart는 그것을 요구하지 않습니다.
Dart는 named 인자가 API에 적합할 때,
그것을 인자 목록의 아무 곳에나 배치 할 수 있습니다:

<?code-excerpt "misc/lib/language_tour/functions.dart (named-arguments-anywhere)"?>
```dart
repeat(times: 2, () {
  ...
});
```

#### Optional positional 매개변수

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

`Null`이 아닌 값으로 optional positional 매개변수의 디폴트 값을 정의하려면 `=`를 사용하세요.
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


### main() 함수

모든 앱은 엔트리 포인트 역할을 하는 최상위 `main()` 함수를 반드시 가지고 있어야 합니다.
`main()` 함수는 `void`를 반환하고 optional `List<String>` 매개변수를 인자롤 가집니다.

다음은 `main()` 함수의 예제입니다:

<?code-excerpt "misc/test/samples_test.dart (hello-world)"?>
```dart
void main() {
  print('Hello, World!');
}
```

다음은 인자를 가지는 커맨드 라인 앱의 `main()` 함수 예제입니다:

<?code-excerpt "misc/test/language_tour/functions_test.dart (main-args)"?>
```dart

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

### 일급 객체로서의 함수

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

### 익명 함수

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


### 렉시컬 스코프 (lexical scope)

Dart는 `lexically scoped` 언어로
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


### 렉시컬 클로저 (lexical closure)

*클로저* 는 함수가 이것의 원래 스코프의 밖에서 쓰여졌다고 해도,
해당 함수 렉시컬 스코프의 변수에 접근 할 수 있는 함수 객체입니다. 

함수는 주변 스코프에 정의된 변수를 포함합니다.
다음의 예제에서, `makeAdder()`는 `addBy` 변수를 캡쳐합니다.
반환된 함수가 가는 곳 마다, `addBy`를 기억합니다.

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


### 동등성 테스트 함수

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


### 반환 값

모든 함수는 값을 반환합니다. 반환 값이 명시되어 있지 않으면,
`return null;`이 암묵적으로 함수의 바디에 추가됩니다.

<?code-excerpt "misc/test/language_tour/functions_test.dart (implicit-return-null)"?>
```dart
foo() {}

assert(foo() == null);
```


## 연산자

Dart는 다음 표의 연산자들을 지원합니다.
표는 Dart 연산자들의 관계에 대한 **근사**인 Dart의 연산자 결합법칙과
[연산자 우선순위](#operator-precedence-example)를 최고에서
최저의 순서로 알려줍니다.
[클래스 멤버로서 연산자](#_operators)를 구현하는 것이 가능합니다.

|-----------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------|
| 설명                                     | 연산자                                                                                                                                                                                              | 결합법칙        |
|-----------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------|
| unary postfix                           | <code><em>expr</em>++</code>    <code><em>expr</em>--</code>    `()`    `[]`    `?[]`    `.`    `?.`    `!`                                                                                       | 없음          |
| unary prefix                            | <code>-<em>expr</em></code>    <code>!<em>expr</em></code>    <code>~<em>expr</em></code>    <code>++<em>expr</em></code>    <code>--<em>expr</em></code>      <code>await <em>expr</em></code>    | 없음          |
| multiplicative                          | `*`    `/`    `%`    `~/`                                                                                                                                                                         | 왼쪽          |
| additive                                | `+`    `-`                                                                                                                                                                                        | 왼쪽          |
| shift                                   | `<<`    `>>`    `>>>`                                                                                                                                                                             | 왼쪽          |
| bitwise AND                             | `&`                                                                                                                                                                                               | 왼쪽          |
| bitwise XOR                             | `^`                                                                                                                                                                                               | 왼쪽          |
| bitwise OR                              | `|`                                                                                                                                                                                               | 왼쪽          |
| relational&nbsp;and&nbsp;type&nbsp;test | `>=`    `>`    `<=`    `<`    `as`    `is`    `is!`                                                                                                                                               | 없음          |
| equality                                | `==`    `!=`                                                                                                                                                                                      | 없음          |
| logical AND                             | `&&`                                                                                                                                                                                              | 왼쪽          |
| logical OR                              | `||`                                                                                                                                                                                              | 왼쪽          |
| if null                                 | `??`                                                                                                                                                                                              | 왼쪽          |
| conditional                             | <code><em>expr1</em> ? <em>expr2</em> : <em>expr3</em></code>                                                                                                                                     | 오른쪽         |
| cascade                                 | `..` &nbsp;&nbsp; `?..`                                                                                                                                                                           | 오른쪽         |
| assignment                              | `=`    `*=`    `/=`    `+=`    `-=`    `&=`    `^=`    <em>etc.</em>                                                                                                                              | 오른쪽         |
{:.table .table-striped}

<!-- Authoritative -> 정직한 ? -->
{{site.alert.warning}}
  위의 표는 유용한 가이드로만 사용해야 합니다.
  연산자 우선순위와 결합법칙에 대한 개념은 언어 문법의 사실을 기반으로 한
  근사입니다. [Dart 언어 설명서][]에 정의된 문법에서
  Dart 연산자 관계의 정직한 동작에 대해 확인할 수 있습니다.
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

<a id="operator-precedence-example"></a>
[연산자 테이블](#operators)에서 각 연산자는 그 뒤에 오는 행의 연산자보다
높은 우선 순위를 가집니다. 예를 들어, 곱셈 연산자 `%`는
등식 연산자 `==` 보다 높은 우선 순위를 가지고 더 먼저 실행됩니다.
그리고 `==`는 논리 AND 연산자인 `&&` 보다 높은 우선 순위를 가집니다.
이런 우선 순위는 다음 두 줄의 라인이 같은 방식으로 실행된다는 것을 의미합니다:

<?code-excerpt "misc/test/language_tour/operators_test.dart (precedence)"?>
```dart
// 괄호는 가독성을 높혀줍니다.
if ((n % i == 0) && (d % i == 0)) ...

// 가독성은 좋지 않지만 위와 동일합니다.
if (n % i == 0 && d % i == 0) ...
```

{{site.alert.warning}}
  두 개의 피연산자를 가지는 연산자는, 맨 왼쪽 피연산자가 메서드를 결정합니다.
  예를 들어, `Vector` 객체와 `Point` 객체로 `aVector + aPoint`를 수행하면,
  `Vector`의 덧셈 (`+`)을 실행합니다.
{{site.alert.end}}


### 산술 연산자

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

Example:

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


### 동등, 관계 연산자

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
(드물게 두 객체가 정확하게 같은 것인지 확인하고 싶다면 [identical()][] 함수를 대신 사용하세요.)
`==` 연산자는 다음과 같이 작동합니다:

1.  *x* 또는 *y* 가 null 일 때, 모두 null이라면 true를, 둘 중에 하나만 null이라면
    false를 반환합니다.

2.  *y* 를 인자로 사용하여 *x*에서 `==` 메서드를 호출한 결과를 반환합니다.
    (`==` 같은 연산자들은 첫 번째 피연산자에서 호출되는 메서드입니다.
    더 자세한 사항은 [연산자](#_operators)를 참고하세요.)

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


### 타입 테스트 연산자

`as`, `is`, 및 `is!` 연산자는 런타임에 타입을 간단하게 확인 할 수 있습니다.

|-----------+-------------------------------------------|
| 연산자      | 의미                                       |
|-----------+-------------------------------------------|
| `as`      | 타입캐스트 ([라이브러리 prefixes](#specifying-a-library-prefix)를 특정할 때도 사용)
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

만약 객체가 타입 `T`라는 것을 확실하지 못한다면, 객체를 사용하기 전에 `is T`로 타입을 확인하세요.
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

### 할당 연산자

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


### 논리 연산자

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


### 비트 단위, 쉬프트 연산자

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
assert((value >>> 4) == 0x02); // Unsigned shift right
assert((-value >> 4) == -0x03); // Shift right
assert((-value >>> 4) > 0); // Unsigned shift right
```

{{site.alert.version-note}}
  _Triple-shift_ 또는 _unsigned shift_ 로 불리는 `>>>` 연산자는
  적어도 2.14의 [language version][]이 필요합니다.
{{site.alert.end}}


### 조건 표현식

Dart에는 [if-else](#if-and-else)문을 간결하게 표현 할 수 있는 두 개의 연산자가 있습니다:

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

<a id="cascade"></a>
### Cascade 표기법

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

### 다른 연산자들

아마 다른 예제에서 아래의 연산자들을 본 경험이 있을 것 입니다:

|----------+------------------------------+--------------------|
| 연산자     | 이름                          | 의미                |
|----------+------------------------------+--------------------|
| `()`     | Function application         | 함수 호출을 나타냅니다.
| `[]`     | Subscript access             | 재정의 할 수 있는 연산자 `[]`의 호출을 나타냅니다; 예제: `fooList[1]`는 `fooList`의 인덱스 `1`에 위치한 요소에 접근하기 위해 정수 `1`을 전달합니다.
| `?[]`    | Conditional subscript access | `[]`와 비슷하지만 맨 왼쪽 피연산자가 null일 수 있습니다; 예제: `fooList[1]`는 `fooList`가 null이 아니라면, `fooList`의 인덱스 `1`에 위치한 요소에 접근하기 위해 정수 `1`을 전달합니다. (만약 null이라면 표현식은 null로 평가됩니다.)
| `.`      | Member access                | 표현식의 프로퍼티를 참조합니다; 예제: `foo.bar`는 표현식 `foo`의 프로퍼티 `bar`를 선택합니다.
| `?.`     | Conditional member access    | `.`와 비슷하지만, 맨 왼쪽 피연산자가 null일 수 있습니다; 예제: `foo?.bar`는 `foo`가 null이 아니라면 표현식 `foo`의 프로퍼티 `bar`를 선택합니다. (만약 null이라면 `foo?.bar`의 값은 null입니다.)
| `!`      | Null assertion operator      | 표현식을 내제된 non-nullable 타입으로 캐스트합니다. 만약 캐스트에 실패하면 런타임 예외를 발생시킵니다; 예제: `foo!.bar`는 `foo`가 null이 아닌지 assert 하고, `foo`가 null이 아니라면 프로퍼티 `bar`를 선택합니다. (만약 null이라면 런타임 예외를 발생시킵니다.)
{:.table .table-striped}

`.`, `?.`, 그리고 `..` 연산자에 대한 더 많은 정보는,
[클래스](#classes)를 참고하세요.


## 흐름 제어문

다음 키워드들을 사용하여 Dart 코드의 흐름을 제어할 수 있습니다:

-   `if` 그리고 `else`
-   `for` 루프
-   `while` 그리고 `do`-`while` 루프
-   `break` 그리고 `continue`
-   `switch` 그리고 `case`
-   `assert`

[예외](#exceptions)에 설명되어 있듯이, `try-catch` 그리고 `throw`를 사용해도
흐름 제어가 가능합니다.


### If, else

다음 예제가 보여주듯이, `if` 문에 `else` 문을 사용하는 것은 선택적입니다.
[조건 표현식](#conditional-expressions)도 살펴보세요.

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
더 자세한 사항은 [Booleans](#booleans)을 살펴보세요.


### For 루프

기본 `for` 루프를 사용하여 반복을 수행할 수 있습니다. 예제:

<?code-excerpt "misc/test/language_tour/control_flow_test.dart (for)"?>
```dart
var message = StringBuffer('Dart is fun');
for (var i = 0; i < 5; i++) {
  message.write('!');
}
```

Dart의 `for` 루프 안에 있는 클로저는 Javascript에서 흔하게 발생하는
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

예상대로라면 `0`과 `1`을 출력합니다. 하지만, Javascript에서
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
  [Iterable collections codelab](/codelabs/iterables)
  을 참고하세요.
{{site.alert.end}}

Iterable 클래스는 다른 옵션으로 [forEach()][] 메서드 또한 가지고 있습니다.

<?code-excerpt "misc/test/language_tour/control_flow_test.dart (forEach)"?>
```dart
var collection = [1, 2, 3];
collection.forEach(print); // 1 2 3
```


### While, do-while

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


### Break, continue

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


### Switch, case

Dart의 Switch 문은 `==`를 사용해 정수, 문자열 그리고 컴파일 타임 상수를 비교합니다.
비교되는 객체는 반드시 동일한 클래스의 인스턴스이어야 하고 (서브타입도 불가능합니다),
해당 클래스는 `==`를 재정의해서는 안 됩니다.
[Enumerated 타입](#enumerated-types) 은 `switch` 문에서 효과적입니다.

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

그러나, Dart는 완성되지 않은 형태를 허용하며, 비어있는 `case` 문을 지원합니다:

<?code-excerpt "misc/lib/language_tour/control_flow.dart (switch-empty-case)"?>
```dart
var command = 'CLOSED';
switch (command) {
  case 'CLOSED': // 완성되지 않은 빈 case.
  case 'NOW_CLOSED':
    // CLASED 그리고 NOW_CLOSED에 대해 모두 실행합니다.
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


### Assert

개발하는 동안, boolean 조건이 false 일 때 코드 진행을 멈추고 싶다면
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
([trailing comma][trailing commas]를 사용해도됩니다):

<?code-excerpt "misc/test/language_tour/control_flow_test.dart (assert-with-message)"?>
```dart
assert(urlString.startsWith('https'),
    'URL ($urlString) should start with "https".');
```

`assert`의 첫 번째 인자에는 boolean 값을 평가하는 표현식이 와야합니다.
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

프로덕션 코드에서, assertion은 무시되고
`assert`의 인자는 평가되지 않습니다.

[webdev serve]: /tools/webdev#serve
[dart compile js]: /tools/dart-compile#js

## 예외

Dard 코드는 예외를 발생(throw), 캐치할 수 있습니다. 
예외는 예상하지 못한 일이 발생했다는 것을 의미하는 에러입니다.
예외가 캐치되지 않았다면, 예외를 발생시키는 [isolate](#isolates)가
지연된 상태이고 보통 해당 isolate나 프로그램이 종료됩니다.

Java와 다르게, Dart의 모든 예외는 확인되지 않은 예외(unchecked exception) 입니다.
메서드는 자신이 어떤 예외를 발생시킬지 선언하지 않고,
개발자에게 예외를 캐치하도록 요구하지도 않습니다.

Dart는 미리정의된 다양한 서브타입과 함께 [`Exception`][] 그리고 [`Error`][]
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
You can also throw arbitrary objects:

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
다수의 catch 절을 사용할 수 있습니다. 발생된 객체의 타입을 매치하는 첫 번째 catch 절은
해당 예외를 처리합니다. catch 절에 타입을 명시하지 않는다면, 해당 절은 발생되는 모든
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
`catch`를 사용하세요

`catch()`에 하나 또는 두개의 매개변수를 전달 할 수 있습니다.
첫 번째는 발생될 예외이고,
두 번째는 stack trace ([`StackTrace`][] 객체) 입니다.

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

`finally` 절은 `catch` 절의 매칭이 끝난 후 실행됩니다:

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

라이브러리 투어의 [예외](/guides/libraries/library-tour#exceptions)
를 통해 더 자세히 학습하세요.

## 클래스

Dart는 클래스와 mixin 기반 상속을 지원하는 객체지향언어입니다.
모든 객체는 클래스의 인스턴스이고, `Null`을 제외한 클래스는 모두 [`Object`][]에서 비롯합니다.
*Mixin 기반 상속*이란 말은, 모든 클래스가 하나의 superclass를 가지고 있지만
([top class][top-and-bottom]인 `Object?`를 제외한) 클래스의 바디는 다양한 클래스 계층에서 재사용 될 수 있음을 의미합니다.
[Extension methods](#extension-methods)는 서브 클래스를 추가하거나, 클래스를 바꾸지 않고 클래스에 기능을 추가하는 방법입니다.


### 클래스 멤버 사용하기

객체들은 함수와 데이터 (각각 *메서드*, *인스턴스 변수*)로 이루어진 *멤버*들을 가집니다.
메서드를 호출 할 때, 객체에서 함수를 *호출*합니다:
메서드는 해당 객체의 함수와 데이터에 접근 할 수 있습니다.

Dot (`.`)을 사용햐여 인스턴스 변수나, 메서드를 사용합니다:

<?code-excerpt "misc/test/language_tour/classes_test.dart (object-members)"?>
```dart
var p = Point(2, 2);

// y의 값을 얻음.
assert(p.y == 2);

// p의 distanceTo() 메서드 호출.
double distance = p.distanceTo(Point(4, 4));
```

만약 왼쪽 피연산자가 null 일 수도 있다면, `.`대신 `?.`을 사용하세요:

<?code-excerpt "misc/test/language_tour/classes_test.dart (safe-member-access)"?>
```dart
// p가 non-null이라면, a의 값을 p의 y의 값으로 설정합니다.
var a = p?.y;
```


### 생성자 사용하기

*생성자*를 사용하여 객체를 생성 할 수 있습니다.
생성자의 이름은 <code><em>ClassName</em></code> or
<code><em>ClassName</em>.<em>identifier</em></code>가 될 수 있습니다.
예를 들면, 다음의 예제에서 `Point` 객체를 `Point()`와 `Point.fromJson()` 생성자를 사용하여 생성합니다:

<?code-excerpt "misc/test/language_tour/classes_test.dart (object-creation)" replace="/ as .*?;/;/g"?>
```dart
var p1 = Point(2, 2);
var p2 = Point.fromJson({'x': 1, 'y': 2});
```

다음 코드는 같은 결과를 생성하지만,
생성자 이름에 선택적인 키워드인 `new`를 사용하였습니다:

<?code-excerpt "misc/test/language_tour/classes_test.dart (object-creation-new)" replace="/ as .*?;/;/g"?>
```dart
var p1 = new Point(2, 2);
var p2 = new Point.fromJson({'x': 1, 'y': 2});
```

몇몇 클래스는 [constant constructors](#constant-constructors)를 제공합니다.
상수 생성자를 사용하여 컴파일 타임 상수를 생성하고 싶다면, 생성자 이름 앞에 `const`를 사용하세요:

<?code-excerpt "misc/test/language_tour/classes_test.dart (const)"?>
```dart
var p = const ImmutablePoint(2, 2);
```

다음과 같이 두개의 동일한 컴파일 타임 상수를 생성하는 것은, 하나의 동일한 인스턴스를 생성합니다.

<?code-excerpt "misc/test/language_tour/classes_test.dart (identical)"?>
```dart
var a = const ImmutablePoint(1, 1);
var b = const ImmutablePoint(1, 1);

assert(identical(a, b)); // 둘은 같은 인스턴스입니다!
```

_상수 컨텍스트 (constant context)_ 안에서, 생성자나 리터럴 뒤의 `const`는 생략이 가능합니다. 
상수 map을 생성하는 다음 코드를 살펴봅시다:

<?code-excerpt "misc/test/language_tour/classes_test.dart (const-context-withconst)" replace="/pointAndLine1/pointAndLine/g"?>
```dart
// 불필요한 const 키워드가 많습니다.
const pointAndLine = const {
  'point': const [const ImmutablePoint(0, 0)],
  'line': const [const ImmutablePoint(1, 10), const ImmutablePoint(-2, 11)],
};
```

`const`를 선언 할 때 첫 번째 `const`를 제외하고 다른 `const`들은 생략 할 수 있습니다:

<?code-excerpt "misc/test/language_tour/classes_test.dart (const-context-noconst)" replace="/pointAndLine2/pointAndLine/g"?>
```dart
// 상수 컨텍스트를 만들어주는 하나의 const만 사용하면 됩니다.
const pointAndLine = {
  'point': [ImmutablePoint(0, 0)],
  'line': [ImmutablePoint(1, 10), ImmutablePoint(-2, 11)],
};
```

상수 생성자가 상수 컨텍스트의 밖에 존재하고 `const` 없이 호출되면,
**상수가 아닌 객체 (non-constant object)** 가 생성됩니다:

<?code-excerpt "misc/test/language_tour/classes_test.dart (nonconst-const-constructor)"?>
```dart
var a = const ImmutablePoint(1, 1); // 상수를 생성합니다
var b = ImmutablePoint(1, 1); // 상수를 생성하지 않습니다.

assert(!identical(a, b)); // 둘은 같은 인스턴스가 아닙니다!
```


### 객체 타입 검출

런타임에서 객체의 타입을 얻고 싶다면,
[`Type`][] 객체를 반환하는 `Object`의 프로퍼티인 `runtimeType`을 사용하세요.

<?code-excerpt "misc/test/language_tour/classes_test.dart (runtimeType)"?>
```dart
print('The type of a is ${a.runtimeType}');
```

{{site.alert.warn}}
  객체의 타입을 테스트하려면, `runtimeType` 대신 [type test operator][]를 사용하세요.
  프로덕션 환경에서, `object is Type` 테스트가 `object.runtimeType == Type` 테스트 보다 더 안전합니다.
{{site.alert.end}}

여기까지 클래스 _사용법_에 대해 알아보았습니다.
나머지 섹션에서는 _구현법_에 대해 알아보겠습니다.


### 인스턴스 변수

인스턴스 변수는 다음과 같이 선언합니다:

<?code-excerpt "misc/lib/language_tour/classes/point_with_main.dart (class)"?>
```dart
class Point {
  double? x; // 초기값이 null인 인스턴스 변수 x를 선언.
  double? y; // 초기값이 null인 y 선언.
  double z = 0; // 초기값이 0인 z 선언.
}
```

초기화 되지 않은 인스턴스 변수는 `null` 값을 가집니다.

모든 인스턴스 변수는 내부적으로 *getter* 메서드를 생성합니다.
Final이 아닌 변수 그리고 Initializers가 없는
`late final` 인스턴스 변수 또한 내부적으로 *setter* 메서드를 생성합니다.
더 자세히 알고 싶다면, [Getters and setters](#getters-and-setters)를 참고하세요.

Non-`late` 변수가 선언 된 동시에 초기화 되면
인스턴스가 생성될 때, 생성자와 해당 initializer 목록이 실행되기 전에 값이 설정됩니다.

<?code-excerpt "misc/lib/language_tour/classes/point_with_main.dart (class+main)" replace="/(double .*?;).*/$1/g" plaster="none"?>
```dart
class Point {
  double? x; // 초기값이 null인 인스턴스 변수 x 선언.
  double? y; // 초기값이 null인 y 선언.
}

void main() {
  var point = Point();
  point.x = 4; // x의 setter 메서드를 사용합니다.
  assert(point.x == 4); // x의 getter 메서드를 사용합니다.
  assert(point.y == null); // y의 디폴트 값은 null입니다.
}
```

인스턴스 변수는 `final`로 선언 될 수 있고, 그런 경우에는 단 한 번만 값이 정확하게 할당됩니다.
`final`과 non-`late` 인스턴스 변수를 선언 할 때 생성자 매개변수나,
생성자의 [initializer list](#initializer-list) 를 사용하여 초기화 하세요:

<?code-excerpt "misc/lib/effective_dart/usage_good.dart (field-init-at-decl)"?>
```dart
class ProfileMark {
  final String name;
  final DateTime start = DateTime.now();

  ProfileMark(this.name);
  ProfileMark.unnamed() : name = '';
}
```

생성자 바디가 시작된 후에 `final` 인스턴스 변수의 값을 할당하고 싶다면, 다음 중 하나를 사용하세요:

* [factory 생성자](#factory-constructors)를 사용하세요.
* `late final`를 [_주의해서_][late-final-ivar] 사용하세요: initializer가 없는
  `late final`는 API에 setter를 추가합니다.


### 생성자

클래스와 동일한 이름을 가지는 함수를 만들어 생성자를 선언 할 수 있습니다.
(선택적으로 [Named 생성자](#named-constructors)
에 명시되어 있는 식별자를 사용해도 됩니다.)

Generative 생성자의 가장 일반적인 형태는 클래스의 새 인스턴스를 생성합니다:

<?code-excerpt "misc/lib/language_tour/classes/point_alt.dart (constructor-long-way)" plaster="none"?>
```dart
class Point {
  double x = 0;
  double y = 0;

  Point(double x, double y) {
    // 인스턴스 변수를 초기화하는 더 나은 방법은
    // 형식 매개변수(formal parameters) 초기화를 참조하십시오.  
    this.x = x;
    this.y = y;
  }
}
```

`this` 키워드는 현재 인스턴스를 참조합니다.

{{site.alert.note}}
  이름의 충돌이 있을 때만 `this`를 사용하세요.
  없다면, Dart 스타일에서는 `this`를 생략합니다.
{{site.alert.end}}


#### 형식 매개변수 초기화

생성자 인수를 인스턴스 변수에 할당하는 패턴은 자주 쓰입니다. Dart에서는 그것을 더 쉽게 수행합니다.

매개변수로 인스턴스 변수를 초기화 하는 것은,
무조건 초기화 되어야만 하거나 기본 값이 주어져야하는 non-nullable 또는 `final` 인스턴스 변수만 가능합니다.

<?code-excerpt "misc/lib/language_tour/classes/point.dart (constructor-initializer)" plaster="none"?>
```dart
class Point {
  final double x;
  final double y;

  // 생성자 바디가 실행되기 전에 x와 y 인스턴스 변수 설정.
  Point(this.x, this.y);
}
```

Initializing formal로 주어진 변수는 초기화 리스트 범위에서 암묵적으로 final 입니다.


#### 디폴트 생성자

생성자를 선언하지 않았다면, 디폴트 생성자가 주어집니다.
디폴트 생성자는 인자가 없고, 인자가 없는 superclass의 생성자를 호출합니다.


#### 생성자는 상속되지 않습니다

Subclass는 superclass로 부터 생성자를 상속받지 않습니다.
생성자를 선언하지 않은 subclass는 이름과 인자가 없는 디폴트 생성자만을 가집니다.


#### Named 생성자

다수의 생성자를 구현하거나, 코드의 명확성을 더하고 싶다면 이름이 있는 생성자를 사용하세요:

<?code-excerpt "misc/lib/language_tour/classes/point.dart (named-constructor)" replace="/Point\.\S*/[!$&!]/g" plaster="none"?>
{% prettify dart tag=pre+code %}
const double xOrigin = 0;
const double yOrigin = 0;

class Point {
  final double x;
  final double y;

  Point(this.x, this.y);

  // Named constructor
  [!Point.origin()!]
      : x = xOrigin,
        y = yOrigin;
}
{% endprettify %}

Superclass의 생성자는 subclass로 상속되지 않는 다는 것을 꼭 기억하세요.
Subclass에서 superclass와 같은 Named 생성자를 사용하고 싶다면, subclass에서도 똑같이 구현해야 합니다.


#### Superclass의 Non-default 생성자 호출

Subclass의 생성자는 superclass의 이름이 없고(unnamed), 인수가 없는(no-argument) 생성자를 디폴트로 호출합니다.
Superclass의 생성자는 subclass 생성자 바디의 처음에 호출됩니다.
[initializer list](#initializer-list)가 사용되면, superclass가 호출되기 전에 실행됩니다.
요약하자면, 실행 순서는 다음과 같습니다:

1. initializer list
1. superclass의 인자가 없는 생성자
1. 메인 class의 인자가 없는 생성자

Superclass가 이름과 인자가 없는 생성자를 가지고 있지 않는다면,
반드시 superclass의 생성자 중 하나를 선택해서 호출해야 합니다.
생성자 바디에 콜론(`:`)을 붙혀서 선택한 superclass의 생성자를 명시하세요.

다음 예제에서 Employee는 자신의 superclass인 Person의 named 생성자를 호출합니다.
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

생성자가 실행되기 전에 Superclass의 생성자로 전해지는 인자가
평가되기 때문에 인자는 함수 호출 처럼 표현식이 될 수 있습니다:

<?code-excerpt "misc/lib/language_tour/classes/employee.dart (method-then-constructor)"?>
```dart
class Employee extends Person {
  Employee() : super.fromJson(fetchDefaultData());
  // ···
}
```

{{site.alert.warning}}
  Superclass의 생성자로 전달되는 인수는 `this`에 접근 할 수 없습니다.
  예를 들면, 인자는 정적 메서드를 호출 할 수 있지만, 인스턴스 메서드는 호출 할 수 없습니다.
{{site.alert.end}}

<a name="super-parameters"></a>
수동으로 superclass의 생성자 매개변수를 넘겨주는 것을 피하고 싶다면,
super-initializer 매개변수를 superclass의 생성자로 넘겨주면 됩니다.
이 피쳐를 redirecting 생성자와 함께 사용하는 것은 불가능합니다.
Super-initializer 매개변수는
[initializing formal parameters](#initializing-formal-parameters)와 비슷한 문법과 의미를 가집니다:

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

Super 생성자 호출이 positional 인자를 가지고 있다면,
Super-initializer 매개변수는 positional이 될 수 없습니다.
하지만 named 매개변수는 언제나 가능합니다:

<?code-excerpt "misc/lib/language_tour/classes/super_initializer_parameters.dart (named)" plaster="none"?>
```dart
class Vector2d {
  // ...

  Vector2d.named({required this.x, required this.y});
}

class Vector3d extends Vector2d {
  // ...

  // 매개변수 y를 named super 생성자로 넘겨줍니다:
  // Vector3d.yzPlane({required double y, required this.z})
  //       : super.named(x: 0, y: y);
  Vector3d.yzPlane({required super.y, required this.z}) : super.named(x: 0);
}
```

{{site.alert.version-note}}
  Super-initializer 매개변수를 사용하는 것은 최소 2.17의 [language version][]을 요구합니다.
  이전의 버전을 사용하고 있다면, 수동으로 모든 super 생성자 매개변수를 넘겨줘야 합니다.
{{site.alert.end}}

#### Initializer list

Superclass 생성자를 호출할 뿐만 아니라
생성자 바디가 실행되기 전에 인스턴스 변수를 초기화할 수도 있습니다. Initializer는 쉼표로 구분합니다.

{% comment %}
[TODO #2950: Maybe change example or point to discussion of ! (in map section?).]
{% endcomment %}

<?code-excerpt "misc/lib/language_tour/classes/point_alt.dart (initializer-list)"?>
```dart
// Initializer list는 생성자 바디가 실행되기 전에 인스턴스 변수를 설정합니다.
Point.fromJson(Map<String, double> json)
    : x = json['x']!,
      y = json['y']! {
  print('In Point.fromJson(): ($x, $y)');
}
```

{{site.alert.warning}}
  Initializer의 오른쪽은 `this`에 접근 할 수 없습니다.
{{site.alert.end}}

개발하는 동안에 `assert`를 initializer list 안에 넣어서 입력에 조건을 추가 할 수 있습니다.

<?code-excerpt "misc/lib/language_tour/classes/point_alt.dart (initializer-list-with-assert)" replace="/assert\(.*?\)/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
Point.withAssert(this.x, this.y) : [!assert(x >= 0)!] {
  print('In Point.withAssert(): ($x, $y)');
}
{% endprettify %}

Initializer list는 final 필드를 설정 할 때 유용합니다.
다음 예제에서는 세 개의 final 필드를 initializer list로 초기화합니다.
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


#### Redirecting 생성자

생성자의 목적이 같은 클래스 내의 다른 생성자로 리디렉트(redirect)하는 경우가 있습니다.
Redirecting 생성자의 바디는 비어있고 콜론 (:) 뒤에 나오며 클래스 이름 대신 `this`를 사용한 생성자 호출로 구성됩니다.

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


#### 상수 생성자

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
더 자세히 알고 싶다면, [using constructors](#using-constructors)를 참고하세요.


#### Factory 생성자

항상 클래스의 새로운 인스턴스를 생성하지 않는 생성자를 구현하고 싶다면, `factory` 키워드를 사용하세요.
예를 들면, factory 생성자는 인스턴스를 캐시에서 반환하거나, 서브타입의 인스턴스를 반환할 수 있습니다.
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


### 메서드

메서드는 객체가 특정한 행동을 할 수 있도록 해주는 함수입니다.

#### 인스턴스 메서드

객체의 인스턴스 메서드는 인스턴스 변수와 `this`에 접근 할 수 있습니다.
다음 예제의 `distanceTo()` 메서드가 인스턴스 메서드의 예입니다:

<?code-excerpt "misc/lib/language_tour/classes/point.dart (class-with-distanceTo)" plaster="none"?>
```dart
import 'dart:math';

class Point {
  final double x;
  final double y;

  Point(this.x, this.y);

  double distanceTo(Point other) {
    var dx = x - other.x;
    var dy = y - other.y;
    return sqrt(dx * dx + dy * dy);
  }
}
```

#### 연산자 {#_operators}

연산자는 특별한 이름을 가진 인스턴스 메소드 입니다.
Dart는 클래스 내에서 다음의 연산자들을 재정의 할 수 있습니다:

`<`  | `+`  | `|`  | `>>>`
`>`  | `/`  | `^`  | `[]`
`<=` | `~/` | `&`  | `[]=`
`>=` | `*`  | `<<` | `~`
`-`  | `%`  | `>>` | `==`
{:.table}

{{site.alert.note}}
  위의 표에 `!=` 같은 [연산자](#operators)가 없다는 것을 알 수 있습니다.
  왜냐하면 그런 표현들은 신택틱 슈가(syntactic sugar)이기 때문입니다.
  예를 들어, `e1 != e2` 같은 표현은 `!(e1 == e2)` 의 신택틱 슈가입니다.
{{site.alert.end}}

{%- comment %}
  Internal note from https://github.com/dart-lang/site-www/pull/2691#discussion_r506184100:
  -  `??`, `&&` and `||` are excluded because they are lazy / short-circuiting operators
  - `!` is probably excluded for historical reasons
{% endcomment %}

연산자 선언은 내장된 `operator` 식별자를 사용합니다.
다음의 예제는 vector 덧셈(`+`), 뺄셈(`-`) 그리고 항등(`==`)을 정의합니다.

<?code-excerpt "misc/lib/language_tour/classes/vector.dart"?>
```dart
class Vector {
  final int x, y;

  Vector(this.x, this.y);

  Vector operator +(Vector v) => Vector(x + v.x, y + v.y);
  Vector operator -(Vector v) => Vector(x - v.x, y - v.y);

  @override
  bool operator ==(Object other) =>
      other is Vector && x == other.x && y == other.y;

  @override
  int get hashCode => Object.hash(x, y);
}

void main() {
  final v = Vector(2, 3);
  final w = Vector(2, 2);

  assert(v + w == Vector(4, 5));
  assert(v - w == Vector(0, 1));
}
```


#### Getters, setters

Getter와 setter는 객체의 프로퍼티를 읽고(get) 쓰는(set) 함수 입니다.
모든 인스턴스 변수는 암묵적으로 getter와 setter를 가진다는 것을 기억하세요.
`get`과 `set` 키워드를 사용하여 getter와 setter를 구현하므로써 추가적인 프로퍼티를 생성 할 수 있습니다.

<?code-excerpt "misc/lib/language_tour/classes/rectangle.dart"?>
```dart
class Rectangle {
  double left, top, width, height;

  Rectangle(this.left, this.top, this.width, this.height);

  // right, bottom 이라는 두 개의 계산된 프로퍼티 정의.
  double get right => left + width;
  set right(double value) => left = value - width;
  double get bottom => top + height;
  set bottom(double value) => top = value - height;
}

void main() {
  var rect = Rectangle(3, 4, 20, 15);
  assert(rect.left == 3);
  rect.right = 12;
  assert(rect.left == -8);
}
```

With getters and setters, you can start with instance variables, later
wrapping them with methods, all without changing client code.

{{site.alert.note}}
  증가 (++)와 같은 연산자는 getter가 명백히 정의되어 있든 말든 특정 방법으로 동작합니다.
  예상치 못한 부작용을 피하기 위해 연산자는 getter를 정확히 한 번 호출하여 값을 임시 변수에 저장하세요.
{{site.alert.end}}

#### 추상 메서드

인스턴스, getter, setter 메서드는 추상화 될 수 있습니다.
추상화란 인터페이스만 구현한 상태로 나머지 부분은 다른 클래스들에게 맡기는 것을 의미합니다.
추상 메서드는 오직 [abstract classes](#abstract-classes)에 존재 할 수 있습니다.

메서드를 추상화 하려면, 메서드 바디 대신에 세미콜론 (;)을 사용하세요:

<?code-excerpt "misc/lib/language_tour/classes/doer.dart"?>
```dart
abstract class Doer {
  // 인스턴스 변수와 메서드 정의 ...

  void doSomething(); // 추상 메서드 정의.
}

class EffectiveDoer extends Doer {
  void doSomething() {
    // 메서드를 구현하여 더 이상 추상적이지 않게 만듬 ...
  }
}
```


### 추상 클래스

`abstract` 수식어를 사용하여, *추상 클래스* (인스턴스화 될 수 없는)를 선언하세요.
추상 클래스는 인터페이스를 정의 할 때 유용하며, 종종 일부 구현과 함께 사용됩니다
추상 클래스를 인스턴스화하려면, [factory 생성자](#factory-constructors)를 정의하세요.

추상 클래스는 [추상 메서드](#abstract-methods)를 가질 수 있습니다.
다음은 추상 메서드를 가지는 추상 클래스의 예제입니다:

<?code-excerpt "misc/lib/language_tour/classes/misc.dart (abstract)"?>
```dart
// 이 클래스는 abstract로 선언되어 인스턴스화 할 수 없습니다.
abstract class AbstractContainer {
  // 생성자, 필드, 메서드 등 정의

  void updateChildren(); // 추상 메서드.
}
```

<a id="interfaces"></a>
### 암묵적 인터페이스 (Implicit interfaces)

모든 클래스는 암묵적으로 클래스의 인스턴스 멤버를 포함하는 인터페이스를 정의합니다.
B 클래스를 상속받지 않은 A 클래스가 B의 API를 사용하고 싶다면 B 인터페이스를 구현해야 합니다.

하나의 클래스는 `implements`문 안에 하나 혹은 여러개의 인터페이스를 구현하고,
인터페이스에 필요한 API들을 제공합니다:

<?code-excerpt "misc/lib/language_tour/classes/impostor.dart"?>
```dart
// person. 암묵적 인터페이스는 greet()을 포함합니다.
class Person {
  // 인터페이스의 안에 있지만 해당 라이브러리에서만 확인이 가능합니다.
  final String _name;

  // 생성자이기 때문에 인터페이스에 없습니다.
  Person(this._name);

  // 인터페이스에 있습니다.
  String greet(String who) => 'Hello, $who. I am $_name.';
}

// Person 인터페이스의 구현.
class Impostor implements Person {
  String get _name => '';

  String greet(String who) => 'Hi $who. Do you know who I am?';
}

String greetBob(Person person) => person.greet('Bob');

void main() {
  print(greetBob(Person('Kathy')));
  print(greetBob(Impostor()));
}
```

다음은 여러 개의 인터페이스를 가지는 클래스입니다:

<?code-excerpt "misc/lib/language_tour/classes/misc.dart (point_interfaces)"?>
```dart
class Point implements Comparable, Location {...}
```


### 클래스 확장

Subclass를 만들고 싶다면, `extends`를 사용하세요.
해당 클래스 안에서 superclass를 참조하고 싶다면 `super`를 사용하면 됩니다:

<?code-excerpt "misc/lib/language_tour/classes/extends.dart" replace="/extends|super/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
class Television {
  void turnOn() {
    _illuminateDisplay();
    _activateIrSensor();
  }
  // ···
}

class SmartTelevision [!extends!] Television {
  void turnOn() {
    [!super!].turnOn();
    _bootNetworkInterface();
    _initializeMemory();
    _upgradeApps();
  }
  // ···
}
{% endprettify %}

`extends`의 다른 사용법을 알고 싶다면,
[parameterized types](#restricting-the-parameterized-type)의
[generics](#generics)을 참고하세요.

<a name="overridable-operators"></a>

#### 멤버 재정의

Subclasses는 [연산자](#_operators)를 포함한 인스턴스 메서드, getter, setter를 오버라이드 하는 것이 가능합니다.
`@override` 표기를 사용하여 의도적으로 멤버를 재정의 할 수 있습니다:

<?code-excerpt "misc/lib/language_tour/metadata/television.dart (override)" replace="/@override/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
class Television {
  // ···
  set contrast(int value) {...}
}

class SmartTelevision extends Television {
  [!@override!]
  set contrast(num value) {...}
  // ···
}
{% endprettify %}

재정의 메서드 선언은 그 메서드가 재정의하는 메서드와 여러가지 방법으로 매치되어야 합니다:

* 반환 타입은 반드시 재정의되는 함수의 반환 타입(서브 타입도 가능)과 동일해야 합니다.
* 인자의 타입은 오버라이딩 되는 함수의 인수 타입(supertype도 가능)과 반드시 동일해야 합니다.
  앞선 예제에서, `SmartTelevision`의 setter인 `contrast`는 인자의 타입을 `int`의 supertype인 `num`으로 변경합니다.
* 만약 재정의되는 함수가 _n_개의 positional 매개변수를 가진다면,
  재정의하는 함수 또한 _n_개의 positional 매개변수를 가져야 합니다.
* [Generic method](#using-generic-methods)는 non-generic인 메서드를 재정의 할 수 없고,
  그 반대도 마찬가지 입니다.

메서드의 매개변수나 인스턴스 변수의 타입을 축소하고 싶은 때가 있을겁니다.
이런 행동은 보통의 룰을 어기는 행위이고, 런타임에서 에러를 발생 시킬 수도 있는 다운캐스트와 비슷합니다.
여전히 코드가 타입 에러를 발생시키지 않는다고 확신 할 수 있다면, 타입을 축소하는 것은 가능합니다.
이런 경우에, [`covariant` keyword](/guides/language/sound-problems#the-covariant-keyword)
를 매개변수 선언에 사용하면 됩니다.
자세한 정보를 원한다면, [Dart 언어 설명서][]를 참고하세요.

{{site.alert.warning}}
  `==` 를 재정의하면, `hashCode` getter도 재정의해야 합니다.
  [Implementing map keys](/guides/libraries/library-tour#implementing-map-keys)에
  `==`와 `hashCode`를 재정의하는 예제가 있습니다.
{{site.alert.end}}

#### noSuchMethod()

코드가 존재하지 않는 함수나 인스턴스 변수에 접근하는 것을 감지, 처리하고 싶다면 `noSuchMethod()` 함수를 재정의하세요:

<?code-excerpt "misc/lib/language_tour/classes/no_such_method.dart" replace="/noSuchMethod(?!,)/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
class A {
  // noSuchMethod를 재정의하지 않고 존재하지 않는 멤버를 사용하면,
  // NoSuchMethodError가 발생합니다.
  @override
  void [!noSuchMethod!](Invocation invocation) {
    print('You tried to use a non-existent member: '
        '${invocation.memberName}');
  }
}
{% endprettify %}

구현되지 않은 함수가 다음 중 **하나**라도 만족한다면, 그 함수는 **호출 할 수 없습니다**.
You **can't invoke** an unimplemented method unless
**one** of the following is true:

* 리시버가 static 타입 `dynamic`일 때.

* 리시버는 구현되지 않은 메서드(추상 메서드는 가능)를 정의하는 static 타입을 가지며,
  리시버의 dynamic 타입은 클래스 `Object`와 다른 `noSuchMethod()`를 구현 했을 때.

더 자세한 정보를 원한다면, [noSuchMethod forwarding specification.](https://github.com/dart-lang/language/blob/master/archive/feature-specifications/nosuchmethod-forwarding.md)를 참고하세요.


### 확장 메서드

확장 메서드는 이미 존재하는 라이브러리에 기능을 추가하는 방법입니다.
우리는 확장 메서드가 무엇인지 모른 채 사용할 수도 있습니다.
예를 들어, IDE를 사용해서 코드를 구현할 때 IDE는 정규 메서드가 아닌 확장 메서드를 추천할 수도 있습니다.

다음은 `string_apis.dart`에 정의되어 있는 `String`의 확장 메서드인 `parseInt()`의 예제 입니다:

```dart
import 'string_apis.dart';
...
print('42'.padLeft(5)); // String 메서드 사용.
print('42'.parseInt()); // 확장 메서드 사용.
```

확장 메서드의 구현과 활용을 더 자세히 알고 싶다면, [extension methods page][]를 참고하세요.

<a id="enums"></a>
### 열거 타입 (Enumerated types)

열거 타입은 종종 _enumerations_, _enums_ 으로도 불립니다.
이 타입은 정해진 수의 상수 값을 가지는 특별한 종류의 클래스 입니다.

{{site.alert.note}}
  모든 enums는 자동으로 [`Enum`][] 클래스를 확장합니다.
  이들은 가려져 있으며, 이는 subclass가 될 수 없고 implement,
  mix 또는 명시적으로 인스턴스화할 수 없다는 것을 의미합니다.

  추상 클래스와 mixin은 명시적으로 `Enum`을 구현하거나 확장합니다.
  그러나 enum 선언에 의해 구현되거나 enum 선언에 믹스되지 않는 한,
  어떤 개체도 실제로 해당 클래스나 mixin의 타입을 구현할 수 없습니다.
{{site.alert.end}}

#### 간단한 enum 선언하기

열거 타입을 선언하고 싶다면,
`enum` 키워드를 사용하고 열거하고 싶은 값들을
나열하세요:

<?code-excerpt "misc/lib/language_tour/classes/enum.dart (enum)"?>
```dart
enum Color { red, green, blue }
```

{{site.alert.tip}}
  열거 타입을 선언 할 때 복사-붙혀넣기 에러를 방지하기 위해, [trailing commas][]를 사용해도 됩니다.
{{site.alert.end}}

#### 발전된(enhanced) enum 사용하기

Dart는 필드, 메서드, 상수 생성자 같이 수가 정해져 있는 상수 인스턴스가 있는 클래스를 선언하는 데 enum을 사용하는 것이 가능합니다.

발전된 enum을 선언하려면, [클래스](#classes)와 비슷하지만 몇가지 다른 문법을 따라야 합니다.

* [mixins](#mixins)으로 추가되는 변수들까지 모든 인스턴스 변수들은 `final`로 선언되어야합니다.
* 모든 [generative constructors](#constant-constructors) 상수로 선언되어야합니다.
* [Factory 생성자](#factory-constructors)는 고정된 enum 인스턴스 중 하나만을 반환할 수 있습니다.
* [`Enum`]이 자동으로 확장되므로 다른 클래스들은 확장 될 수 없습니다.
* `index`, `hashCode`, 항등 연산자 `==`는 재정의할 수 없습니다.
* Avalues로 명명된 멤버는 enum에 선언 될 수 없습니다. 만약 enum에 선언한다면, 자동으로 생성된 정적 `values` getter와 충돌합니다.
* Enum의 모든 인스턴스들은 선언의 처음 부분에 선언되어야 하고 반드시 한 개 이상의 인스턴스가 선언되어야 합니다.

다음은 다수의 인스턴스, 인스턴스 변수, getter 그리고 인터페이스를 가지는 발전된 enum의 예제 입니다:

<?code-excerpt "misc/lib/language_tour/classes/enum.dart (enhanced)"?>
```dart
enum Vehicle implements Comparable<Vehicle> {
  car(tires: 4, passengers: 5, carbonPerKilometer: 400),
  bus(tires: 6, passengers: 50, carbonPerKilometer: 800),
  bicycle(tires: 2, passengers: 1, carbonPerKilometer: 0);

  const Vehicle({
    required this.tires,
    required this.passengers,
    required this.carbonPerKilometer,
  });

  final int tires;
  final int passengers;
  final int carbonPerKilometer;

  int get carbonFootprint => (carbonPerKilometer / passengers).round();

  @override
  int compareTo(Vehicle other) => carbonFootprint - other.carbonFootprint;
}
```

발전된 enum에 대해 더 자세히 알고 싶다면, [클래스](#classes)를 참고하세요.

{{site.alert.version-note}}
  발전된 enum은 최소 2.17의 [language version][]을 요구합니다.
{{site.alert.end}}

#### enum 사용하기

[정적 변수](#static-variables)에 접근하는 것 처럼 열거 값에 접근하면 됩니다:

<?code-excerpt "misc/lib/language_tour/classes/enum.dart (access)"?>
```dart
final favoriteColor = Color.blue;
if (favoriteColor == Color.blue) {
  print('Your favorite color is blue!');
}
```

Enum의 각 값들은 선언에서 값의 0 기반 위치를 반환하는 'index' getter가 있습니다.
예를 들어, 첫 번째 값은 index 0을 가지고 두 번째 값은 index 1을 가집니다.

<?code-excerpt "misc/lib/language_tour/classes/enum.dart (index)"?>
```dart
assert(Color.red.index == 0);
assert(Color.green.index == 1);
assert(Color.blue.index == 2);
```

열거 값의 리스트를 얻고 싶다면, enum의 `values` 상수를 사용하세요.

<?code-excerpt "misc/lib/language_tour/classes/enum.dart (values)"?>
```dart
List<Color> colors = Color.values;
assert(colors[2] == Color.blue);
```

[switch 구문](#switch-and-case)에 enum을 사용해도 됩니다.
하지만 enum의 모든 값들을 처리하지 않으면 경고가 발생합니다:

<?code-excerpt "misc/lib/language_tour/classes/enum.dart (switch)"?>
```dart
var aColor = Color.blue;

switch (aColor) {
  case Color.red:
    print('Red as roses!');
    break;
  case Color.green:
    print('Green as grass!');
    break;
  default: // 이 처리가 없으면, 경고가 발생합니다.
    print(aColor); // 'Color.blue'
}
```

열거 값의 이름에 접근하고 싶다면, `Color.blue`의 `'blue'`처럼 `.name` 프로퍼티를 사용하면 됩니다:

<?code-excerpt "misc/lib/language_tour/classes/enum.dart (name)"?>
```dart
print(Color.blue.name); // 'blue'
```


<a id="mixins"></a>

### 클래스에 피쳐 추가하기: mixins

Mixins은 다수의 클래스 계층에서 클래스의 코드를 재사용 할 수 있는 방법입니다.
Mixin을 _사용_ 하려면, 다음 코드처럼 `with` 키워드와 사용 할 mixin의 이름을 명시하면 됩니다:

<?code-excerpt "misc/lib/language_tour/classes/orchestra.dart (Musician and Maestro)" replace="/(with.*) \{/[!$1!] {/g"?>
{% prettify dart tag=pre+code %}
class Musician extends Performer [!with Musical!] {
  // ···
}

class Maestro extends Person [!with Musical, Aggressive, Demented!] {
  Maestro(String maestroName) {
    name = maestroName;
    canConduct = true;
  }
}
{% endprettify %}

Mixin을 _구현_하려면 Object를 확장하며, 생성자가 없는 클래스를 생성하세요.
Mixin을 일반 클래스로 사용할 수 없도록 하려면 `class` 대신 `mixin` 키워드를 사용하세요:

<?code-excerpt "misc/lib/language_tour/classes/orchestra.dart (Musical)"?>
```dart
mixin Musical {
  bool canPlayPiano = false;
  bool canCompose = false;
  bool canConduct = false;

  void entertainMe() {
    if (canPlayPiano) {
      print('Playing piano');
    } else if (canConduct) {
      print('Waving hands');
    } else {
      print('Humming to self');
    }
  }
}
```

Mixin을 사용 할 수 있는 타입을 제한할 수도 있습니다.
예를 들어, mixin이 정의하지 않은 메서드를 호출할 수 있는지에 따라 달라질 수 있습니다.
다음 예제처럼 `on` 키워드로 사용할 수 있는 superclass를 제한함으로써 mixin의 사용을 제한할 수 있습니다:

<?code-excerpt "misc/lib/language_tour/classes/orchestra.dart (mixin-on)" plaster="none" replace="/on Musician2/[!on Musician!]/g" ?>
```dart
class Musician {
  // ...
}
mixin MusicalPerformer [!on Musician!] {
  // ...
}
class SingerDancer extends Musician with MusicalPerformer {
  // ...
}
```

위의 코드에서 `Musician` 클래스를 확장, 구현하는 클래스들만 `MusicalPerformer` mixin을 사용 할 수 있습니다.
`SingerDancer`가 `Musician`을 확장하기 때문에, `SingerDancer`는 `MusicalPerformer` mixin을 사용 할 수 있습니다.

### 클래스 변수와 메서드

`static` 키워드를 사용해 class-wide한 변수와 메소드를 선언하세요.

#### 정적 변수

정적 변수(클래스 변수)는 class-wide한 상수와, 상태를 정의 할 때 유용합니다:

<?code-excerpt "misc/lib/language_tour/classes/misc.dart (static-field)"?>
```dart
class Queue {
  static const initialCapacity = 16;
  // ···
}

void main() {
  assert(Queue.initialCapacity == 16);
}
```

정적 변수는 사용하기 전에는 초기화되지 않습니다.

{{site.alert.note}}
  이 페이지는 [스타일 가이드 추천](/guides/language/effective-dart/style#identifiers)
  에서 선호하는 `lowerCamelCase`를 상수 선언에 사용합니다.
{{site.alert.end}}

#### 정적 메서드

정적 메소드(클래스 메소드)는 인스턴스 위에서 실행되지 않기 때문에 `this`에 접근 할 수 없지만,
정적 변수에 대한 접근은 가능합니다.
다음 코드는 클래스에서 직접 정적 메소드를 실행합니다:

<?code-excerpt "misc/lib/language_tour/classes/point_with_distance_method.dart"?>
```dart
import 'dart:math';

class Point {
  double x, y;
  Point(this.x, this.y);

  static double distanceBetween(Point a, Point b) {
    var dx = a.x - b.x;
    var dy = a.y - b.y;
    return sqrt(dx * dx + dy * dy);
  }
}

void main() {
  var a = Point(2, 2);
  var b = Point(4, 4);
  var distance = Point.distanceBetween(a, b);
  assert(2.8 < distance && distance < 2.9);
  print(distance);
}
```

{{site.alert.note}}
  범용적으로 자주 쓰이는 기능을 구현하기 위해서는 정적 메서드 대신 최상위 메서드를 사용하는 것을 고려해보세요.
{{site.alert.end}}

정적 메소드를 컴파일 타임 상수로 사용 할 수 있습니다.
예를 들어, 상수 생성자의 매개변수로 정적 메소드를 넘겨 줄 수 있습니다.


## 제네릭

기본 배열 타입의 API 문서를 보면,
[`List`][] 타입이 `List<E>`로 표기되어 있는 걸 볼 수 있습니다.
\<...\> 표시는 List를 형식 타입 매개변수를 가지는 제네릭 (또는 *매개변수화된*) 타입으로 지정합니다.
[관례상][] 대부분의 타입 변수는 E, T, S, K, V 같은 single-letter 이름을 가집니다.

[관례상]: /guides/language/effective-dart/design#do-follow-existing-mnemonic-conventions-when-naming-type-parameters


### 제네릭을 왜 사용할까?

보통 타입 세이프티 때문에 제네릭을 사용하지만, 사실 더 많은 기능을 수행합니다:

* 제네릭 타입을 적절하게 명시한 코드는 더 잘 작성된 코드 입니다.
* 코드 중복를 줄이기 위해 제네릭을 사용 할 수 있습니다.

리스트가 string 값만 가지게 하고 싶다면, List<String>로 리스트를 선언하면 됩니다.
그렇게 함으로써, 동료와 개발 툴이 string 이외의 값은 리스트에 추가될 수 없음을 바로 알 수 있습니다:

{:.fails-sa}
```dart
var names = <String>[];
names.addAll(['Seth', 'Kathy', 'Lars']);
names.add(42); // Error
```

제네릭을 사용하는 또 다른 이유는 코드 중복을 줄이기 위함입니다.
제네릭은 정적인 분석의 이점을 챙기면서, 많은 타입들이 단일 인터페이스와 구현을 공유 할 수 있게 합니다.
예를 들어, 객체를 캐싱하는 인터페이스를 생성한다고 해봅시다:

<?code-excerpt "misc/lib/language_tour/generics/cache.dart (ObjectCache)"?>
```dart
abstract class ObjectCache {
  Object getByKey(String key);
  void setByKey(String key, Object value);
}
```

해당 인터페이스의 string 버전이 필요하다면 다음과 같이 선언하면 됩니다:

<?code-excerpt "misc/lib/language_tour/generics/cache.dart (StringCache)"?>
```dart
abstract class StringCache {
  String getByKey(String key);
  void setByKey(String key, String value);
}
```

나중에 number 버전이 필요해 졌다면... 어떻게 하는 게 좋을까요?

제네릭 타입은 위처럼 모든 인터페이스를 생성해야하는 문제를 해결해줍니다.
타입 매개변수를 가지는 하나의 단일 인터페이스만을 구현하면 됩니다:

<?code-excerpt "misc/lib/language_tour/generics/cache.dart (Cache)"?>
```dart
abstract class Cache<T> {
  T getByKey(String key);
  void setByKey(String key, T value);
}
```

위의 코드에서, T는 stand-in 타입입니다.
이것은 개발자가 추후에 타입을 마음대로 지정 할 수 있게 해주는 플레이스 홀더입니다.


### 컬렉션 리터럴 사용

List, set 그리고 map 리터럴은 매개변수화 될 수 있습니다.
매개변수화된 리터럴은 list, set에 <code>&lt;<em>type</em>></code> 또는
map에 <code>&lt;<em>keyType</em>, <em>valueType</em>></code>를 시작 괄호에 추가하는 것만 빼면,
일반적으로 사용하는 리터럴과 비슷하게 생겼습니다.
다음은 타입이 있는 리터럴의 예제입니다:

<?code-excerpt "misc/lib/language_tour/generics/misc.dart (collection-literals)"?>
```dart
var names = <String>['Seth', 'Kathy', 'Lars'];
var uniqueNames = <String>{'Seth', 'Kathy', 'Lars'};
var pages = <String, String>{
  'index.html': 'Homepage',
  'robots.txt': 'Hints for web robots',
  'humans.txt': 'We are people, not machines'
};
```


### 생성자에 매개변수화된 타입 사용

생성자를 사용 할 때 하나 혹은 다수의 타입을 특정하고 싶다면,
타입을 클래스 이름 다음의 `<...>` (angle brackets) 안에 넣으세요:

<?code-excerpt "misc/test/language_tour/generics_test.dart (constructor-1)"?>
```dart
var nameSet = Set<String>.from(names);
```

{% comment %}[TODO #2950: It isn't idiomatic to use a constructor for an empty Map. Change to a class that doesn't have literal support.]{% endcomment %}

다음 예제에서는 정수 키와 View 타입의 값을 가지는 map을 생성합니다:

<?code-excerpt "misc/test/language_tour/generics_test.dart (constructor-2)"?>
```dart
var views = Map<int, View>();
```


### 제네릭 콜렉션과 그것이 가지는 타입

Dart 제네릭 타입은 *구체화* 되어있습니다.
그것은 런타임에 타입들에 대한 정보를 가져온다는 것을 의미합니다.
예를 들어, 콜렉션의 타입을 다음과 같이 테스트 할 수 있습니다:

<?code-excerpt "misc/test/language_tour/generics_test.dart (generic-collections)"?>
```dart
var names = <String>[];
names.addAll(['Seth', 'Kathy', 'Lars']);
print(names is List<String>); // true
```

{{site.alert.note}}
  Dart와 다르게, Java의 제네릭은 *erasure*을 사용하고,
  제네릭 타입 매개변수는 런타임에 삭제됩니다.
  자바에서 어떤 객체가 List인지 테스트 할 수는 있지만, 
  `List<String>`인지 테스트 하는 것은 불가능합니다.
{{site.alert.end}}


### 매개변수화된 타입 제한

제네릭 타입을 구현할 때, 인자로 제공되는 타입을 제한해서
인자가 특정 타입의 서브타입이 되게 해야 할 경우가 발생합니다.
`extends`를 사용하면 가능합니다.

Non-nullalbe인 것을 보장하기 위해, 디폴트인 [`Object?`][top-and-bottom]
대신 `Object`의 서브타입으로 만들 때 자주 사용됩니다.

<?code-excerpt "misc/lib/language_tour/generics/misc.dart (non-nullable)"?>
```dart
class Foo<T extends Object> {
  // Foo에게 제공되는 T 타입은 반드시 non-nullable 입니다.
}
```

`Object` 이외의 타입들과 함께 `extends`를 사용 할 수 있습니다.
다음은 `SomeBaseClass`를 확장하는 예로, `SomeBaseClass`의 멤버들은 타입 `T`의 객체로 볼 수 있습니다:

<?code-excerpt "misc/lib/language_tour/generics/base_class.dart" replace="/extends SomeBaseClass(?=. \{)/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
class Foo<T [!extends SomeBaseClass!]> {
  // 클래스 구현 ...
  String toString() => "Instance of 'Foo<$T>'";
}

class Extender extends SomeBaseClass {...}
{% endprettify %}

`SomeBaseClass`나 이것의 서브타입을 제네릭 인자로 사용하는 것도 가능합니다:

<?code-excerpt "misc/test/language_tour/generics_test.dart (SomeBaseClass-ok)" replace="/Foo.\w+./[!$&!]/g"?>
{% prettify dart tag=pre+code %}
var someBaseClassFoo = [!Foo<SomeBaseClass>!]();
var extenderFoo = [!Foo<Extender>!]();
{% endprettify %}

제네릭 인자를 특정하지 않는 것도 가능합니다:

<?code-excerpt "misc/test/language_tour/generics_test.dart (no-generic-arg-ok)" replace="/expect\((.*?).toString\(\), .(.*?).\);/print($1); \/\/ $2/g"?>
```dart
var foo = Foo();
print(foo); // 'Foo<SomeBaseClass>'의 인스턴스
```

Non-`SomeBaseClass` 타입으로 특정하는 것은 에러를 발생시킵니다:

{:.fails-sa}
{% prettify dart tag=pre+code %}
var foo = [!Foo<Object>!]();
{% endprettify %}


### 제네릭 메소드 사용

메서드와 함수에도 타입 인자를 사용할 수 있습니다:

<!-- {{site.dartpad}}/a02c53b001977efa4d803109900f21bb -->
<!-- https://gist.github.com/a02c53b001977efa4d803109900f21bb -->
<?code-excerpt "misc/test/language_tour/generics_test.dart (method)" replace="/<T.(?=\()|T/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
[!T!] first[!<T>!](List<[!T!]> ts) {
  // 초기 작업 또는 에러 확인, 그리고 ...
  [!T!] tmp = ts[0];
  // 추가적인 확인 또는 프로세싱 ...
  return tmp;
}
{% endprettify %}

`first` (`<T>`)에 있는 제네릭 타입 매개변수로
여러 위치에서 타입 인자인 `T`를 사용할 수 있습니다.

* 함수의 반환 타입 (`T`).
* 인자의 타입 (`List<T>`).
* 지역 변수의 타입 (`T tmp`).


## Libraries and visibility

The `import` and `library` directives can help you create a
modular and shareable code base. Libraries not only provide APIs, but
are a unit of privacy: identifiers that start with an underscore (`_`)
are visible only inside the library. *Every Dart app is a library*, even
if it doesn’t use a `library` directive.

Libraries can be distributed using [packages](/guides/packages).

{{site.alert.info}}
  If you're curious why Dart uses underscores instead of
  access modifier keywords like `public` or `private`, see
  [SDK issue 33383](https://github.com/dart-lang/sdk/issues/33383).
{{site.alert.end}}


### Using libraries

Use `import` to specify how a namespace from one library is used in the
scope of another library.

For example, Dart web apps generally use the [dart:html][]
library, which they can import like this:

<?code-excerpt "misc/test/language_tour/browser_test.dart (dart-html-import)"?>
```dart
import 'dart:html';
```

The only required argument to `import` is a URI specifying the
library.
For built-in libraries, the URI has the special `dart:` scheme.
For other libraries, you can use a file system path or the `package:`
scheme. The `package:` scheme specifies libraries provided by a package
manager such as the pub tool. For example:

<?code-excerpt "misc/test/language_tour/browser_test.dart (package-import)"?>
```dart
import 'package:test/test.dart';
```

{{site.alert.note}}
  *URI* stands for uniform resource identifier.
  *URLs* (uniform resource locators) are a common kind of URI.
{{site.alert.end}}


#### Specifying a library prefix

If you import two libraries that have conflicting identifiers, then you
can specify a prefix for one or both libraries. For example, if library1
and library2 both have an Element class, then you might have code like
this:

<?code-excerpt "misc/lib/language_tour/libraries/import_as.dart" replace="/(lib\d)\.dart/package:$1\/$&/g"?>
```dart
import 'package:lib1/lib1.dart';
import 'package:lib2/lib2.dart' as lib2;

// Uses Element from lib1.
Element element1 = Element();

// Uses Element from lib2.
lib2.Element element2 = lib2.Element();
```

#### Importing only part of a library

If you want to use only part of a library, you can selectively import
the library. For example:

<?code-excerpt "misc/lib/language_tour/libraries/show_hide.dart" replace="/(lib\d)\.dart/package:$1\/$&/g"?>
```dart
// Import only foo.
import 'package:lib1/lib1.dart' show foo;

// Import all names EXCEPT foo.
import 'package:lib2/lib2.dart' hide foo;
```

<a id="deferred-loading"></a>
#### Lazily loading a library

_Deferred loading_ (also called _lazy loading_)
allows a web app to load a library on demand,
if and when the library is needed.
Here are some cases when you might use deferred loading:

* To reduce a web app's initial startup time.
* To perform A/B testing—trying out
  alternative implementations of an algorithm, for example.
* To load rarely used functionality, such as optional screens and dialogs.

{{site.alert.warn}}
  **Only `dart compile js` supports deferred loading.**
  Flutter and the Dart VM don't support deferred loading.
  To learn more, see
  [issue #33118](https://github.com/dart-lang/sdk/issues/33118) and
  [issue #27776.](https://github.com/dart-lang/sdk/issues/27776)
{{site.alert.end}}

To lazily load a library, you must first
import it using `deferred as`.

<?code-excerpt "misc/lib/language_tour/libraries/greeter.dart (import)" replace="/hello\.dart/package:greetings\/$&/g"?>
```dart
import 'package:greetings/hello.dart' deferred as hello;
```

When you need the library, invoke
`loadLibrary()` using the library's identifier.

<?code-excerpt "misc/lib/language_tour/libraries/greeter.dart (loadLibrary)"?>
```dart
Future<void> greet() async {
  await hello.loadLibrary();
  hello.printGreeting();
}
```

In the preceding code,
the `await` keyword pauses execution until the library is loaded.
For more information about `async` and `await`,
see [asynchrony support](#asynchrony-support).

You can invoke `loadLibrary()` multiple times on a library without problems.
The library is loaded only once.

Keep in mind the following when you use deferred loading:

* A deferred library's constants aren't constants in the importing file.
  Remember, these constants don't exist until the deferred library is loaded.
* You can't use types from a deferred library in the importing file.
  Instead, consider moving interface types to a library imported by
  both the deferred library and the importing file.
* Dart implicitly inserts `loadLibrary()` into the namespace that you define
  using <code>deferred as <em>namespace</em></code>.
  The `loadLibrary()` function returns a [`Future`](/guides/libraries/library-tour#future).


### Implementing libraries

See
[Create Library Packages](/guides/libraries/create-library-packages)
for advice on how to implement a library package, including:

* How to organize library source code.
* How to use the `export` directive.
* When to use the `part` directive.
* When to use the `library` directive.
* How to use conditional imports and exports to implement
  a library that supports multiple platforms.


<a id="asynchrony"></a>
## Asynchrony support

Dart libraries are full of functions that
return [`Future`][] or [`Stream`][] objects.
These functions are _asynchronous_:
they return after setting up
a possibly time-consuming operation
(such as I/O),
without waiting for that operation to complete.

The `async` and `await` keywords support asynchronous programming,
letting you write asynchronous code that
looks similar to synchronous code.


<a id="await"></a>
### Handling Futures

When you need the result of a completed Future,
you have two options:

* Use `async` and `await`, as described here and in the
  [asynchronous programming codelab](/codelabs/async-await).
* Use the Future API, as described
  [in the library tour](/guides/libraries/library-tour#future).

Code that uses `async` and `await` is asynchronous,
but it looks a lot like synchronous code.
For example, here's some code that uses `await`
to wait for the result of an asynchronous function:

<?code-excerpt "misc/lib/language_tour/async.dart (await-lookUpVersion)"?>
```dart
await lookUpVersion();
```

To use `await`, code must be in an `async` function—a
function marked as `async`:

<?code-excerpt "misc/lib/language_tour/async.dart (checkVersion)" replace="/async|await/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
Future<void> checkVersion() [!async!] {
  var version = [!await!] lookUpVersion();
  // Do something with version
}
{% endprettify %}

{{site.alert.note}}
  Although an `async` function might perform time-consuming operations, 
  it doesn't wait for those operations. 
  Instead, the `async` function executes only
  until it encounters its first `await` expression.
  Then it returns a `Future` object,
  resuming execution only after the `await` expression completes.
{{site.alert.end}}

Use `try`, `catch`, and `finally` to handle errors and cleanup
in code that uses `await`:

<?code-excerpt "misc/lib/language_tour/async.dart (try-catch)"?>
```dart
try {
  version = await lookUpVersion();
} catch (e) {
  // React to inability to look up the version
}
```

You can use `await` multiple times in an `async` function.
For example, the following code waits three times
for the results of functions:

<?code-excerpt "misc/lib/language_tour/async.dart (repeated-await)"?>
```dart
var entrypoint = await findEntryPoint();
var exitCode = await runExecutable(entrypoint, args);
await flushThenExit(exitCode);
```

In <code>await <em>expression</em></code>,
the value of <code><em>expression</em></code> is usually a Future;
if it isn't, then the value is automatically wrapped in a Future.
This Future object indicates a promise to return an object.
The value of <code>await <em>expression</em></code> is that returned object.
The await expression makes execution pause until that object is available.

**If you get a compile-time error when using `await`,
make sure `await` is in an `async` function.**
For example, to use `await` in your app's `main()` function,
the body of `main()` must be marked as `async`:

<?code-excerpt "misc/lib/language_tour/async.dart (main)" replace="/async|await/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
void main() [!async!] {
  checkVersion();
  print('In main: version is ${[!await!] lookUpVersion()}');
}
{% endprettify %}

{{site.alert.note}}
  The preceding example uses an `async` function (`checkVersion()`)
  without waiting for a result—a practice that can cause problems
  if the code assumes that the function has finished executing.
  To avoid this problem,
  use the [unawaited_futures linter rule][].
{{site.alert.end}}

[unawaited_futures linter rule]: /tools/linter-rules#unawaited_futures

For an interactive introduction to using futures, `async`, and `await`,
see the [asynchronous programming codelab](/codelabs/async-await).


<a id="async"></a>
### Declaring async functions

An `async` function is a function whose body is marked with
the `async` modifier.

Adding the `async` keyword to a function makes it return a Future.
For example, consider this synchronous function,
which returns a String:

<?code-excerpt "misc/lib/language_tour/async.dart (sync-lookUpVersion)"?>
```dart
String lookUpVersion() => '1.0.0';
```

If you change it to be an `async` function—for example,
because a future implementation will be time consuming—the
returned value is a Future:

<?code-excerpt "misc/lib/language_tour/async.dart (async-lookUpVersion)"?>
```dart
Future<String> lookUpVersion() async => '1.0.0';
```

Note that the function's body doesn't need to use the Future API.
Dart creates the Future object if necessary.
If your function doesn't return a useful value,
make its return type `Future<void>`.

For an interactive introduction to using futures, `async`, and `await`,
see the [asynchronous programming codelab](/codelabs/async-await).

{% comment %}
TODO #1117: Where else should we cover generalized void?
{% endcomment %}


<a id="await-for"></a>
### Handling Streams

When you need to get values from a Stream,
you have two options:

* Use `async` and an _asynchronous for loop_ (`await for`).
* Use the Stream API, as described
  [in the library tour](/guides/libraries/library-tour#stream).

{{site.alert.note}}
  Before using `await for`, be sure that it makes the code clearer and that you
  really do want to wait for all of the stream's results. For example, you
  usually should **not** use `await for` for UI event listeners, because UI
  frameworks send endless streams of events.
{{site.alert.end}}

An asynchronous for loop has the following form:

<?code-excerpt "misc/lib/language_tour/async.dart (await-for)"?>
```dart
await for (varOrType identifier in expression) {
  // Executes each time the stream emits a value.
}
```

The value of <code><em>expression</em></code> must have type Stream.
Execution proceeds as follows:

1. Wait until the stream emits a value.
2. Execute the body of the for loop,
   with the variable set to that emitted value.
3. Repeat 1 and 2 until the stream is closed.

To stop listening to the stream,
you can use a `break` or `return` statement,
which breaks out of the for loop
and unsubscribes from the stream.

**If you get a compile-time error when implementing an asynchronous for loop,
make sure the `await for` is in an `async` function.**
For example, to use an asynchronous for loop in your app's `main()` function,
the body of `main()` must be marked as `async`:

<?code-excerpt "misc/lib/language_tour/async.dart (number_thinker)" replace="/async|await for/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
void main() [!async!] {
  // ...
  [!await for!] (final request in requestServer) {
    handleRequest(request);
  }
  // ...
}
{% endprettify %}

For more information about asynchronous programming, in general, see the
[dart:async](/guides/libraries/library-tour#dartasync---asynchronous-programming)
section of the library tour.


<a id="generator"></a>
## Generators

When you need to lazily produce a sequence of values,
consider using a _generator function_.
Dart has built-in support for two kinds of generator functions:

* **Synchronous** generator: Returns an [`Iterable`] object.
* **Asynchronous** generator: Returns a [`Stream`] object.

To implement a **synchronous** generator function,
mark the function body as `sync*`,
and use `yield` statements to deliver values:

<?code-excerpt "misc/test/language_tour/async_test.dart (sync-generator)"?>
```dart
Iterable<int> naturalsTo(int n) sync* {
  int k = 0;
  while (k < n) yield k++;
}
```

To implement an **asynchronous** generator function,
mark the function body as `async*`,
and use `yield` statements to deliver values:

<?code-excerpt "misc/test/language_tour/async_test.dart (async-generator)"?>
```dart
Stream<int> asynchronousNaturalsTo(int n) async* {
  int k = 0;
  while (k < n) yield k++;
}
```

If your generator is recursive,
you can improve its performance by using `yield*`:

<?code-excerpt "misc/test/language_tour/async_test.dart (recursive-generator)"?>
```dart
Iterable<int> naturalsDownFrom(int n) sync* {
  if (n > 0) {
    yield n;
    yield* naturalsDownFrom(n - 1);
  }
}
```

## Callable classes

To allow an instance of your Dart class to be called like a function,
implement the `call()` method.

The `call()` method allows any class that defines it to emulate a function.
This method supports the same functionality as normal [functions](#functions)
such as parameters and return types.

In the following example, the `WannabeFunction` class defines a `call()` function
that takes three strings and concatenates them, separating each with a space,
and appending an exclamation. Click **Run** to execute the code.

<?code-excerpt "misc/lib/language_tour/callable_classes.dart"?>
```dart:run-dartpad:height-350px:ga_id-callable_classes
class WannabeFunction {
  String call(String a, String b, String c) => '$a $b $c!';
}

var wf = WannabeFunction();
var out = wf('Hi', 'there,', 'gang');

void main() => print(out);
```

## Isolates

Most computers, even on mobile platforms, have multi-core CPUs.
To take advantage of all those cores, developers traditionally use
shared-memory threads running concurrently. However, shared-state
concurrency is error prone and can lead to complicated code.

Instead of threads, all Dart code runs inside of *isolates*. 
Each Dart isolate uses a single thread of execution and
shares no mutable objects with other isolates.
Spinning up multiple isolates creates multiple threads of execution.
This enables multi-threading without its primary drawback,
[race conditions](https://en.wikipedia.org/wiki/Race_condition#In_software)

For more information, see the following:
* [Concurrency in Dart](/guides/language/concurrency)
* [dart:isolate API reference,][dart:isolate]
  including [Isolate.spawn()][] and
  [TransferableTypedData][]
* [Background parsing][background json] cookbook on the Flutter site
* [Isolate sample app][]

[Isolate.spawn()]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-isolate/Isolate/spawn.html
[TransferableTypedData]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-isolate/TransferableTypedData-class.html
[background json]: {{site.flutter-docs}}/cookbook/networking/background-parsing
[Isolate sample app]: https://github.com/flutter/samples/tree/main/isolate_example


## Typedefs

A type alias—often called a _typedef_ because
it's declared with the keyword `typedef`—is
a concise way to refer to a type.
Here's an example of declaring and using a type alias named `IntList`:

<?code-excerpt "misc/lib/language_tour/typedefs/misc.dart (int-list)"?>
```dart
typedef IntList = List<int>;
IntList il = [1, 2, 3];
```

A type alias can have type parameters:

<?code-excerpt "misc/lib/language_tour/typedefs/misc.dart (list-mapper)"?>
```dart
typedef ListMapper<X> = Map<X, List<X>>;
Map<String, List<String>> m1 = {}; // Verbose.
ListMapper<String> m2 = {}; // Same thing but shorter and clearer.
```

{{site.alert.version-note}}
  Before 2.13, typedefs were restricted to function types.
  Using the new typedefs requires a [language version][] of at least 2.13.
{{site.alert.end}}

We recommend using [inline function types][] instead of typedefs for functions,
in most situations.
However, function typedefs can still be useful:

<?code-excerpt "misc/lib/language_tour/typedefs/misc.dart (compare)"?>
```dart
typedef Compare<T> = int Function(T a, T b);

int sort(int a, int b) => a - b;

void main() {
  assert(sort is Compare<int>); // True!
}
```

[typedef-functions]: /guides/language/effective-dart/design#dont-use-the-legacy-typedef-syntax
[inline function types]: /guides/language/effective-dart/design#prefer-inline-function-types-over-typedefs


## Metadata

Use metadata to give additional information about your code. A metadata
annotation begins with the character `@`, followed by either a reference
to a compile-time constant (such as `deprecated`) or a call to a
constant constructor.

Three annotations are available to all Dart code: 
`@Deprecated`, `@deprecated`, and `@override`. 
For examples of using `@override`,
see [Extending a class](#extending-a-class).
Here’s an example of using the `@Deprecated` annotation:

<?code-excerpt "misc/lib/language_tour/metadata/television.dart (deprecated)" replace="/@Deprecated.*/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
class Television {
  /// Use [turnOn] to turn the power on instead.
  [!@Deprecated('Use turnOn instead')!]
  void activate() {
    turnOn();
  }

  /// Turns the TV's power on.
  void turnOn() {...}
  // ···
}
{% endprettify %}

You can define your own metadata annotations. Here’s an example of
defining a `@Todo` annotation that takes two arguments:

<?code-excerpt "misc/lib/language_tour/metadata/todo.dart"?>
```dart
class Todo {
  final String who;
  final String what;

  const Todo(this.who, this.what);
}
```

And here’s an example of using that `@Todo` annotation:

<?code-excerpt "misc/lib/language_tour/metadata/misc.dart"?>
```dart
@Todo('Dash', 'Implement this function')
void doSomething() {
  print('Do something');
}
```

Metadata can appear before a library, class, typedef, type parameter,
constructor, factory, function, field, parameter, or variable
declaration and before an import or export directive. You can
retrieve metadata at runtime using reflection.


## Comments

Dart supports single-line comments, multi-line comments, and
documentation comments.


### Single-line comments

A single-line comment begins with `//`. Everything between `//` and the
end of line is ignored by the Dart compiler.

<?code-excerpt "misc/lib/language_tour/comments.dart (single-line-comments)"?>
```dart
void main() {
  // TODO: refactor into an AbstractLlamaGreetingFactory?
  print('Welcome to my Llama farm!');
}
```


### Multi-line comments

A multi-line comment begins with `/*` and ends with `*/`. Everything
between `/*` and `*/` is ignored by the Dart compiler (unless the
comment is a documentation comment; see the next section). Multi-line
comments can nest.

<?code-excerpt "misc/lib/language_tour/comments.dart (multi-line-comments)"?>
```dart
void main() {
  /*
   * This is a lot of work. Consider raising chickens.

  Llama larry = Llama();
  larry.feed();
  larry.exercise();
  larry.clean();
   */
}
```


### Documentation comments

Documentation comments are multi-line or single-line comments that begin
with `///` or `/**`. Using `///` on consecutive lines has the same
effect as a multi-line doc comment.

Inside a documentation comment, the analyzer ignores all text
unless it is enclosed in brackets. Using brackets, you can refer to
classes, methods, fields, top-level variables, functions, and
parameters. The names in brackets are resolved in the lexical scope of
the documented program element.

Here is an example of documentation comments with references to other
classes and arguments:

<?code-excerpt "misc/lib/language_tour/comments.dart (doc-comments)"?>
```dart
/// A domesticated South American camelid (Lama glama).
///
/// Andean cultures have used llamas as meat and pack
/// animals since pre-Hispanic times.
///
/// Just like any other animal, llamas need to eat,
/// so don't forget to [feed] them some [Food].
class Llama {
  String? name;

  /// Feeds your llama [food].
  ///
  /// The typical llama eats one bale of hay per week.
  void feed(Food food) {
    // ...
  }

  /// Exercises your llama with an [activity] for
  /// [timeLimit] minutes.
  void exercise(Activity activity, int timeLimit) {
    // ...
  }
}
```

In the class's generated documentation, `[feed]` becomes a link
to the docs for the `feed` method,
and `[Food]` becomes a link to the docs for the `Food` class.

To parse Dart code and generate HTML documentation, you can use Dart's
documentation generation tool, [`dart doc`](/tools/dart-doc).
For an example of generated documentation, see the 
[Dart API documentation.]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}) 
For advice on how to structure your comments, see
[Effective Dart: Documentation.](/guides/language/effective-dart/documentation)


## Summary

This page summarized the commonly used features in the Dart language.
More features are being implemented, but we expect that they won’t break
existing code. For more information, see the [Dart 언어 설명서][] and
[Effective Dart](/guides/language/effective-dart).

To learn more about Dart's core libraries, see
[A Tour of the Dart Libraries](/guides/libraries/library-tour).

[`AssertionError`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/AssertionError-class.html
[`Characters`]: {{site.pub-api}}/characters/latest/characters/Characters-class.html
[characters API]: {{site.pub-api}}/characters
[characters example]: {{site.pub-pkg}}/characters/example
[characters package]: {{site.pub-pkg}}/characters
[`dart run`]: /tools/dart-run
[dart:html]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-html
[dart:isolate]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-isolate
[dart:math]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-math
[Dart 언어 설명서]: /guides/language/spec
[dartdevc]: /tools/dartdevc
[const를 중복으로 사용하지 마십시오]: /guides/language/effective-dart/usage#dont-use-const-redundantly
[`double`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/double-class.html
[`Enum`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Enum-class.html
[`Error`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Error-class.html
[`Exception`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Exception-class.html
[extension methods page]: /guides/language/extension-methods
[Flutter]: {{site.flutter}}
[Flutter debug mode]: {{site.flutter-docs}}/testing/debugging#debug-mode-assertions
[forEach()]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Iterable/forEach.html
[Function API reference]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Function-class.html
[`Future`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-async/Future-class.html
[grapheme clusters]: https://unicode.org/reports/tr29/#Grapheme_Cluster_Boundaries
[identical()]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/identical.html
[`int`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/int-class.html
[`Iterable`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Iterable-class.html
[iteration]: /guides/libraries/library-tour#iteration
[js numbers]: https://stackoverflow.com/questions/2802957/number-of-bits-in-javascript-numbers/2803010#2803010
[language version]: /guides/language/evolution#language-versioning
[late-final-ivar]: /guides/language/effective-dart/design#avoid-public-late-final-fields-without-initializers
[`List`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/List-class.html
[`Map`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Map-class.html
[meta]: {{site.pub-pkg}}/meta
[ns]: /null-safety
[ns-enable]: /null-safety#enable-null-safety
[`num`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/num-class.html
[`Object`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Object-class.html
[ObjectVsDynamic]: /guides/language/effective-dart/design#avoid-using-dynamic-unless-you-want-to-disable-static-checking
[runes]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Runes-class.html
[`Set`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Set-class.html
[`StackTrace`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/StackTrace-class.html
[`Stream`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-async/Stream-class.html
[`String`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/String-class.html
[`Symbol`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Symbol-class.html
[top-and-bottom]: /null-safety/understanding-null-safety#top-and-bottom
[trailing commas]: #trailing-comma
[type test operator]: #type-test-operators
[`Type`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Type-class.html
[Understanding null safety]: /null-safety/understanding-null-safety
