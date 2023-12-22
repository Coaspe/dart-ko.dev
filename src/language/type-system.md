---
title: Dart 타입 시스템
description: 타입 안전성을 지키는 Dart 코드를 작성하는 이유와 방법.
prevpage:
  url: /language/typedefs
  title: Typedefs
nextpage:
  url: /language/patterns
  title: Patterns
---
<?code-excerpt replace="/ *\/\/\s+ignore_for_file:[^\n]+\n//g; /([A-Z]\w*)\d\b/$1/g; /\b(main)\d\b/$1/g; /(^|\n) *\/\/\s+ignore:[^\n]+\n/$1/g; /(\n[^\n]+) *\/\/\s+ignore:[^\n]+\n/$1\n/g"?>
<?code-excerpt path-base="type_system"?>

Dart는 타입이 안전한 프로그래밍 언어입니다: Dart는 변수값이 항상 변수의 정적 또는 안전한 타입과
일치하는지 확인하기 위해 정적 타입 검사와 [런타임 검사](#런타임-검사)를 사용합니다.
_타입_ 은 필수적이지만, [타입 추론](#타입-추론) 덕분에
타입 _어노테이션_ 은 선택적입니다.

정적 타입 체킹의 장점 중 하나는 Dart의 [static analyzer][analysis]를
사용하여 컴파일 타임에 버그를 찾을 수 있다는 것 입니다.

예를 들어, 아래 코드에서 `main()`은 리스트를 생성하고, 이를 정수 리스트로 출력하는
`printInts()` 함수에 전달합니다.

{:.fails-sa}
<?code-excerpt "lib/strong_analysis.dart (opening-example)" replace="/list(?=\))/[!$&!]/g"?>
```dart
void printInts(List<int> a) => print(a);

void main() {
  final list = [];
  list.add(1);
  list.add('2');
  printInts([!list!]);
}
```

위의 코드는 `printInts(list)`를 호출할 때 `list`에 대해
타입 에러를 발생시킵니다 (강조 표시됨):

{:.console-output}
<?code-excerpt "analyzer-results-stable.txt" retain="/strong_analysis.*List.*argument_type_not_assignable/" replace="/-(.*?):(.*?):(.*?)-/-/g; /. • (lib|test)\/\w+\.dart:\d+:\d+//g"?>
```nocode
error - The argument type 'List<dynamic>' can't be assigned to the parameter type 'List<int>'. - argument_type_not_assignable
```

하이라이트된 에러는 `list<dynamic>`에서 `List<int>`로의 잘못된 암묵적 변환 때문입니다.
`list` 변수는 `List<dynamic>`을 정적 타입으로 가집니다. 이는 변수의 초기화 선언인
`var list = []` 가 analyzer에게 `dynamic` 보다 더 구체적인 타입 매개변수를
유추할 수 있는 충분한 정보를 제공하지 않기 때문입니다. `printInts()` 함수는 `List<int>`
타입의 인수를 예상하므로 타입의 불일치가 발생합니다.

리스트를 생성할 때 타입 어노테이션 `<int>` (코드에 하이라이트된 부분)을 추가하면
analyzer가 문자열 인수를 `int`형 매개변수에 할당할 수 없다는 메시지를 표시합니다.
`list.add('2')`에서 문자열 따옴표를 제거하면 코드가 정적 분석을 통과하고
정상적으로 실행됩니다.

{:.passes-sa}
<?code-excerpt "test/strong_test.dart (opening-example)" replace="/<int.(?=\[)|2/[!$&!]/g"?>
```dart
void printInts(List<int> a) => print(a);

void main() {
  final list = [!<int>!][];
  list.add(1);
  list.add([!2!]);
  printInts(list);
}
```

[DartPad에서 체험해보세요!]({{site.dartpad}}/25074a51a00c71b4b000f33b688dedd0).

## 타입 안전성(soundness)이란?

*타입 안전성(soundness)*이란 프로그램이 유효하지 않는 상태가 되지 않도록 하는 것입니다.
안전한 *타입 시스템*은 프로그램이 표현식이 표현식의 정적 타입과 일치하지 않는 값으로
평가되는 상태에 진입할 수 없음을 의미합니다. 예를 들어 표현식의 정적 유형이 `String`인
경우 평가할 때 문자열만 얻을 수 있도록 런타임에서 보장됩니다.

Java나 C#의 타입 시스템 처럼 Dart의 타입 시스템도 안전합니다.
Dart는 정적 검사 (컴파일 타임 에러)와 런타임 검사를 조합하여 타입 안전성을 강요합니다.
예를 들어 `String`을 `int`에 할당하는 것은 컴파일 타입 에러입니다. 객체가 `String`이 아닌 경우
`as String`을 사용하여 객체를 `String`으로 변환하면 런타임 에러가 발생하여 실패합니다.


## 타입 안전성의 이점

안전한 타입 시스템은 많은 장점을 가지고 있습니다:

* 타입 관련 버그를 컴파일 타임에 찾을 수 있습니다.<br>
  안전한 타입 시스템은 코드의 타입이 애매하지 않도록 강제하므로,
  런타임에 발견하기 어려울 수 있는 타입 관련 버그를 컴파일 타임에 찾아줍니다.

* 코드의 가독성이 높아집니다.<br>
  코드를 작성할 때 특정 타입을 가지는 값에 의존하게 되기 때문에, 가독성이 높아집니다.
  타입이 안전한 Dart에서 타입은 거짓말을 하지 않습니다.

* 코드의 유지관리가 쉬워집니다.<br>
  안전한 타입 시스템은 코드를 수정할 때 영향을 받는 다른 코드에 대해 경고해줍니다.

* 더 나은 AOT(Ahead-Of-Time) 컴파일을 제공합니다.<br>
  AOT 컴파일은 타입 없이도 가능하지만, 생성된 코드는 훨씬 비효율적입니다.


## 정적 검사를 위한 팁

정적 타이핑 규칙은 대부분 이해하기 쉽습니다.
다음은 몇 가지 애매한 규칙들 입니다:

* 메서드를 재정의할 때 타입이 안전한 반환 값을 사용하세요.
* 메서드를 재정의할 때 타입이 안전한 매개변수를 사용하세요.
* 동적으로 타입이 지정된 List를 타입이 지정된 List 처럼 사용하지 마세요.

다음의 타입 계층을 사용하여 이러한 규칙에 대해 자세히 살펴봅시다:

<img src="/assets/img/language/type-hierarchy.png" alt="a hierarchy of animals where the supertype is Animal and the subtypes are Alligator, Cat, and HoneyBadger. Cat has the subtypes of Lion and MaineCoon">

<a name="use-proper-return-types"></a>

### 메서드를 재정의할 때 타입이 안전한 반환 값을 사용하세요.

자식 클래스 메서드의 반환 값의 타입은 부모 클래스의 메서드 또는 해당 서브타입의 반환 값의 타입과 동일해야 합니다.
`Animal` 클래스의 getter 메서드를 다음과 같이 생성합니다:

<?code-excerpt "lib/animal.dart (Animal)" replace="/Animal get.*/[!$&!]/g"?>
```dart
class Animal {
  void chase(Animal a) { ... }
  [!Animal get parent => ...!]
}
```

`부모`의 getter 메서드는 `Animal`을 반환합니다. 자식 클래스인 `HoneyBadger`의 getter 메서드의 반환 타입을
`HoneyBadger` 또는 `Animal`의 다른 서브타입으로 대체할 수 있지만, 관련이 없는 타입으로는 불가능합니다.

{:.passes-sa}
<?code-excerpt "lib/animal.dart (HoneyBadger)" replace="/(\w+)(?= get)/[!$&!]/g"?>
```dart
class HoneyBadger extends Animal {
  @override
  void chase(Animal a) { ... }

  @override
  [!HoneyBadger!] get parent => ...
}
```

{:.fails-sa}
<?code-excerpt "lib/animal.dart (HoneyBadger)" replace="/HoneyBadger get/[!Root!] get/g"?>
```dart
class HoneyBadger extends Animal {
  @override
  void chase(Animal a) { ... }

  @override
  [!Root!] get parent => ...
}
```

<a name="use-proper-param-types"></a>

### 메서드를 재정의할 때 타입이 안전한 매개변수를 사용하세요.

자식 클래스 메서드의 매개변수는 부모 클래스 메서드의 매개변수 또는 해당 매개변수의
부모 타입과 동일한 타입이어야 합니다. 원래의 매개변수 타입을 대체하기 위해 서브타입을 사용하지 마세요.
그러면 매개변수 타입이 "좁아"집니다.

{{site.alert.note}}
  합당한 이유로 서브타입을 사용해야 한다면,  [`covariant` 키워드]
  (/guides/language/sound-problems#the-covariant-keyword)의 사용을 고려해보세요.
{{site.alert.end}}

`Animal` 클래스의 `chase(Animal)` 메서드를 다음과 같이 작성합니다:

<?code-excerpt "lib/animal.dart (Animal)" replace="/void chase.*/[!$&!]/g"?>
```dart
class Animal {
  [!void chase(Animal a) { ... }!]
  Animal get parent => ...
}
```

`chase()` 메서드의 매개변수 타입은 `Animal` 입니다. `HoneyBadger`는 어느 것이든 선택할 수 있습니다.
따라서 `chase()` 메서드를 재정의할 때 매개변수 타입을 어떤 타입의 `Object`로도 지정할 수 있습니다.

{:.passes-sa}
<?code-excerpt "lib/animal.dart (chase-Object)" replace="/Object/[!$&!]/g"?>
```dart
class HoneyBadger extends Animal {
  @override
  void chase([!Object!] a) { ... }

  @override
  Animal get parent => ...
}
```

`Mouse`는 `Animal`의 하위 클래스이며 아래 코드는 `chase()` 메서드의 매개변수 범위를
`Animal`에서 `Mouse`로 좁힙니다.

{:.fails-sa}
<?code-excerpt "lib/incorrect_animal.dart (chase-mouse)" replace="/Mouse/[!$&!]/g"?>
```dart
class [!Mouse!] extends Animal { ... }

class Cat extends Animal {
  @override
  void chase([!Mouse!] a) { ... }
}
```

다음 코드는 a를 고양이 객체로 선언하고, 악어 객체를 넘겨줄 수 있기 때문에 타입이 안전하지 않습니다:

<?code-excerpt "lib/incorrect_animal.dart (would-not-be-type-safe)" replace="/Alligator/[!$&!]/g"?>
```dart
Animal a = Cat();
a.chase([!Alligator!]()); // 타입과 고양이가 안전하지 않습니다.
```

### 동적으로 타입이 지정된 리스트를 타입이 지정된 리스트처럼 사용하지 마세요.

`dynamic` 리스트는 다른 타입의 요소들을 한 리스트에 추가할 때 유용합니다.
그러나, `dynamic` 리스트를 타입이 지정된 리스트처럼 사용할 수 없습니다.

이 규칙은 제네릭 타입의 인스턴스에도 적용됩니다.

다음 코드는 `Dog` 객체를 가지는 `dynamic` 리스트를 생성하고
해당 리스트를 `Cat` 타입의 리스트에 할당합니다. 이 코드는 정적 분석에서 에러를 발생시킵니다.

{:.fails-sa}
<?code-excerpt "lib/incorrect_animal.dart (invalid-dynamic-list)" replace="/(<dynamic\x3E)(.*?)Error/[!$1!]$2Error/g"?>
```dart
void main() {
  List<Cat> foo = [!<dynamic>!][Dog()]; // Error
  List<dynamic> bar = <dynamic>[Dog(), Cat()]; // OK
}
```

## 런타임 검사

런타임 검사는 컴파일 타임에서 감지하지 못하는 타입 안전성 이슈를 처리합니다.

예를 들어, 다음 코드는 강아지의 리스트를 고양이의 리스트로 캐스팅하는 것은 에러이므로
런타임에 예외를 발생시킵니다:

{:.runtime-fail}
<?code-excerpt "test/strong_test.dart (runtime-checks)" replace="/animals as[^;]*/[!$&!]/g"?>
```dart
void main() {
  List<Animal> animals = [Dog()];
  List<Cat> cats = [!animals as List<Cat>!];
}
```


## 타입 추론

Analyzer는 필드, 메서드, 지역 변수와 대부분의 제네릭 타입 인자를 추론합니다.
Analyzer가 특정 타입을 추론할 만큼 충분한 정보가 없다면, `dynamic` 타입을 사용합니다.

다음은 타입 추론이 제네릭에 작동하는 예제입니다.
이 예제에서 `arguments`라는 변수는 문자열 키와 여러가지 타입의 값의 쌍을
가지는 map을 홀드합니다.

명시적으로 변수의 타입을 지정하려면, 다음과 같이 작성하면 됩니다:

<?code-excerpt "lib/strong_analysis.dart (type-inference-1-orig)" replace="/Map<String, dynamic\x3E/[!$&!]/g"?>
```dart
[!Map<String, dynamic>!] arguments = {'argA': 'hello', 'argB': 42};
```

`var` 또는 `final`을 사용하여 Dart가 타입을 추론하도록 할 수 있습니다:

<?code-excerpt "lib/strong_analysis.dart (type-inference-1)" replace="/var/[!$&!]/g"?>
```dart
[!var!] arguments = {'argA': 'hello', 'argB': 42}; // Map<String, Object>
```

Map 리터럴은 자신의 엔트리를 참고하여 타입을 추론하고
변수는 map 리터럴의 타입을 참고하여 자신의 타입을 추론합니다.
이 map에서 키는 모두 문자열이지만 값은 다른 타입
(같은 상한(upper bound) 타입인 `Object`를 갖는 `String`과 `int`)입니다.
따라서 Map 리터럴의 타입은 `arguments` 변수의 타입인 `Map<String, Object>`입니다.


### 필드 및 메서드 추론

타입을 지정하지 않고 부모 클래스의 필드 또는 메서드를 재정의하고 부모 클래스의
필드 또는 메서드의 타입을 상속합니다.

선언된 타입과 상속된 타입이 없는 필드가 존재하는 경우 선언 시점에 초기화되면,
필드의 추론된 타입은 초기화에 사용한 값의 타입입니다.

### 정적 필드 추론

정적 필드와 변수의 타입은 해당 변수의 initializer에서 유추됩니다.
추론이 루프를 형성하는 경우 추론이 실패합니다.
(변수의 타입을 아는 것이 변수의 타입을 추론하는 데 달려 있다는 의미)

### 지역 변수 추론

지역 변수 타입은 해당 변수의 initializer가 있는 경우에는 initializer에서 유추됩니다.
후속 할당은 고려되지 않습니다. 이런 식으로 추론된 타입은 엄격할 수 있습니다.
그렇다면 타입 어노테이션을 추가하세요.
Local variable types are inferred from their initializer, if any.
Subsequent assignments are not taken into account.
This may mean that too precise a type may be inferred.
If so, you can add a type annotation.

{:.fails-sa}
<?code-excerpt "lib/strong_analysis.dart (local-var-type-inference-error)"?>
```dart
var x = 3; // x는 int로 추론됩니다.
x = 4.0;
```

{:.passes-sa}
<?code-excerpt "lib/strong_analysis.dart (local-var-type-inference-ok)"?>
```dart
num y = 3; //  num는 double 또는 int가 될 수 있습니다.
y = 4.0;
```

### 매개변수 타입 추론

생성자 호출 및 [제네릭 메서드](/language/generics#제네릭-메서드-사용)
호출의 형식 매개변수는 컨텍스트의 하향 정보와 생성자 또는 제네릭 메서드의 매개변수에 대한 상향 정보를 조합하여 추론됩니다.
추론이 의도한 대로 또는 예상대로 작동하지 않는 경우 해당 매개변수의 타입을 명시적으로 지정할 수 있습니다.

{:.passes-sa}
<?code-excerpt "lib/strong_analysis.dart (type-arg-inference)"?>
```dart
// <int>[]로 추론됩니다.
List<int> listOfInt = [];

// <double>[3.0]로 추론됩니다.
var listOfDouble = [3.0];

// Iterable<int>로 추론됩니다.
var ints = listOfDouble.map((x) => x.toInt());
```

마지막 예제에서 `x`는 하향 정보를 사용하여 `double`로 추론됩니다.
클로저의 반환 타입은 상향 정보를 사용하여 `int`로 추론됩니다.
Dart는 `map()` 메서드의 타입 매개변수 `<int>`를 추론할 때 이 반환 값의 타입을 상향 정보로 사용합니다.


## 대체 타입

메서드를 재정의할 때, 이전 타입(이전 메서드)을 새로운 타입(새 메서드)으로 바꿀 수 있습니다.
마찬가지로 인수가 함수에 전달될 때 다른 타입의 객체(실제 인수)를 사용하여 기존 타입의 객체
(선언된 타입의 인수)를 대체할 수 있습니다. 언제 한 타입의 객체를 하위 타입 또는 상위 타입
의 객체로 대체할 수 있을까요?

타입을 대체할 때, _소비자_ 와 _생산자_ 의 관점에서 생각하면 이해가 쉽습니다.
소비자는 타입을 사용하고 생산자는 타입을 생성합니다.

**소비자의 타입을 상위 타입으로 생산자 타입을 하위 타입으로 바꿀 수 있습니다.**

아래의 제네릭 타입 할당 및 제네릭 타입 할당의 예제를 살펴봅시다.

### 일반 타입 할당

객체에 객체를 할당할 때, 언제 현재 타입을 다른 타입으로 대체할 수 있을까요?
답은 객체가 소비자인지 생산자인지에 따라 다릅니다.

다음 타입 계층을 살펴봅시다:

<img src="/assets/img/language/type-hierarchy.png" alt="a hierarchy of animals where the supertype is Animal and the subtypes are Alligator, Cat, and HoneyBadger. Cat has the subtypes of Lion and MaineCoon">

`Cat c`가 _소비자_ 이고 `Cat()`이 _생산자_ 인 다음 예제의 일반 할당을 살펴봅시다:

<?code-excerpt "lib/strong_analysis.dart (Cat-Cat-ok)"?>
```dart
Cat c = Cat();
```

소비자 입장에서는 특정 타입(`Cat`)의 객체를 모든 타입(`Animal`)의 객체로 바꾸는 것이 안전합니다.
`Animal`이 `Cat`의 상위 클래스이므로 `Cat c`를 `Animal c`로 바꾸는 것이 가능합니다.

{:.passes-sa}
<?code-excerpt "lib/strong_analysis.dart (Animal-Cat-ok)"?>
```dart
Animal c = Cat();
```

하지만 `Cat c`를 `MaineCoon c`로 대체하는 것은 부모 클래스가
`Lion` 같은 Cat 타입을 제공하므로 안전성을 훼손시킵니다:

{:.fails-sa}
<?code-excerpt "lib/strong_analysis.dart (MaineCoon-Cat-err)"?>
```dart
MaineCoon c = Cat();
```

생산자의 입장에서, `Cat` 타입을 생산하는 것보다
구체적인 타입(`MaineCoon`)으로 대체하는 것이 보다 안전합니다.
그러므로 다음이 가능합니다:

{:.passes-sa}
<?code-excerpt "lib/strong_analysis.dart (Cat-MaineCoon-ok)"?>
```dart
Cat c = MaineCoon();
```

### 제네릭 타입 할당

제네릭 타입에도 똑같은 규칙을 적용해도 될까요? 가능합니다.
`Cat` `List`는 `Animal` `List`의 하위 타입이고
`MaineCoon` `List`의 상위 타입인 동물 리스트 계층을 살펴봅시다:

<img src="/assets/img/language/type-hierarchy-generics.png" alt="List<Animal> -> List<Cat> -> List<MaineCoon>">

`List<MainCoon>`은 `List<Cat>`의 하위 타입이므로
다음 예제에서, `MaineCoon` 리스트를 `myCats`에 할당이 가능합니다:

{:.passes-sa}
<?code-excerpt "lib/strong_analysis.dart (generic-type-assignment-MaineCoon)" replace="/<MaineCoon/<[!MaineCoon!]/g"?>
```dart
List<[!MaineCoon!]> myMaineCoons = ...
List<Cat> myCats = myMaineCoons;
```

방향을 반대로 해서 `Animal` 리스트를 `List<Cat>`에 할당하는 것이 가능할까요?

{:.fails-sa}
<?code-excerpt "lib/strong_analysis.dart (generic-type-assignment-Animal)" replace="/<Animal/<[!Animal!]/g"?>
```dart
List<[!Animal!]> myAnimals = ...
List<Cat> myCats = myAnimals;
```

이 할당은 `Animal` 같은 non-`dynamic` 타입에서 허용되지 않는
암시적 다운캐스트를 생성하기 때문에 정적 분석을 통과하지 못합니다.

이 코드가 정적 분석을 통과하려면 명시적 변환을 사용해야 합니다.

<?code-excerpt "lib/strong_analysis.dart (generic-type-assignment-implied-cast)" replace="/as.*(?=;)/[!$&!]/g"?>
```dart
List<Animal> myAnimals = ...
List<Cat> myCats = myAnimals [!as List<Cat>!];
```

그러나 변환된 리스트의 실제 타입 (여기서는 `myAnimals`)에 따라 명시적 변환이
런타임에 실패할 수 있습니다.

### 메서드

메서드를 재정의할 때, 생산자와 소비자 규칙은 여전히 적용이 가능합니다.
예제:

<img src="/assets/img/language/consumer-producer-methods.png" alt="Animal class showing the chase method as the consumer and the parent getter as the producer">

`chase(Animal)` 메소드 같은 소비자의 경우 매개변수 타입을 상위 타입으로 대체가 가능합니다.
`parent` getter 메서드 같은 생산자의 경우 반환 값의 타입을 하위 타입으로 대체가 가능합니다.

더 많은 정보를 원한다면,
[메서드를 재정의할 때 타입이 안전한 반환 값을 사용하세요](#메서드를-재정의할-때-타입이-안전한-반환-값을-사용하세요)
와 [메서드를 재정의할 때 타입이 안전한 매개변수를 사용하세요](#메서드를-재정의할-때-타입이-안전한-매개변수를-사용하세요)를 참고하세요.


## 다른 리소스

다음은 Dart의 안전성에 대한 추가적인 리소스 입니다:

* [자주 발생하는 타입 문제 해결](/guides/language/sound-problems) -
  안전한 Dart 코드를 작성할 때 만나게 되는 에러와 해결하는 방법.
* [타입 프로모션 실패 해결](/tools/non-promotion-reasons) - 
  타입 프로모션 에러를 해결하는 방법을 배웁니다.
* [견고한 null 안전성](/null-safety) - 
  안전한 null safety 코드 작성법을 배웁니다.
* [정적 분석 사용자화][analysis] -
  분석 옵션 파일을 사용하여 분석기와 린터를 셋업하고 사용자화하는 법을 다룹니다.


[analysis]: /tools/analysis
[language version]: /guides/language/evolution#language-versioning
[null safety]: /null-safety
