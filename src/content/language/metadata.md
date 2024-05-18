---
title: 메타데이터
description: Dart의 메타데이터와 어노테이션에 대해 학습합니다.
toc: false
prevpage:
  url: /language/comments
  title: 주석
nextpage:
  url: /language/libraries
  title: 라이브러리
---


코드에 추가적인 정보를 더하고 싶다면 메타데이터를 사용하세요.
메타데이터 표기는 `@` 문자로 시작해서 `deprecated` 같은 컴파일 타임 상수
에 대한 참조 또는 상수 생성자에 대한 호출로 이어집니다.

모든 Dart 코드에서 다음 4개의 어노테이션이 사용 가능합니다:
[`@Deprecated`][], [`@deprecated`][], [`@override`][], 그리고 [`@pragma`][]. 
`@override`를 사용하는 예제는
[클래스 확장하기][Extending a class]를 참고하세요.
다음은 `@Deprecated` 표기를 사용하는 예제입니다:

<?code-excerpt "misc/lib/language_tour/metadata/television.dart (deprecated)" replace="/@Deprecated.*/[!$&!]/g"?>
```dart
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
```

메시지를 명시하고 싶지 않다면 `@deprecated`를 사용하세요.
그러나, 항상 `@Deprecated`를 사용하여
메시지를 명시하는 것을 [추천][dep-lint]합니다.

개발자가 메타데이터 어노테이션을 정의할 수도 있습니다.
다음은 두개의 인자를 받는 `@Todo` 어노테이션을 정의하는 예제입니다:

<?code-excerpt "misc/lib/language_tour/metadata/todo.dart"?>
```dart
class Todo {
  final String who;
  final String what;

  const Todo(this.who, this.what);
}
```

다음은 `@Todo` 어노테이션을 사용하는 예제입니다:

<?code-excerpt "misc/lib/language_tour/metadata/misc.dart (usage)"?>
```dart
@Todo('Dash', 'Implement this function')
void doSomething() {
  print('Do something');
}
```

메타데이터는 라이브러리, 클래스, typedef, 타입 매개변수,
생성자, factory, 함수, 필드, 매개변수, 변수 선언 뒤에 나올 수 있고
import나 export 명령어 뒤에도 나올 수 있습니다.

[`@Deprecated`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Deprecated-class.html
[`@deprecated`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/deprecated-constant.html
[`@override`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/override-constant.html
[`@pragma`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/pragma-class.html
[dep-lint]: /tools/linter-rules/provide_deprecation_message
[Extending a class]: /language/extend
