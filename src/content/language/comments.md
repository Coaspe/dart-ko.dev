---
title: 주석
description: Dart의 주석 타입
prevpage:
  url: /language/operators
  title: 연산자
nextpage:
  url: /language/metadata
  title: 메타데이터
---

Dart는 싱글 라인, 멀티 라인, 문서화 주석을 지원합니다.


## 싱글 라인 주석

싱글 라인 주석은 `//`로 시작합니다. `//`와 해당 라인의 끝까지 Dart의 컴파일러가 무시합니다.

<?code-excerpt "misc/lib/language_tour/comments.dart (single-line-comments)"?>
```dart
void main() {
  // TODO: refactor into an AbstractLlamaGreetingFactory?
  print('Welcome to my Llama farm!');
}
```

## 멀티 라인 주석

멀티 라인 주석은 `/*`로 시작해서 `*/`로 끝납니다. 주석이 문서화 주석이 아니라면,
`/*`와 `*/` 사이에 있는 것들은 Dart 컴파일러가 무시합니다. 멀티 라인 주석은
중첩이 가능합니다.

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

## 문서화 주석

문서화 문서 주석은 `///` 또는 `/**`로 시작하는 멀티 또는 싱글 라인 주석입니다. 연이은 라인에 `///`
를 사용하는 것은 멀티 라인 문서 주석과 같은 효과를 발휘합니다.

문서화 주석 안에 괄호로 감싸진 텍스트를 제외한 것은 모두 애널라이저가 무시합니다.
괄호를 사용하여 클래스, 메서드, 필드, 최상위 변수, 함수, 매개변수를 참조할 수 있습니다.
괄호 안에 있는 이름은 문서화된 프로그램 요소의 렉시컬 스코프 안에서 해석됩니다.

다음은 클래스와 인자들에 대한 참조를 가지는 문서 주석에 대한 예제입니다:

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

위 클래스 안에 생성된 문서에서 `[feed]`는 `feed` 메서드의 링크가 되고,
`[Food]`는 `Food` 클래스의 링크가 됩니다.

Dart 코드를 파싱하고 HTML 문서를 생성하고 싶다면,
Dart의 문서 생성 툴인 [`dart doc`](/tools/dart-doc)를 사용하세요.
생성된 문서의 예를 보고 싶다면,
[Dart API 문서화]({{site.dart-api}}/{{site.sdkInfo.channel}}) 를
참고하세요. 주석을 어떻게 달아야하는지 조언을 얻고 싶다면,
[효과적인 Dart: 문서화](/effective-dart/documentation)을
참고하세요.
