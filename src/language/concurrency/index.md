---
title: Dart의 동시성
description: Isolate를 사용하여 멀티 프로세서 코어에서 병렬 코드를 실행하세요.
---

<?code-excerpt path-base="concurrency"?>

<style>
  article img {
    padding: 15px 0;
  }
</style>

Dart는 async-await, isolate 그리고 `Future`, `Stream`과 같은 클래스로 동시 프로그래밍을 지원합니다.
이 페이지는 async-await, `Future`, `Stream` 그리고 isolate에 대해 다루며,
대부분은 isolate에 대한 설명입니다.

앱의 모든 Dart 코드는 _isolate_ 안에서 실행됩니다.
각 Dart isolate는 단일 실행 스레드를 가지고
다른 isolate와 변할 수 있는 객체를 공유하지 않습니다.
Isolate 사이의 커뮤니케이션은 메시지 패싱으로 이루어집니다.
대부분의 Dart 앱은 _main isolate_ 라는 하나의 isolate만 사용합니다.
멀티 프로세서 코어에서 병렬 코드 실행을 활성화하고 싶다면
추가적인 isolate를 생성하세요.

Dart의 isolate 모델은 운영 체제가 제공하는 프로세스와 스레드 같은
내재된 프리미티브로 만들어지만, Dart VM에서 이러한 프리미티브를 어떻게 사용하여
isolate를 구현하였는지는 이 페이지에서 다루지 않습니다.

## 비동기 타입과 문법

`Future`, `Stream` 그리고 async-await에 이미 익숙하다면,
[isolate 섹션][]으로 넘어가세요.

[isolate 섹션]: #isolate-작동-방식


### Future와 Stream 타입

Dart 언어와 라이브러리는 객체의 값을 미래에 얻을 수 있다는 것을 나타내기 위해
`Future`와 `Stream`을 사용합니다. 예를 들어, 결국에 `int` 값을 얻게 되는
약속(promise)은 `Future<int>` 타입입니다.
`int`의 시리즈를 얻을 수 있는 약속은 `Stream<int>` 타입입니다.

다른 예로, 파일을 읽을 때 dart:io 메서드를 사용할 수 있습니다.
동기 `File` 메서드인 [`readAsStringSync()`][]는 파일을 동기적으로 읽습니다.
즉, 파일을 모두 읽거나 에러가 발생하기 전까지 코드 실행을 막습니다.
그런 다음 메서드는 `String` 타입의 객체를 반환하거나 예외를 발생시킵니다.
같은 작업을 수행하는 비동기 함수인 [`readAsString()`][]은
즉시 `Future<String>` 타입의 객체를 반환합니다.
미래의 어느 시점에서, `Future<String>`은 작업을 끝내 문자열 값 또는 에러를 반환합니다.

[`readAsStringSync()`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-io/File/readAsStringSync.html
[`readAsString()`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-io/File/readAsString.html


#### 비동기 코드가 중요한 이유

대부분의 앱은 동시에 여러가지 작업을 수행해야하기 때문에
메서드의 동기, 비동기 여부는 중요합니다.

비동기 계산은 종종 현재 Dart 코드 외부에서 수행한 계산의 결과입니다.
이런 계산들은 즉시 끝나지 않으며 결과가 나올 때까지 Dart 코드의 실행을 중지해야 할 수도 있습니다.
예를 들어, 비동기 코드는 앱이 HTTP 요청을 보낸 후 완료되기 전에
디스플레이를 갱신하거나 유저 입력에 반응할 수 있습니다.

비차단 I/O, HTTP 요청 또는 브라우저와의 상호 작용 같은 운영 체제 호출이 비동기 작업에 해당합니다.
Dart의 isolate를 사용하여 계산하거나 타이머가 트리거될 때까지 기다리는 경우도 있습니다.
비동기 작업들은 다른 스레드에서 실행되거나 운영 체제 또는 Dart 런타임에 처리되어 계산과 함께 Dart 코드가 동시에 실행될 수 있습니다.


### async-await 문법

`async`와 `await` 키워드는 비동기 함수와 그 결과를 사용하는 선언적인 방법을 제공합니다.

다음 코드는 파일 I/O가 진행되는 동안 코드의 실행이 중단되는 동기식 코드입니다:

<?code-excerpt "lib/sync_number_of_keys.dart"?>
```dart
void main() {
  // 데이터 읽기.
  final fileData = _readFileSync();
  final jsonData = jsonDecode(fileData);

  // 데이터 사용.
  print('Number of JSON keys: ${jsonData.length}');
}

String _readFileSync() {
  final file = File(filename);
  final contents = file.readAsStringSync();
  return contents.trim();
}
```

다음은 비동기식으로 변경한(하이라이트된 곳) 코드입니다:

<?code-excerpt "lib/async_number_of_keys.dart" replace="/async|await|readAsString\(\)/[!$&!]/g; /Future<\w+\W/[!$&!]/g;"?>
{% prettify dart tag=pre+code %}
void main() [!async!] {
  // 데이터 읽기.
  final fileData = [!await!] _readFileAsync();
  final jsonData = jsonDecode(fileData);

  // 데이터 사용.
  print('Number of JSON keys: ${jsonData.length}');
}

[!Future<String>!] _readFileAsync() [!async!] {
  final file = File(filename);
  final contents = [!await!] file.[!readAsString()!];
  return contents.trim();
}
{% endprettify %}

`main()` 함수는 네이티브 코드(파일 I/O)가 실행되는 동안
이벤트 핸들러 같은 Dart 코드가 CPU를 사용할 수 있도록
`_readFileAsync()` 앞에 `await` 키워드를 사용하였습니다.
`await`을 사용하는 것은 `_readFileAsync()`가 반환하는 `Future<String>`을
`String`으로 변환하는 효과를 가지고 있습니다.
결과적으로, `contents` 변수는 암묵적으로 `String` 타입을 가집니다.

{{site.alert.note}}
  `await` 키워드는 `async`로 표시된 함수에서만 작동합니다.
{{site.alert.end}}

다음 그림에서 볼 수 있듯이,
Dart 가상 머신 (VM) 또는 운영 체제(OS)에서 `readAsString()`이 non-Dart 코드를 실행할 때,
Dart 코드는 중단됩니다.
`readAsString()`이 값을 반환하고 나면, Dart 코드는 재개됩니다.

![Flowchart-like figure showing app code executing from start to exit, waiting for native I/O in between](/language/concurrency/images/basics-await.png)

`async`, `await`, 그리고 future에 대해 더 학습하고 싶다면,
[비동기 프로그래밍 codelab][]을 방문하세요.

[비동기 프로그래밍 codelab]: /codelabs/async-await


## Isolate 작동 방식

대부분의 현대 디바이스들은 멀티 코어 CPU를 가집니다.
이러한 많은 코어를 활용하기 위해,
개발자들은 종종 동시에 실행되는 공유 메모리 스레드를 사용합니다.
그러나, 공유 상태 동시성은
[에러가 발생하기 쉽고](https://en.wikipedia.org/wiki/Race_condition#In_software)
복잡한 코드로 이어질 수 있습니다.

Dart 코드는 스레드가 아닌 isolate의 내부에서 실행됩니다.
각 isolate는 자신의 메모리 힙을 가지고,
다른 isolate에서 자신의 상태에 접근할 수 없습니다.
공유하는 메모리가 없기 때문에, 
[뮤텍스, 락](https://en.wikipedia.org/wiki/Lock_(computer_science))을
고려할 필요가 없습니다.

Isolate를 사용하면 Dart 코드가 가능한 추가 프로세서 코어를 사용하여 여러가지 독립된 작업을
한 번에 수행할 수 있습니다. Isolate는 스레드, 프로세스와 비슷하지만,
각 isolate는 고유한 메모리와 이벤트 루프를 작동시키는 단일 스레드를 가지고 있습니다.

{{site.alert.info}}
  **Platform note:**
    오직 [Dart 네이티브 플랫폼][]만 isolate를 가지고 있습니다.
    Dart 웹 플랫폼에 대해 더 학습하고 싶다면,
    [웹에서의 동시성](#웹에서의-동시성) 섹션을 참고하세요.
{{site.alert.end}}

[Dart 네이티브 플랫폼]: /overview#platform

### Main isolate

경우에 따라 isolate에 대해 전혀 고려할 필요가 없습니다.
기본적으로 Dart 프로그램은 main isolate에서 실행됩니다.
다음 그림에서 볼 수 있듯이 main isolate는 프로그램의 실행이
시작되는 스레드 입니다.

![A figure showing a main isolate, which runs `main()`, responds to events, and then exits](/language/concurrency/images/basics-main-isolate.png)

단일 isolate 프로그램도 async-await를 사용하여 비동기 작업이
완료될 때까지 기다렸다가 다음 코드를 진행하면 원할하게 실행할 수 있습니다.
제대로 작성된 앱은 빠른 시작 후 가능한 빨리 이벤트 사이클에 진입합니다.
앱은 필요하다면 비동기 명령을 사용하여 큐에 대기 중인 이벤트에 바로 응답합니다.


### Isolate 생명 주기

다음 그림에서 볼 수 있듯이,
모든 isolate는 `main()` 함수 같은 Dart 코드를
실행하면서 시작합니다. 이 Dart 코드는 사용자 입력 처리나
파일 I/O와 같은 이벤트 리스너를 등록할 수 있습니다.
Isolate에서 실행된 Dart 코드가 종료된 후에도
이벤트를 처리해야 하는 경우에는 isolate가 계속 유지됩니다.
이벤트의 처리가 끝난 후, isolate는 종료됩니다.

![A more general figure showing that any isolate runs some code, optionally responds to events, and then exits](/language/concurrency/images/basics-isolate.png)


### 이벤트 처리

클라이언트 앱에서 main isolate의 이벤트 큐에는 리페인트 요청, 클릭된 알림 또는
기타 UI 이벤트가 포함될 수 있습니다. 예를 들어, 다음 그림에서
리페인트 이벤트 이후 하나의 탭 이벤트 그리고 두 개의 리페인트 이벤트가 큐에 진입합니다.
이벤트 루프는 FIFO(First In First Out) 순서로 큐에 있는 이벤트를 처리합니다.

![A figure showing events being fed, one by one, into the event loop](/language/concurrency/images/event-loop.png)

`main()` 메서드가 실행된 후에 이벤트 큐의 처리가 시작되며,
이때 리페인트 이벤트가 첫 번째로 처리됩니다. 그 뒤로 main isolate는
탭 이벤트를 처리하고 이어서 리페인트 이벤트를 처리합니다.

![A figure showing the main isolate executing event handlers, one by one](/language/concurrency/images/event-handling.png)

동기 명령이 긴 처리 시간을 소요한다면,
앱의 반응성은 떨어집니다.
다음 그림에서, 탭을 처리하는 코드는 긴 시간을 소요하여,
이어지는 이벤트의 처리가 지연됩니다.
앱은 마치 멈춰있는 것처럼 보일 것이고,
앱이 수행하는 애니메이션은 버벅거릴 것입니다.

![A figure showing a tap handler with a too-long execution time](/language/concurrency/images/event-jank.png)

클라이언트 앱에서, 너무 긴 동기 명령은
[버벅거리는 UI 애니메이션][jank]을 야기합니다.
더 심해지면 UI가 완전히 반응하지 않을 수 있습니다.

[jank]: {{site.flutter-docs}}/perf/rendering-performance


### 백그라운드 워커

[큰 JSON 파일을 파싱][json]하는 것처럼 긴 시간을 소요하는 계산 때문에
UI가 반응하지 않는다면, 해당 계산을 워커 isolate로 옮기는 선택지가 있으며
일반적으로 이러한 isolate를 _백그라운드 워커_ 라고 합니다.
다음 그림에서 계산을 수행하고 종료되는 간단한 워커 isolate를 생성합니다.
워커 isolate는 종료될 때 계산 결과를 메시지로 반환합니다.

[json]: {{site.flutter-docs}}/cookbook/networking/background-parsing

![A figure showing a main isolate and a simple worker isolate](/language/concurrency/images/isolate-bg-worker.png)

각 Isolate는 메시지를 통해 객체를 전달할 수 있으며,
이 객체의 모든 내용은 전달 가능한 조건을 만족해야합니다.
모든 객체가 전달 조건을 만족하는 것은 아니며, 조건을 충족하지 못할 경우
메시지 전송이 실패합니다. 예를 들어, `List<Object>`를 전송하려면 해당 리스트에
있는 모든 요소가 전달될 수 있는지 확인해야 합니다. `Socket`은 전송할 수 없기 때문에
리스트에 `Socket`이 있다면 전송에 실패합니다.

메시지로 전송할 수 있는 객체에 대해 알고 싶다면 [`send()` 메소드][] API 문서를 참고하세요.

워커 isolate는 파일을 읽고 쓰는 것과 같은 I/O, 타이머 설정 등을 수행할 수 있습니다.
Isolate는 자신만의 메모리를 가지고 있고 main isolate와 상태를 공유하지 않습니다.
워커 isolate를 블락해도 다른 isolate에 영향을 미치지 않습니다.

[`send()` 메소드]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-isolate/SendPort/send.html


## 코드 샘플

이번 섹션에서는 `Isolate` API를 이용하여
isolate를 구현하는 예제에 대해 이야기 해봅니다.

{{site.alert.flutter-note}}
  웹이 아닌 플랫폼에서 Flutter를 사용한다면,
  `Isolate` API를 직접적으로 사용하기 보다
  [Flutter `compute()` 함수][]을 사용하세요.
  `compute()` 함수는 단일 함수를 워커 isolate로 호출하는
  간단한 방법입니다.
{{site.alert.end}}

 [Flutter `compute()` 함수]: {{site.flutter-docs}}/cookbook/networking/background-parsing#4-move-this-work-to-a-separate-isolate


### 간단한 워커 isolate 구현

이번 섹션에서 워커 isolate를 생성하는
main isolate를 구현합니다.
워커 isolate는 함수를 실행하고 main isolate에게 단일 메시지를
전송하며 종료합니다. [`Isolate.run()`][] 함수는 워커 isolate를
설정하고 관리하는 단계를 단순화 시켜줍니다:

1. Isolate를 시작하고 생성합니다
2. 생성된 isolate에서 함수를 실행합니다
3. 결과를 캡처합니다
4. Main isolate로 결과를 반환합니다
5. 작업이 완료되면 isolate를 종료합니다.
6. 예외와 에러를 확인, 캡처 그리고 발생시켜 main isolate에 알립니다.

[`Isolate.run()`]: {{site.dart-api}}/dev/dart-isolate/Isolate/run.html

{{site.alert.flutter-note}}
  Flutter를 사용하고 있다면,
  `Isolate.run()` 대신에
  [Flutter의 `compute()` 함수][]의 사용을 고려해보세요.
  `compute` 함수는 [네이티브와 네이티브가 아닌 플랫폼][]에서
  모두 사용이 가능합니다. 보다 사용자 친화적인 API를 위해 네이티브 플랫폼을
  대상으로 할 때 `Isolate.run()`을 사용하세요.
{{site.alert.end}}

[네이티브와 네이티브가 아닌 플랫폼]: /overview#platform
[Flutter의 `compute()` 함수]: {{site.flutter-api}}/flutter/foundation/compute-constant.html

#### 새로운 isolate에서 메소드 실행

아래의 main isolate는 새로운 isolate를 생성합니다:

<?code-excerpt "lib/simple_worker_isolate.dart (main)"?>
```dart
void main() async {
  // 데이터 읽기.
  final jsonData = await Isolate.run(_readAndParseJson);

  // 데이터 사용.
  print('Number of JSON keys: ${jsonData.length}');
}
```

생성된 isolate는 첫 번째 인자로 주어진
함수인 `_readAndParseJson`을 실행합니다:

<?code-excerpt "lib/simple_worker_isolate.dart (spawned)"?>
```dart
Future<Map<String, dynamic>> _readAndParseJson() async {
  final fileData = await File(filename).readAsString();
  final jsonData = jsonDecode(fileData) as Map<String, dynamic>;
  return jsonData;
}
```

1. `Isolate.run()`은 백그라운드 워커인 isolate를 생성하고
   `main()`은 결과를 기다립니다.

2. 생성된 isolate는 `run()`의 인자로 넘겨진 함수를 (위에서는 `_readAndParseJson()`) 실행합니다.

3. `Isolate.run()`은 `return`이 반환하는 결과를 main isolate에 전달하고
   워커 isolate를 셧다운합니다.

4. 워커 isolate는 결과를 홀딩하고 있는 메모리를 main isolate에게 *전달*합니다.
   데이터를 *복사하지 않습니다*. 워커 isolate는 해당 객체를
   전달할 수 있는지 검증하는 작업을 수행합니다.

`_readAndParseJson()` 함수는 main isolate에서 직접
실행할 수도 있는 비동기 함수입니다. 하지만 `Isolate.run()`을 사용하여
실행하면 동시성이 활성화됩니다. 워커 isolate는 `_readAndParseJson()`의
계산을 완전히 추상화하며 main isolate를 블락하지 않고 작업을 완료할 수 있습니다.

Main isolate의 코드는 계속해서 실행되기 때문에 `Isolate.run()`의 결과는 항상 Future 입니다.
Main isolate와 워커 isolate는 동시에 실행되기 때문에
워커 isolate가 실행하는 계산이 동기적이든 아니든 main isolate에 영향을 주지 않습니다.

For the complete program, check out the [send_and_receive.dart][] sample.

{% comment %}
TODO:
Should create a diagram for the current example.
Previous example's diagram and text for reference:

  The following figure illustrates the communication between
  the main isolate and the worker isolate:
  
  ![A figure showing the previous snippets of code running in the main isolate and in the worker isolate](/language/concurrency/images/isolate-api.png)
{% endcomment %}

#### Isolate에 클로저 전달

`run()`을 사용하여 Main isolate에서 워커 isolate를 생성할 때,
직접적으로 함수 리터럴 또는 클로저를 사용할 수 있습니다.

<?code-excerpt "lib/simple_isolate_closure.dart (main)"?>
```dart
void main() async {
  // 데이터 읽기.
  final jsonData = await Isolate.run(() async {
    final fileData = await File(filename).readAsString();
    final jsonData = jsonDecode(fileData) as Map<String, dynamic>;
    return jsonData;
  });

  // 데이터 사용.
  print('Number of JSON keys: ${jsonData.length}');
}
```

이 예제는 이전의 예제외 같은 작업을 수행합니다.
새로운 isolate를 생성하고 작업을 수행하며 마지막으로 결과를 반환합니다.

그러나, 이번 예제에서는 isolate에 [클로저][Closure]를 전달합니다.
클로저는 코드를 작성하는 방법과 작동하는 방식에서 일반적인 named 함수보다 제약이 적습니다.
이번 예제에서, `Isolate.run()`은 로컬 코드처럼 보이는 코드를 동시에 실행합니다.
이런 맥락에서 `run()`이 코드를 "병렬적으로 실행"하기 위해 흐름 제어 연산자 처럼
작동한다고 생각할 수도 있습니다.

[Closure]: /language/functions#익명-함수

### Isolate 사이에 다수의 메시지 전송

`Isolate.run()`은 isolate를 관리하는
다음과 같은 유용한 저수준의 API들을 추상화합니다:

* [`Isolate.spawn()`][], [`Isolate.exit()`][]
* [`ReceivePort`][], [`SendPort`][]

* [`Isolate.spawn()`][], [`Isolate.exit()`][]
* [`ReceivePort`][], [`SendPort`][]

[`Isolate.exit()`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-isolate/Isolate/exit.html
[`Isolate.spawn()`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-isolate/Isolate/spawn.html
[`ReceivePort`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-isolate/ReceivePort-class.html
[`SendPort`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-isolate/SendPort-class.html

Isolate 기능을 더 정밀하게 제어하고 싶다면 위의 저수준 API를 사용하면 됩니다.
예를 들어, `run()`은 하나의 메시지를 반환한 후 isolate를 셧다운합니다.
Isolate 사이에 다수의 메시지를 전송하고 싶다면 어떻게 해야할까요?
`run()`의 구현에서 `SendPort`의 [`send()` 메소드][]를 조금 다르게 수정하여
isolate를 설정하면 됩니다.

다음과 그림과 같이 main isolate에서 워커 isolate로
요청 메시지를 보낸 후 하나 또는 다수의 응답 메시지를 보내는 패턴이 자주 사용됩니다.

![A figure showing the main isolate spawning the isolate and then sending a request message, which the worker isolate responds to with a reply message; two request-reply cycles are shown](/language/concurrency/images/isolate-custom-bg-worker.png)

Isolate 사이에서 다수의 메시지를 송수신하는
장기 실행 isolate를 생성하는 방법은 [long_running_isolate.dart][] 샘플에서 확인하세요.

{% assign samples = "https://github.com/dart-lang/samples/tree/main/isolates" %}

[isolate 샘플]: {{ samples }}
[send_and_receive.dart]: {{ samples }}/bin/send_and_receive.dart
[long_running_isolate.dart]: {{ samples }}/bin/long_running_isolate.dart


## 성능과 isolate 그룹

Isolate가 [`Isolate.spawn()`][]을 호출하면,
두 개의 isolate는 동일한 실행 가능 코드를 가지고
같은 _isolate 그룹_ 에 속합니다.
Isolate 그룹은 코드 공유 같은 성능 최적화가 가능합니다;
코드 공유를 활성화하면 새로운 isolate가 isolate 그룹이 가지고 있는 코드를
즉시 실행합니다. 또한, `Isolate.exit()`은 해당 isolate들이 동일한
isolate 그룹에 있을 때 작동합니다.

가끔 [`Isolate.spawnUri()`][]를 사용하여 특정 URI에 해당하는
코드 사본을 사용하여 새로운 isolate를 셋업 할 때가 있습니다.
그러나, `spawnUri()`는 `spawn()` 보다 느리고
그렇게 생성된 isolate는 해당 isolate를 생성한 isolate와 같은
그룹에 속하지 않습니다. 또한 다른 그룹에 속한 isolate
사이의 메시지 패싱은 느립니다.

[`Isolate.spawnUri()`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-isolate/Isolate/spawnUri.html

{{site.alert.flutter-note}}
  Flutter는 `Isolate.spawnUri()`를 지원하지않습니다.
{{site.alert.end}}

## 웹에서의 동시성

모든 Dart 앱은 비차단, 인터리브 계산을 위해
`async-await`, `Future`, 그리고 `Stream`을 사용할 수 있습니다.
그러나 [Dart 웹 플랫폼][]은 isolate를 지원하지 않습니다.
Dart 웹앱은 isolate와 유사하게 [웹 워커][]를 사용하여
백그라운드 스레드에서 스크립트 실행이 가능합니다.
그러나 웹 워커의 기능과 능력은 isolate와 다소 다릅니다.

예를 들어, 웹 워커가 스레드 사이에서 데이터를 전송할 때,
데이터를 복사합니다. 그러나 데이터 복사는 특히 큰 메시지의 경우
매우 느릴 수 있습니다. Isolate도 비슷하게 작동하지만
메시지를 저장하는 메모리를 더 효율적으로 _전송_ 할 수 있는 API를 제공합니다.

웹 워커와 isolate를 생성하는 것 또한 서로 다릅니다.
웹 워커를 생성하고 싶다면 분리된 프로그램 엔트리를 선언하고
그것을 따로 컴파일 해야 합니다. 웹 워커를 생성하는 것은
`Isolate.spawnUri`를 사용하여 isolate를 생성하는 것과 유사합니다.
`Isoatel.spawn`을 사용하여 isolate를 생성하면 생성을 호출한 isolate와
[일부 동일한 코드와 데이터를 재사용](#성능과-isolate-그룹)
하기 때문에 더 적은 리소스를 사용합니다. 웹 워커는 이와 동일한 API가 없습니다.

[Dart 웹 플랫폼]: /overview#platform
[웹 워커]: https://developer.mozilla.org/docs/Web/API/Web_Workers_API/Using_web_workers
