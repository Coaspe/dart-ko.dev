---
title: 라이브러리 & 임포트
description: 라이브러리를 구현하고 임포팅하는 방법에 대해 학습합니다.
prevpage:
  url: /language/metadata
  title: 메타데이터
nextpage:
  url: /language/keywords
  title: 키워드
---

`import`와 `library` 명령어는 코드를 모듈화하고 공유하는 것을 도와줍니다.
라이브러리는 API를 제공할 뿐만 아니라, 관리(privacy)의 단위가 됩니다:
언더스코어(`_`)로 시작하는 식별자들은 오직 그 라이브러리 안에서만 보입니다.
[`library`](#library-directive) 명령어를 사용하지 않았다고 해도,
*모든 Dart 앱은 [라이브러리][library]*입니다.

라이브러리들은 [packages](/guides/packages)를 사용해 분산 될 수 있습니다.

{{site.alert.info}}
  Dart가 `public` 이나 `private` 같은 접근 수식 키워드를 사용하지 않고,
  언더스코어를 사용하는지가 궁금하다면,
  [SDK issue 33383](https://github.com/dart-lang/sdk/issues/33383)
  를 참고하세요.
{{site.alert.end}}

[library]: /tools/pub/glossary#library

## 라이브러리 사용하기

어떤 라이브러리의 네임스페이스가 다른 라이브러리의 스코프에서 사용되는 방법을
지정하고 싶다면 `import`를 사용하세요. 

예를 들어, Dart 웹앱은 보통
[dart:html][] 라이브러리를 사용합니다. 다음 예제 처럼 말이죠:

<?code-excerpt "misc/test/language_tour/browser_test.dart (dart-html-import)"?>
```dart
import 'dart:html';
```

`import`가 필요한 인자는 라이브러리를 특정 지을 수 있는 URI뿐입니다.
내장 라이브러리들은 `dart:`라는 특별한 스킴(scheme)을 따릅니다.
이외의 라이브러리를 사용하고 싶다면, 파일 시스템 경로나 `package:`를 사용하면 됩니다.
`package:` 스킴은 pub 같은 패키지 매니저가 제공하는 라이브러리를 특정 지을 때 사용합니다.

<?code-excerpt "misc/test/language_tour/browser_test.dart (package-import)"?>
```dart
import 'package:test/test.dart';
```

{{site.alert.note}}
  *URI*는 stands for uniform resource identifier의 준말입니다.
  *URLs* (uniform resource locators)은 URI의 종류입니다.
{{site.alert.end}}

### 라이브러리 프리픽스 지정하기

같은 식별자를 가지는 두 개의 라이브러리를 임포트하면 충돌이 발생합니다.
그럴 때 프리픽스를 특정하면 문제가 해결됩니다.
예를 들면, 라이브러리1과 라이브러리2가 Element 클래스를 가진다고 하면, 코드는 다음과 같을 것 입니다:

<?code-excerpt "misc/lib/language_tour/libraries/import_as.dart" replace="/(lib\d)\.dart/package:$1\/$&/g"?>
```dart
import 'package:lib1/lib1.dart';
import 'package:lib2/lib2.dart' as lib2;

// lib1의 Element 사용.
Element element1 = Element();

// lib3의 Element 사용.
lib2.Element element2 = lib2.Element();
```

### 라이브러리의 일부만 가져오기

라이브러리의 일부만 필요하다면, 다음과 같이 라이브러리를 선택적으로 import 할 수 있습니다:

<?code-excerpt "misc/lib/language_tour/libraries/show_hide.dart" replace="/(lib\d)\.dart/package:$1\/$&/g"?>
```dart
// foo만 import.
import 'package:lib1/lib1.dart' show foo;

// foo를 제외하고 모두 import.
import 'package:lib2/lib2.dart' hide foo;
```

<a id="deferred-loading"></a>
#### 라이브러리 지연 로딩

_지연 로딩(Deferred loading)_ (_lazy loading_)은
웹앱이 해당 라이브러리가 필요할 때 로드하게 해줍니다.
다음은 지연 로딩을 사용해야 하는 케이스 입니다:

* 웹앱의 초기 로딩 시간을 줄이고 싶을 때
* A/B 테스팅을 진행할 때 —
  예를 들어, 대안이 되는 알고리즘들의 구현을 시험해 볼 때가 있습니다.
* 선택적인 화면과 다이얼 로그 같은 드물게 사용되는 기능을 로드 할 때.

{{site.alert.warn}}
  **오직 `dart compile js`만 지연 로딩을 지원합니다.**
  Flutter, Dart VM은 지연 로딩을 지원하지 않습니다.
  더 자세히 알고 싶다면,
  [issue #33118](https://github.com/dart-lang/sdk/issues/33118)와
  [issue #27776.](https://github.com/dart-lang/sdk/issues/27776)를
  참고하세요.
{{site.alert.end}}

라이브러리를 필요 할 때 지연 로딩하고 싶다면, `deferred as`를 사용해 입포트 하세요.

<?code-excerpt "misc/lib/language_tour/libraries/greeter.dart (import)" replace="/hello\.dart/package:greetings\/$&/g"?>
```dart
import 'package:greetings/hello.dart' deferred as hello;
```

라이브러리를 사용해야 한다면, `loadLibrary()`를 라이브러리의 식별자에 사용해 호출하세요.

<?code-excerpt "misc/lib/language_tour/libraries/greeter.dart (loadLibrary)"?>
```dart
Future<void> greet() async {
  await hello.loadLibrary();
  hello.printGreeting();
}
```

앞선 코드에서, `await` 키워드는 라이브러리가 로드 될 때 까지 실행을 멈춥니다.
`async`와 `await`에 대해 더 자세히 알고 싶다면, [비동기 지원](#비동기-지원)을 참고하세요.

`loadLibrary()`를 한 라이브러리에 여러번 호출해도
한 번만 로드되기 때문에 에러가 발생하지 않습니다.

지연 로딩을 사용할 때 다음을 꼭 기억해두세요:

* 지연된 라이브러리의 상수는 import하는 파일에서 상수가 아닙니다.
  꼭 기억하세요, 이 상수는 지연된 라이브러리가 로드되기 전에는 존재하지 않는 상수 입니다.
* Import하는 파일에서 지연된 라이브러리에 타입을 사용 할 수 없습니다.
  대신, 지연된 라이브러리와 import하는 파일에서 가져온 라이브러리로 인터페이스 타입을 이동하는 것을 고려하세요.
* Dart는 암묵적으로 `loadLibrary()`를 <code>deferred as <em>namespace</em></code>
  를 사용하여 정의한 네임스페이스에 삽입합니다.
  `loadLibrary()`는 [`Future`](/guides/libraries/library-tour#future)를 반환합니다.

### `library` 명령어 {#library-directive}

라이브러리 수준의 [문서화 주석][doc comments] or [메타데이터 어노테이션][metadata annotations]를
지정하고 싶다면, 파일의 시작에 `library` 선언을 추가하세요.

<?code-excerpt "misc/lib/effective_dart/docs_good.dart (library-doc)"?>
{% prettify dart tag=pre+code %}
/// A really great test library.
@TestOn('browser')
library;
{% endprettify %}

## 라이브러리 구현하기

라이브러리 구현에 대한 자세한 방법은
다음 항목들을 포함하는
[패키지 만들기](/guides/libraries/create-packages)를 살펴보세요:

* 라이브러리 소스 코드 구성법.
* `export` 명령어 사용법.
* `part` 명령어를 사용해야할 때.
* 다수의 플랫폼을 지원하는 라이브러리를 구현 할 때
  조건적인 import와 export의 사용법.

[dart:html]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-html
[doc comments]: /effective-dart/documentation#consider-writing-a-library-level-doc-comment
[metadata annotations]: /language/metadata
