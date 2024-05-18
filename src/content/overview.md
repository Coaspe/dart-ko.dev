---
title: Dart 개요
description: Dart에 대한 짦은 소개
js: [{url: '/assets/js/inject_dartpad.js', defer: true}]
---

<img 
  style="padding: 30px; float: right; width: 300px" 
  src="/assets/img/logo_lockup_dart_horizontal.png" 
  alt="Dart product logo">

Dart는 모든 플랫폼에서 빠른 앱을 개발하기 위해 클라이언트에 최적화된 언어입니다.
Dart의 목표는 앱 프레임워크를 위한 [유연한 실행 런타임 플랫폼](#platform)을
가진 멀티 플랫폼 개발을 위한 가장 생산적인 프로그래밍 언어를 제공하는 것입니다.

일반적으로 프로그래밍 언어에는 언어 설계에서의 선택이
기능과 강점을 결정한다는 _기술적 범위 (techincal envelope)_ 가 포함되어있습니다.
Dart의 언어 설계는 클라이언트 개발에 적합하게 개발되었으며
다중 플랫폼 (웹, 모바일 및 데스크톱)에서의 개발(1초 이하의 stateful 핫리로드)과
높은 퀄리티의 프로덕션 경험을 우선시합니다.

Dart는 [Flutter]({{site.flutter}})의 기반을 형성하였습니다.
Dart는 Flutter 앱을 구동하는 언어와 런타임을 제공할 뿐만 아니라,
코드 포맷, 분석, 테스트와 같은 개발자들의 핵심 작업들을 지원합니다.


## Dart: 언어 {#language}

Dart 언어는 타입 세이프 합니다;
Dart는 변수의 값이 _항상_ 변수의 정적 타입과 매치됨을 보장하는
정적 타입 체킹을 사용합니다. 가끔, 이것은 sound typing으로도 불립니다.
비록 타입은 필수이지만, 타입 추론이 있기 때문에 타입 표기는 선택적입니다.
Dart 타이핑(typing) 시스템은 유연하여 런타임 검사와 결합된
`dynamic` 타입을 사용할 수 있으며, 이는 실험 중에 또는 동적인 코드에 유용할 수 있습니다.

Dart는 [견고한 null 안전성](/null-safety)을 지원합니다.
이는 개발자가 해당 변수의 값이 null 일 수 있음을 표시하지 않는다면,
null 값을 가지지 못하게 한다는 것을 의미합니다.
견고한 null 안전성를 사용하여 Dart는 정적 코드 분석을 통해
런타임에서 null exception으로부터 개발자들을 보호합니다.
Null-safe한 다른 언어들과 다르게,
Dart가 어떤 변수를 non-nullable로 결정할 때,
해당 변수는 _항상_ non-nullable입니다.
디버거에서 실행 코드를 검사하면,
런타임에서 non-nullability가 유지되는 것을 알 수 있습니다(그렇기 때문에 _sound_ null safety라고 부릅니다).

다음 코드 샘플은 라이브러리, async 호출, nullable 그리고 non-nullable 타입,
화살표 문법, 재네레이터, 스트림, getter와 같은 다양한 Dart 언어의 피쳐를 포함합니다.
언어에 대해 더 학습하고 싶다면,
[Dart 언어 투어](/language)를 참고하세요.

<?code-excerpt "misc/lib/overview_pi.dart"?>
```dart:run-dartpad:ga_id-overview
import 'dart:math' show Random;

void main() async {
  print('Compute π using the Monte Carlo method.');
  await for (final estimate in computePi().take(100)) {
    print('π ≅ $estimate');
  }
}

/// 점점 더 정확해지는 π 근사치의 스트림 생성.
Stream<double> computePi({int batch = 100000}) async* {
  var total = 0; // int 타입으로 추론됩니다.
  var count = 0;
  while (true) {
    final points = generateRandom().take(batch);
    final inside = points.where((p) => p.isInsideUnitCircle);

    total += batch;
    count += inside.length;
    final ratio = count / total;

    // 원의 넓이는 A = π⋅r² 이므로, π = A/r².
    // 따라서 x ∈ <0,1>, y ∈ <0,1>의 임의의 점이 주어졌을 때,
    // 단위 원 안에 있는 점들의 비율은 π / 4에 근접해야합니다.
    // 그러므로, π의 값은 다음과 같습니다:
    yield ratio * 4;
  }
}

Iterable<Point> generateRandom([int? seed]) sync* {
  final random = Random(seed);
  while (true) {
    yield Point(random.nextDouble(), random.nextDouble());
  }
}

class Point {
  final double x;
  final double y;

  const Point(this.x, this.y);

  bool get isInsideUnitCircle => x * x + y * y <= 1;
}
```

{{site.alert.info}}
  위의 에제는 임베드 [DartPad](/tools/dartpad)에서 실행됩니다.
  이 예제를
  <a href="{{site.dartpad}}/bc63d212c3252e44058ff76f34ef5730"
  target="_blank" rel="noopener">새로운 창에서 열 수도 있습니다</a>.
{{site.alert.end}}


## Dart: 라이브러리 {#libraries}

Dart는 프로그래밍 업무에 필수적인 기능을 제공하는
[풍부한 핵심 라이브러리](/libraries)를 가지고 있습니다:

* 내장 타입, 컬렉션, 그리고 Dart 프로그램을 위한 또 다른 핵심 기능
  (`dart:core`)
* 큐, 링크드 리스트, 해쉬맵, 이진 트리 같은 풍부한 컬렉션
  (`dart:collection`)
* JSON, UTF-8을 포함한 서로 다른 데이터 표현 간의 변환을 위한 인코더와 디코더
  (`dart:convert`)
* 수학과 관련된 상수, 함수, 난수 생성
  (`dart:math`)
* Support for asynchronous programming,
  with classes such as `Future` and `Stream`
  (`dart:async`)
* 고정된 크기의 데이터를 효과적으로 다루는 리스트들
  (예를 들어, unsigned 8-byte 정수)과 SIMD 숫자 타입
  (`dart:typed_data`)
* File, socket, HTTP, and other I/O support for non-web applications
  (`dart:io`)
* Foreign function interfaces for interoperability with
  other code that presents a C-style interface
  (`dart:ffi`)
* _Isolate_ 를 사용한 동시(concurrent) 프로그래밍—
  스레드와 비슷하지만 메모리를 공유하지 않고, 메시지로 통신하는 독립된 워커(worker)
  (`dart:isolate`)
* 브라우저 및 DOM(Document Object Model)과 상호작용해야 하는
  웹 기반 애플리케이션을 위한 HTML 엘리먼트와 기타 리소스
  (`dart:html`)

핵심 라이브러리를 이외에도 많은 API들이 패키지 셋을 통해 제공됩니다.
Dart 팀은 다음과 같은 유용한 보조 패키지를 만들었습니다:

* [characters]({{site.pub-pkg}}/characters)
* [intl]({{site.pub-pkg}}/intl) 
* [http]({{site.pub-pkg}}/http)
* [crypto]({{site.pub-pkg}}/crypto)
* [markdown]({{site.pub-pkg}}/markdown)

게다가, 서드 파티 퍼블리셔와 광범위한 커뮤니티가 수천 개의 패키지와 함께
다음과 같은 기능을 제공합니다:

* [XML]({{site.pub-pkg}}/xml) 
* [Windows integration]({{site.pub-pkg}}/win32)
* [SQLite]({{site.pub-pkg}}/sqflite_common)
* [compression]({{site.pub-pkg}}/archive)

To see a series of working examples featuring the Dart core libraries,
read the [core library documentation](/libraries).
To find additional APIs, check out the
[commonly used packages page](/resources/useful-packages).


## Dart: 플랫폼 {#platform}

Dart의 컴파일러 기술은 코드를 여러 가지 방법으로 실행할 수 있게 해줍니다:

* **네이티브 플랫폼**: 모바일과 데스크탑 디바이스를 겨냥한 앱들을 위해,
  Dart는 머신 코드를 생성할 때 JIT(just-in-time) 컴파일과
  AOT(ahead-of-time) 컴파일을 모두 지원합니다.

* **웹 플랫폼**: 웹을 대상으로 하는 앱을 경우,
  Dart는 개발(development) 또는 생산(production) 목적으로 컴파일이 가능합니다.
  웹 컴파일러는 Dart를 자바스크립트로 번역합니다.

<img 
  src="/assets/img/Dart-platforms.svg" 
  width="800" 
  alt="An illustration of the targets supported by Dart">

[Flutter 프레임워크]({{site.flutter}})는 Dart 플랫폼을 기반으로 하는 인기 잇는
멀티 플랫폼 UI 툴킷으로 IOS, 안드로이드, macOS, Windows, Linux 및 웹에서 실행되는
UI 경험을 구축하기 위해 툴링과 UI 라이브러리를 제공합니다.

#### Dart 네이티브 (머신 코드 JIT와 AOT) {#native-platform}

개발하는 동안에 빠른 개발 주기는 반복에 중요합니다.
Dart VM은 증분 재컴파일(핫 리로드 지원), 실시간 데이터 수집 ([DevTools](/tools/dart-devtools) 구동)
및 풍부한 디버깅 지원이 있는 JIT(Just-In-Time) 컴파일러를 제공합니다.

프로덕션 백엔드를 배포하든 앱스토어에 퍼블리싱하든 상관없이,
앱을 배포할 준비가 되면 Dart의 AOT(Ahead-Of-Time) 컴파일러가
네이티브 ARM 또는 x64 머신 코드로 컴파일합니다.
AOT로 컴파일된 앱은 일정하고 짧은 시작 시간으로 실행됩니다.

AOT로 컴파일된 코드는 sound Dart 타입 시스템을 따르고
빠른 객체 할당과 
[generational 가비지 컬렉터](https://medium.com/flutter-io/flutter-dont-fear-the-garbage-collector-d69b3ff1ca30)
를 사용하는 효과적인 Dart 런타임에서 실행됩니다.

더 자세히 알고 싶다면 다음을 참고하세요:
* [시작: 커맨드 라인과 서버 앱](/tutorials/server/get-started)
* [JIT 또는 AOT를 머신 코드로 컴파일하여 실행하기 위한 'dart' 도구](/tools/dart-tool)
* [커맨드라인 앱 작성](/tutorials/server/cmdline)
* [HTTP 서버 작성](/tutorials/server/httpserver)

#### Dart 웹 (JavaScript dev & prod) {#web-platform}

Dart 웹은 Javascript로 구동되는 웹 플랫폼에서 Dart 코드를 실행할 수 있게 해줍니다.
Dart 웹을 사용하면 Dart 코드를 Javascript 코드로 컴파일하여 브라우저에서 실행합니다.
(예: [Chrome](https://www.google.com/chrome/)내 [V8](https://v8.dev/))

Dart 웹은 두개의 컴파일 모드를 가지고 있습니다:

* 빠른 개발자 사이클이 가능한 증분 개발 컴파일러
* 빠르고 컴팩트하며 배포가능한 Javascript로 Dart 코드를 컴파일 하는 최적화된 프로덕션 컴파일러.
  이러한 효율성은 죽은 코드 제거(dead-code elimination)와 같은 기술에서 비롯됩니다.
* An optimizing WebAssembly (WasmGC) production compiler which compiles Dart
code to super-fast, deployable WebAssembly GC code.

More information:

* [Build a web app with Dart](/web/get-started)
* [`dart compile js`](/tools/dart-compile#js)
* [`webdev` 툴](/tools/webdev)
* [웹 배포 팁](/web/deployment)
* [WebAssembly compilation](/web/wasm)

#### Dart 런타임 {#runtime}

플랫폼과 컴파일 방법에 상관없이 코드를 실행할 때 Dart 런타임이 필요합니다.
런타임은 다음의 중요한 역할을 수행합니다:

* 메모리 관리:
  Dart는 사용되지 않은 메모리가 가비지 컬렉터(GC)에
  의해 회수되는 관리된 메모리 모델을 사용합니다.

* Dart 타입 시스템을 따름:
  Dart의 타입 체킹은 대부분 정적이지만 (컴파일 타임),
  몇몇 타입 체킹은 유동적입니다 (런타임).
  예를 들어, Dart 런타임은
  [타입 체크와 캐스트 연산자](/language/operators#타입-테스트-연산자)로
  동적인 체크를 수행합니다.

* [Isolate](/language/concurrency) 관리:
  Dart 런타임은 일반적으로 코드를 실행하는 메인 isolate와
  앱을 생성하는 다른 isolate를 제어합니다.

네이티브 플랫폼에서 Dart 런타임은 자동으로 self-contained 실행 파일에
포함되며 [`dart run`](/tools/dart-run) 커맨드로 제공되는
Dart VM의 일부입니다.

## Dart 학습 {#learning-dart}

Dart를 학습하는 여러가지 방법이 있습니다. 다음은 우리가 추천하는 방법입니다:

* Dart 코드를 위한 웹 기반 실행 환경인 DartPad를 사용해 [브라우저에서 Dart를 체험하세요]({{site.dartpad}}/).
* [Dart 언어의 주요 기능 사용법 배우세요](/language).
* 커맨드 라인 프로그램을 만들기 위한 Dart의 기본인 [Dart 튜토리얼 완료하세요](/tutorials/server/cmdline).
* Dart 전문가의 [온라인 교육][udemy]을 참고하세요.
* Dart 핵심 라이브러리에 대한 [API 문서 살펴보세요]({{site.dart-api}}).
* [Dart 프로그래밍 책](/resources/books)을 참고하세요.

[udemy]: https://www.udemy.com/course/complete-dart-guide/?couponCode=NOV-20
