---
title: 패키지 생성
description: Dart 라이브러리 패키지 생성 방법을 학습합니다.
---

Dart 생태계는 라이브러리나 툴 같은 소프트웨어를
공유하기 위해 [패키지](/guides/packages)를 사용합니다.
이 페이지는 표준 [패키지](/tools/pub/glossary#package)의 생성 방법을 알려줍니다.


## 새로운 패키지 생성

생성할 패키지의 초기 디렉토리와 구조를 만들고 싶다면,
[`dart create`](/tools/dart-create) 커맨드와 `package` 템플릿을 사용하세요:

```console
$ dart create -t package <PACKAGE_NAME>
```

{% comment %}
TODO: Add coverage of packages that contain tools.
{% endcomment %}

## 라이브러리 패키지를 구성하는 것

다음은 다이어그램은 간단한 라이브러리 패키지의 레이아웃을 나타냅니다:

<img 
  src="/assets/img/libraries/simple-lib2.png" 
  alt="root directory contains pubspec.yaml and lib/file.dart">

라이브러리를 위한 최소한의 요구사항은 다음과 같습니다:

pubspec 파일
: 라이브러리의 `pubspec.yaml` 파일은 [어플리케이션 패키지][application package]의 `pubspec.yaml`과 같습니다.
  `pubspec.yaml` 파일에는 이 패키지가 라이브러리라고 명시되어 있지 않습니다.

lib 디렉토리
: 예상대로 라이브러리의 코드는 _lib_ 디렉토리 아래에 있으며 다른 패키지에 대해서는 공개되어 있습니다.
  필요에 따라 lib에서 파일 계층을 임의로 만들 수 있습니다.
  관례에 따라 구현 코드는 _lib/src_ 아래에 위치합니다.
  _lib/src_ 에 잇는 코드들은 private로 간주됩니다.
  다른 패키지는 `src/...` 디렉토리에서 코드를 가져올 필요가 없습니다.
  lib/src 디렉토리의 API 공개는 lib/scr 디렉토리의 파일을 lib 디렉토리의 파일로
  내보냄으로써 달성됩니다.

[application package]: /tools/pub/glossary#application-package

## 라이브러리 패키지 구성

_미니 라이브러리_ 라고 하는 작고 독립된 라이브러리를 만들 때는 라이브러리 패키지의
유지 관리, 확장 및 테스트가 매우 쉽습니다.
대부분의 경우, 각 클래스가 밀접하게 결합된 상황이 아닌 한 자신을
미니 라이브러리로 간주해야 합니다.

:::note
  파일의 앞 부분에 `part` 명령을 사용하여 하나의 라이브러리를 여러 개의 Dart
  파일로 분할할 수 있습니다. `part` 명령을 사용하지 않고 미니 라이브러리를
  생성하는 것을 추천합니다.
:::

모든 공개 API를 내보내는 lib/_&lt;package-name&gt;_.dart의
"main" 라이브러리 파일을 lib 디렉토리에 직접 만드세요.
이를 통해 사용자는 하나의 파일을 가져오면 라이브러리의 모든
기능을 사용할 수 있습니다.

lib 디렉토리는 src 라이브러리 외에도 다른 중요한 것들을 가지고 있습니다.
예를 들어, 메인 라이브러리가 크로스 플랫폼이지만, 생성된 독립 라이브러리는
`dart:io` 또는 `dart:html`에 의존할 수도 있습니다.
몇몇 패키지들은 프리픽스를 사용하여 가져와야하는 독립된 라이브러리와 그렇지 않은
메인 라이브러리를 가집니다.

다음으로 shelf 라는 실제 라이브러리 패키지의 구조를 살펴봅시다.
[shelf]({{site.repo.dart.org}}/shelf)
패키지는 Dart를 사용하여 웹 서버를 쉽게 만들 수 있는 방법을 제공하고,
Dart 라이브러리 패키지에서 자주 사용되는 구조를 가지고 있습니다:

<img 
  src="/assets/img/libraries/shelf.png"
  alt="shelf root directory contains example, lib, test, and tool subdirectories">

lib 바로 아래에 메인 라이브러리 파일인 `shelf.dart`는
`lib/src`의 여러 API 파일들을 내보냅니다.
과도하게 API를 내보내지 않고 개발자에게 공개 API의 개요를 제공하기 위해
`shelf.dart`는 `show`를 사용하여 내보낼 코드를 지정합니다.

```dart title="lib/shelf.dart"
export 'src/cascade.dart' show Cascade;
export 'src/handler.dart' show Handler;
export 'src/hijack_exception.dart' show HijackException;
export 'src/middleware.dart' show Middleware, createMiddleware;
export 'src/middleware/add_chunked_encoding.dart' show addChunkedEncoding;
export 'src/middleware/logger.dart' show logRequests;
export 'src/middleware_extensions.dart' show MiddlewareExtensions;
export 'src/pipeline.dart' show Pipeline;
export 'src/request.dart' show Request;
export 'src/response.dart' show Response;
export 'src/server.dart' show Server;
export 'src/server_handler.dart' show ServerHandler;
```

shelf 패키지는 shelf_io라는 미니 라이브러리를 포함하고 있습니다.
이 어댑터는 `dart:io`의 HttpRequest 객체를 처리합니다.

:::tip
[`webdev serve`][]를 사용하여 개발할 때 최고의 성능을 위해
[구현 파일](/tools/pub/package-layout#implementation-files)을
`/lib` 대신 `/lib/src` 아래에 위치시키세요.
또한, <code>package:<em>package_name</em>/src/...</code>를 사용하여 파일을 가져오지 마세요.
:::

[`webdev serve`]: /tools/webdev#serve

## 라이브러리 파일 가져오기

다른 패키지에서 라이브러리 파일을 임포트할 때,
`package:` 명령어로 파일의 URI를 지정하세요.

```dart
import 'package:utilities/utilities.dart';
```

작성하고 있는 패키지의 라이브러리 파일을 가져오고 싶다면
두 파일 모두 lib 디렉토리에 있거나 lib 디렉토리 밖에 있을 때,
상대 경로로 라이브러리 파일을 가져올 수 있습니다.
Import되는 파일이 lib에 있고 import하는 파일이 밖에 있다면, `package:`를 사용하여 가져오세요.

다음 그림은 web과 lib에서 `lib/foo/a.dart`를 가져오는 방법을 보여줍니다.

<img 
  src="/assets/img/libraries/import-lib-rules.png"
  alt="lib/bar/b.dart uses a relative import; web/main.dart uses a package import">


## 라이브러리 파일을 조건부로 가져오고 내보내기

라이브러리가 멀티 플랫폼을 지원한다면,
라이브러리 파일을 조건부로 가져오고 내보내는 것이 필요합니다.
웹과 네이티브 플랫폼을 모두 지원하는 라이브러리가 흔한 예시입니다.

조건부로 내보내고 가져오고 싶다면,
`dart:*` 라이브러리가 존재하는지 확인해야합니다.
다음은 `dart:io`과 `dart:js_interop`의 존재 여부를 확인하는
조건부 내보내기의 예제입니다:

<?code-excerpt "create_libraries/lib/hw_mp.dart (export)"?>
```dart title="lib/hw_mp.dart"
export 'src/hw_none.dart' // Stub implementation
    if (dart.library.io) 'src/hw_io.dart' // dart:io implementation
    if (dart.library.js_interop) 'src/hw_web.dart'; // package:web implementation
```

코드 설명은 다음과 같습니다:

* 커맨드 라인 앱 처럼 `dart:io`를 사용하는 앱은
  `src/hw_io.dart`를 내보냅니다.
* `dart:js_interop`를 사용하는 웹앱은
  `src/hw_web.dart`를 내보냅니다.
* 모두 해당하지 않는다면, `src/hw_none.dart`를 내보냅니다.

파일을 조건부로 가져오고 싶다면, 위의 코드에서 `export`를 `import`로 교체하세요.

:::note
  조건부 가져오기 또는 내보내기는 실제 가져오기 또는 사용 여부에 관계없이
  라이브러리가 현재 플랫폼에서 _사용 가능한지_ 여부만 확인합니다.
:::

조건부로 내보낸 모든 라이브러리는 동일한 API를 구현해야 합니다.
예를 들어, 다음 `dart:io` 구현을 살펴봅시다:

<?code-excerpt "create_libraries/lib/src/hw_io.dart"?>
```dart title="lib/src/hw_io.dart"
import 'dart:io';

void alarm([String? text]) {
  stderr.writeln(text ?? message);
}

String get message => 'Hello World from the VM!';
```

다음은 `UnsupportedError`를 발생시키는 스텁을 사용하는 기본 구현입니다:

<?code-excerpt "create_libraries/lib/src/hw_none.dart"?>
```dart title="lib/src/hw_none.dart"
void alarm([String? text]) => throw UnsupportedError('hw_none alarm');

String get message => throw UnsupportedError('hw_none message');
```

모든 플랫폼에서
조건부 내보내기 코드를 가지고 있는 라이브러리를 가져오는 것이 가능합니다:

<?code-excerpt "create_libraries/example/hw_example.dart" replace="/create_libraries/hw_mp/g"?>
```dart
import 'package:hw_mp/hw_mp.dart';

void main() {
  print(message);
}
```

## Providing additional files

A well-designed package is easy to test.
We recommend that you write tests using the
[test]({{site.repo.dart.org}}/test) package,
placing the test code in the `test` directory at the
top of the package.

If you create any command-line tools intended for public consumption,
place those in the `bin` directory, which is public.
Enable running a tool from the command line, using
[`dart pub global activate`](/tools/pub/cmd/pub-global#activating-a-package).
Listing the tool in the
[`executables` section](/tools/pub/pubspec#executables)
of the pubspec allows a user to run it directly without calling
[`dart pub global run`](/tools/pub/cmd/pub-global#running-a-script-using-dart-pub-global-run).

It's helpful if you include an example of how to use your library.
This goes into the `example` directory at the top of the package.

Any tools or executables that you create during development that aren't for
public use go into the `tool` directory.

Other files that are required if you publish your library to the
[pub.dev]({{site.pub}}) site, such as `README.md` and `CHANGELOG.md`, are
described in [Publishing a package](/tools/pub/publishing).
For more information on how to organize a package directory,
see the [pub package layout conventions](/tools/pub/package-layout).

## Documenting a library

You can generate API docs for your library using
the [`dart doc`][] tool.
`dart doc` parses the source looking for
[documentation comments](/effective-dart/documentation#doc-comments),
which use the `///` syntax:

```dart
/// The event handler responsible for updating the badge in the UI.
void updateBadge() {
  ...
}
```

For an example of generated docs, see the
[shelf documentation.]({{site.pub-api}}/shelf/latest)

To include any *library-level* documentation in the generated docs,
add a `library` directive and attach the comment directly above it.
For the how-and-why of documenting libraries, see
[Effective Dart: Documentation](/effective-dart/documentation#consider-writing-a-library-level-doc-comment).


## Distributing an open source library {:#distributing-a-library}

If your library is open source,
we recommend sharing it on the [pub.dev site.]({{site.pub}})
To publish or update the library,
use [pub publish](/tools/pub/cmd/pub-lish),
which uploads your package and creates or updates its page.
For example, see the page for the [shelf package.]({{site.pub-pkg}}/shelf)
See [Publishing a package](/tools/pub/publishing)
for details on how to prepare your package for publishing.

The pub.dev site not only hosts your package,
but also generates and hosts your package's API reference docs.
A link to the latest generated docs is in the package's **About** box;
for example, see the shelf package's
[API docs.]({{site.pub-api}}/shelf)
Links to previous versions' docs are in the
**Versions** tab of the package's page.

To ensure that your package's API docs look good on the pub.dev site,
follow these steps:

* Before publishing your package, run the [`dart doc`][] tool
  to make sure that your docs generate successfully and look as expected.
* After publishing your package, check the **Versions** tab
  to make sure that the docs generated successfully.
* If the docs didn't generate at all,
  click **failed** in the **Versions** tab to see the `dart doc` output.

## Resources

Use the following resources to learn more about packages:

* [Libraries and imports](/language/libraries) covers
  using library files.
* The [package](/guides/packages) documentation is useful, particularly the
  [package layout conventions](/tools/pub/package-layout).
* [What not to commit](private-files)
  covers what should not be checked into a source code repository.
* The newer packages under the
  [dart-lang]({{site.repo.dart.org}}) organization tend
  to show best practices. Consider studying these examples:
  [dart_style,]({{site.repo.dart.org}}/dart_style)
  [path,]({{site.repo.dart.org}}/path)
  [shelf,]({{site.repo.dart.org}}/shelf)
  [source_gen,]({{site.repo.dart.org}}/source_gen) and
  [test.]({{site.repo.dart.org}}/test)

[`dart doc`]: /tools/dart-doc
