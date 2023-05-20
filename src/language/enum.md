---
title: 열거 타입
description: Dart의 열거 타입에 대해 학습합니다.
short-title: Enum
---

열거 타입은 종종 _enumerations_, _enums_ 으로도 불립니다.
이 타입은 정해진 수의 상수 값을 가지는 특별한 종류의 클래스 입니다.

{{site.alert.note}}
  모든 enums은 자동적으로 [`Enum`][] 클래스를 확장합니다.
  이들은 가려져 있으며, 이는 자식 클래스가 될 수 없고 implement,
  mix 또는 명시적으로 인스턴스화할 수 없다는 것을 의미합니다.

  추상 클래스와 mixin은 명시적으로 `Enum`을 구현하거나 확장합니다.
  그러나 enum 선언에 의해 구현되거나 enum 선언에 믹스되지 않는 한,
  어떤 개체도 실제로 해당 클래스나 mixin의 타입을 구현할 수 없습니다.
{{site.alert.end}}

## 간단한 enum 선언하기

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

## 발전된 enum 사용하기

Dart는 필드, 메서드, 상수 생성자같이 수가 정해져 있는 상수 인스턴스가 있는 클래스를 선언하는 데 enum을 사용하는 것이 가능합니다.

발전된 enum을 선언하려면, [클래스][클래스]와 비슷하지만 몇 가지 다른 문법을 따라야 합니다.

* [mixins][mixins]으로 추가되는 변수들까지 모든 인스턴스 변수들은 `final`로 선언되어야 합니다.
* 모든 [제너러티브 생성자][generative constructors] 상수로 선언되어야 합니다.
* [Factory 생성자][Factory constructors]는 고정된 enum 인스턴스 중 하나만을 반환할 수 있습니다.
* [`Enum`]이 자동으로 확장되므로 다른 클래스들은 [`Enum`]으로 확장될 수 없습니다.
* `index`, `hashCode`, 항등 연산자 `==`는 재정의할 수 없습니다.
* `value`로 명명된 멤버는 enum에 선언될 수 없습니다. 만약 enum에 선언한다면, 자동으로 생성된 정적 `value` getter와 충돌합니다.
* Enum의 모든 인스턴스들은 선언의 처음 부분에 선언되어야 하고 반드시 한 개 이상의 인스턴스가 선언되어야 합니다.

발전된 enum이 가지고 있는 인스턴스 메서드는 `this`를 사용하여
현재 enum 값을 참조할 수 있습니다.

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

  bool get isTwoWheeled => this == Vehicle.bicycle;

  @override
  int compareTo(Vehicle other) => carbonFootprint - other.carbonFootprint;
}
```

{{site.alert.version-note}}
  발전된 enum은 최소 2.17의 [language version][]을 요구합니다.
{{site.alert.end}}

## enum 사용하기

[정적 변수][static variable]에 접근하는 것 처럼 열거 값에 접근하면 됩니다:

<?code-excerpt "misc/lib/language_tour/classes/enum.dart (access)"?>
```dart
final favoriteColor = Color.blue;
if (favoriteColor == Color.blue) {
  print('Your favorite color is blue!');
}
```

Enum의 각 값들은 `index` getter 메서드가 있습니다.
이 메서드는 0을 기준으로 인덱스된 위치 값을 반환합니다.
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

[switch 구문][switch statements]에 enum을 사용해도 됩니다.
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

You can access a member of an enum value
like you would on a normal object:

<?code-excerpt "misc/lib/language_tour/classes/enum.dart (method-call)"?>
```dart
print(Vehicle.car.carbonFootprint);
```

[`Enum`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Enum-class.html
[trailing commas]: /language/collections#lists
[classes]: /language/classes
[mixins]: /language/mixins
[generative constructors]: /language/constructors#상수-생성자
[Factory constructors]: /language/constructors#factory-생성자
[language version]: /guides/language/evolution#language-versioning
[static variable]: /language/classes#클래스-변수와-메서드
[switch statements]: /language/branches#switch
