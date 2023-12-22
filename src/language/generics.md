---
title: 제네릭
description: Dart의 제네릭에 대해 학습합니다.
revpage:
  url: /language/collections
  title: 컬렉션
nextpage:
  url: /language/typedefs
  title: Typedefs
---

<?code-excerpt replace="/ *\/\/\s+ignore_for_file:[^\n]+\n//g; /(^|\n) *\/\/\s+ignore:[^\n]+\n/$1/g; /(\n[^\n]+) *\/\/\s+ignore:[^\n]+\n/$1\n/g; / *\/\/\s+ignore:[^\n]+//g; /([A-Z]\w*)\d\b/$1/g"?>

기본 배열 타입의 API 문서를 보면,
[`List`][] 타입이 `List<E>`로 표기되어 있는 걸 볼 수 있습니다.
\<...\> 표기는 List를 형식 타입 매개변수를 가지는 제네릭 (또는 *매개변수화된*) 타입으로 지정합니다.
[관례상][By convention], 대부분의 타입 변수는 E, T, S, K, V 같은 single-letter 이름을 가집니다.

## 제네릭을 왜 사용할까?

보통 타입 세이프티 때문에 제네릭을 사용하지만, 사실 더 많은 기능을 수행합니다:

* 제네릭 타입을 적절하게 명시한 코드는 더 잘 작성된 코드 입니다.
* 코드 중복를 줄이기 위해 제네릭을 사용 할 수 있습니다.

리스트가 문자열 값만 가지게 하고 싶다면, `List<String>`로 리스트를 선언하면 됩니다.
그렇게 함으로써, 동료와 개발 툴이 문자열 이외의 값은 리스트에 추가될 수 없음을 바로 알 수 있습니다:

{:.fails-sa}
```dart
var names = <String>[];
names.addAll(['Seth', 'Kathy', 'Lars']);
names.add(42); // Error
```

제네릭을 사용하는 또 다른 이유는 코드 중복을 줄이기 위함입니다.
제네릭은 정적인 분석의 이점을 챙기면서, 많은 타입들이 단일 인터페이스와 구현을 공유할 수 있게 합니다.
예를 들어, 객체를 캐싱하는 인터페이스를 생성한다고 해봅시다:

<?code-excerpt "misc/lib/language_tour/generics/cache.dart (ObjectCache)"?>
```dart
abstract class ObjectCache {
  Object getByKey(String key);
  void setByKey(String key, Object value);
}
```

해당 인터페이스의 문자열 버전이 필요하다면 다음과 같이 선언하면 됩니다:

<?code-excerpt "misc/lib/language_tour/generics/cache.dart (StringCache)"?>
```dart
abstract class StringCache {
  String getByKey(String key);
  void setByKey(String key, String value);
}
```

이후에 number 버전이 필요해 졌다면... 어떻게 하는 게 좋을까요?

제네릭 타입은 위처럼 모든 인터페이스를 생성해야하는 문제를 해결해줍니다.
타입 매개변수를 가지는 하나의 단일 인터페이스만을 구현하면 됩니다:

<?code-excerpt "misc/lib/language_tour/generics/cache.dart (Cache)"?>
```dart
abstract class Cache<T> {
  T getByKey(String key);
  void setByKey(String key, T value);
}
```

위의 코드에서, T는 대체 타입으로 개발자가 추후에 타입을 마음대로 지정할 수 있게 해주는 플레이스 홀더입니다.


## 컬렉션 리터럴 사용하기

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


## 생성자에 매개변수화된 타입 사용하기

생성자를 사용할 때 하나 혹은 다수의 타입을 특정하고 싶다면,
타입을 클래스 이름 다음의 `<...>` (angle brackets) 안에 넣으세요:

<?code-excerpt "misc/test/language_tour/generics_test.dart (constructor-1)"?>
```dart
var nameSet = Set<String>.from(names);
```

다음 예제에서는 정수 키와 View 타입의 값을 가지는 map을 생성합니다:

<?code-excerpt "misc/test/language_tour/generics_test.dart (constructor-2)"?>
```dart
var views = Map<int, View>();
```


## 제네릭 컬렉션과 제네릭 컬렉션의 타입

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


## 매개변수화된 타입 제한하기

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


## 제네릭 메서드 사용하기

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

[`List`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/List-class.html
[By convention]: /effective-dart/design#do-follow-existing-mnemonic-conventions-when-naming-type-parameters
[top-and-bottom]: /null-safety/understanding-null-safety#top-and-bottom
