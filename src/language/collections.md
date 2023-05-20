---
title: 컬렉션
description: Dart의 다양한 컬렉션에 대한 요약.
---

Dart has built-in support for list, set, and map [collections][].
To learn more about configuring the types collections contain,
check out [Generics][].

## Lists

아마 모든 프로그래밍 언어에서 가장 흔한 컬렉션은 *배열*이나 정렬된 객체의 그룹일 겁니다.
Dart에서 배열은 [`List`][] 객체로 존재하며, 보통 *list*라고 부릅니다.

Dart list literals are denoted by
a comma separated list of expressions or values,
enclosed in square brackets (`[]`).
Here's a simple Dart list:

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (list-literal)"?>
```dart
var list = [1, 2, 3];
```

{{site.alert.note}}
  Dart는 위의 `list`를 `List<int>` 타입으로 추정합니다. `list`에 정수가 아닌 값을
  추가하고 싶다면, 애널라이저 또는 런타임이 에러를 발생시킵니다. 더 자세히 알고 싶다면,
  [타입 추론][type inference]
{{site.alert.end}}

<a name="trailing-comma"></a>
Dart 컬렉션 리터럴의 마지막 아이템 뒤에 쉼표를 추가할 수 있습니다.
_trailing comma_ 는 컬렉션에 영향을 미치진 않지만,
복사-붙여넣기 에러를 예방합니다.

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

Lists에 대한 더 자세한 정보를 원한다면,
[라이브러리 투어](/guides/libraries/library-tour#lists)의
lists 섹션을 살펴보세요.

## Sets

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
  [타입 추론](/guides/language/type-system#타입-추론)을
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

`add()` 또는 `addAll()` 메서드를 사용해 존재하는 set에 요소를 추가하세요:

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (set-add-items)"?>
```dart
var elements = <String>{};
elements.add('fluorine');
elements.addAll(halogens);
```

Set에 있는 요소들의 수를 알고 싶다면, `.length`를 사용하세요:

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

Sets에 대한 더 자세한 정보를 원한다면
[라이브러리 투어](/guides/libraries/library-tour#sets)
의 Sets 섹션을 참고하세요.

## Maps

일반적으로, map은 키와 값으로 구성된 객체입니다.
키와 값 모두 어떤 타입의 객체든 할당이 가능합니다.
각 *키*들은 유일하지만 *값*은 중복될 수 있습니다.
Dart는 map 리터럴과 [`Map`][] 타입으로 map을 지원합니다.

다음은 map 리터럴을 사용하여 Dart의 map을 생성하는 코드입니다:

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (map-literal)"?>
```dart
var gifts = {
  // 키:    값
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
  [타입 추론][type inference]을 참고하세요.
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
  더 자세한 사항은 [생성자 사용하기][Using constructors]를 참고하세요.
{{site.alert.end}}

서브스크립트 할당 연산자 (`[]=`)를 사용하여
기존의 map에 키-값 쌍을 추가하세요:

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (map-add-item)"?>
```dart
var gifts = {'first': 'partridge'};
gifts['fourth'] = 'calling birds'; // 키-값 쌍 추가
```

서브스크립트 연산자 (`[]`)를 사용하여 map에서 원하는 값에 접근하세요:

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (map-retrieve-item)"?>
```dart
var gifts = {'first': 'partridge'};
assert(gifts['first'] == 'partridge');
```

Map에 존재하지 않는 키로 접근하면, `null`을 반환합니다:

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (map-missing-key)"?>
```dart
var gifts = {'first': 'partridge'};
assert(gifts['fifth'] == null);
```

Map에 있는 키-값 쌍의 개수를 알고 싶다면, `.length`을 사용하세요:

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

Maps에 대한 더 자세한 정보를 원한다면,
[라이브러리 투어](/guides/libraries/library-tour#maps)의
Maps 섹션을 참고하세요.

## 연산자

### 전개 연산자

Dart는 컬렉션에 여러 값들을 간편하게 삽입해주는
**전개 연산자** (`...`)와 **null-aware 전개 연산자** (`...?`)
를 지원합니다.

예를 들어, 한 리스트의 모든 요소를 다른 리스트에 삽입할 때
전개 연산자 (`...`)를 사용할 수 있습니다:

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (list-spread)"?>
```dart
var list = [1, 2, 3];
var list2 = [0, ...list];
assert(list2.length == 4);
```

전개 연산자의 오른편 표현식의 값이 null 일 수도 있다면,
null-aware 전개 연산자 (`...?`)를 사용하여 예외를 피할 수 있습니다:


<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (list-null-spread)"?>
```dart
var list2 = [0, ...?list];
assert(list2.length == 1);
```

전개 연산에 대한 더 많은 정보와 예제를 원한다면,
[전개 연산자 프로포절][spread proposal]을 참고하세요.

<a id="collection-operators"></a>
### 제어-흐름 연산자

Dart는 조건 (`if`)과 반복 (`for`)을 사용하여
컬렉션을 빌드할 수 있는 **컬렉션 if** 와 **컬렉션 for**
을 제공합니다.

다음은 **컬렉션 if**를 사용하여 3개 또는 4개의 요소가 있는 리스트를 생성한는 예제입니다:

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (list-if)"?>
```dart
var nav = ['Home', 'Furniture', 'Plants', if (promoActive) 'Outlet'];
```

다음은 **컬렉션 for**을 사용하여 list 항목을
다른 목록에 추가하기 전에 해당 항목을 수정하는 예제입니다:

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (list-for)"?>
```dart
var listOfInts = [1, 2, 3];
var listOfStrings = ['#0', for (var i in listOfInts) '#$i'];
assert(listOfStrings[1] == '#1');
```

컬렉션 `if` 와 `for`에 대한 더 자세한 정보와 예제를 원한다면,
[제어 흐름 컬렉션 프로포절][collections proposal]을 참고하세요.

[collections]: /guides/libraries/library-tour#collections
[type inference]: /language/type-system#type-inference
[`List`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/List-class.html
[`Map`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Map-class.html
[Using constructors]: /language/classes#생성자-사용하기
[collections proposal]: https://github.com/dart-lang/language/blob/master/accepted/2.3/control-flow-collections/feature-specification.md
[spread proposal]: https://github.com/dart-lang/language/blob/master/accepted/2.3/spread-collections/feature-specification.md
[generics]: /language/generics
[`Set`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Set-class.html
