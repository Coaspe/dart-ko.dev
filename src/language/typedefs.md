---
title: Typedefs
description: Dart의 타입 앨리어스에 대해 학습합니다.
toc: false
prevpage:
  url: /language/generics
  title: Generics
nextpage:
  url: /language/type-system
  title: Type system
---

<?code-excerpt replace="/ *\/\/\s+ignore_for_file:[^\n]+\n//g; /(^|\n) *\/\/\s+ignore:[^\n]+\n/$1/g; /(\n[^\n]+) *\/\/\s+ignore:[^\n]+\n/$1\n/g; / *\/\/\s+ignore:[^\n]+//g; /([A-Z]\w*)\d\b/$1/g"?>

`typedef` 키워드로 선언되기 때문에 _typedef_ 로도 불리우는
타입 앨리어스는 타입을 참조하는 간편한 수단입니다.
다음은 `IntList`라는 타입 앨리어스를 선언하고 사용하는 예제입니다:

<?code-excerpt "misc/lib/language_tour/typedefs/misc.dart (int-list)"?>
```dart
typedef IntList = List<int>;
IntList il = [1, 2, 3];
```

타입 앨리어스는 타입 매개변수를 가집니다:

<?code-excerpt "misc/lib/language_tour/typedefs/misc.dart (list-mapper)"?>
```dart
typedef ListMapper<X> = Map<X, List<X>>;
Map<String, List<String>> m1 = {}; // 타입 선언이 장황합니다.
ListMapper<String> m2 = {}; // 위와 같지만 더 깔끔하고 짧습니다.
```

{{site.alert.version-note}}
  2.13 이전 버전에는 typedefs를 함수 타입에만 사용이 가능했습니다.
  새로운 typedefs를 사용하고 싶다면 최소 2.13.의 [language version][]이 필요합니다.
{{site.alert.end}}

대부분의 상황에서 함수에는 typedefs 대신
[인라인 함수 타입][inline function types]의 사용을 추천합니다.
하지만, 함수의 typedefs는 여전히 유용합니다:

<?code-excerpt "misc/lib/language_tour/typedefs/misc.dart (compare)"?>
```dart
typedef Compare<T> = int Function(T a, T b);

int sort(int a, int b) => a - b;

void main() {
  assert(sort is Compare<int>); // True!
}
```

[language version]: /guides/language/evolution#language-versioning
[inline function types]: /effective-dart/design#prefer-inline-function-types-over-typedefs
