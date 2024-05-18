---
title: Dart의 동시성
description: Isolate를 사용하여 멀티 프로세서 코어에서 병렬 코드를 실행하세요.
short-title: 동시성
prevpage:
  url: /language/modifier-reference
  title: Class modifiers reference
nextpage:
  url: /language/async
  title: 비동기
---

<?code-excerpt path-base="concurrency"?>

<style>
  article img {
    padding: 15px 0;
  }
</style>

This page contains a conceptual overview of how concurrent programming works in
Dart. It explains the event-loop, async language features, and isolates from
a high-level. For more practical code examples of using async features,
read the [Asynchrony support](/language/async).

Concurrent programming in Dart refers to both asynchronous APIs, like `Future`
and `Stream`, and *isolates*, which allow you to move processes to separate
cores.

All Dart code runs in isolates, starting in the default main isolate,
and optionally expanding to whatever subsequent isolates you
explicitly create. When you spawn a new isolate,
it has its own isolated memory, and its own event loop.
The event loop is what makes asynchronous and
concurrent programming possible in Dart.

## Event Loop

Dart’s runtime model is based on an event loop.
The event loop is responsible for executing your program's code,
collecting and processing events, and more.

As your application runs, all events are added to a queue,
called the *event queue*.
Events can be anything from requests to repaint the UI,
to user taps and keystrokes, to I/O from the disk.
Because your app can’t predict what order events will happen,
the event loop processes events in the order they're queued, one at a time.

![A figure showing events being fed, one by one, into the
event loop](/assets/img/language/concurrency/event-loop.png)

The way the event loop functions resembles this code:

```dart
while (eventQueue.waitForEvent()) {
  eventQueue.processNextEvent();
}
```

This example event loop is synchronous and runs on a single thread.
However, most Dart applications need to do more than one thing at a time. 
For example, a client application might need to execute an HTTP request, 
while also listening for a user to tap a button. 
To handle this, Dart offers many async APIs, 
like [Futures, Streams, and async-await](/language/async).
These APIs are built around this event loop.

For example, consider making a network request:

```dart
http.get('https://example.com').then((response) {
  if (response.statusCode == 200) {
    print('Success!')'
  }  
}
```

When this code reaches the event loop, it immediately calls the
first clause, `http.get`, and returns a `Future`.
It also tells the event loop to hold onto the callback in the `then()` clause
until the HTTP request resolves. When that happens, it should
execute that callback, passing the result of the request as an argument.

![Figure showing async events being added to an event loop and
holding onto a callback to execute later
.](/assets/img/language/concurrency/async-event-loop.png)

This same model is generally how the event loop handles all other
asynchronous events in Dart, such as [`Stream`][] objects.

[`Stream`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-async/Stream-class.html

## Asynchronous programming

This section summarizes the different types and syntaxes of asynchronous programming in Dart.
If you're already familiar with `Future`, `Stream`, and async-await,
then you can skip ahead to the [isolates section][].

[isolates section]: #isolates

### Futures

A `Future` represents the result of an asynchronous operation that will 
eventually complete with a value or an error.

In this sample code, the return type of `Future<String>` represents a 
promise to eventually provide a `String` value (or error).

<?code-excerpt "lib/future_syntax.dart"?>
```dart
Future<String> _readFileAsync(String filename) {
  final file = File(filename);

  // .readAsString() returns a Future.
  // .then() registers a callback to be executed when `readAsString` resolves.
  return file.readAsString().then((contents) {
    return contents.trim();
  });
}
```

### async-await 문법

The `async` and `await` keywords provide a declarative way to define
asynchronous functions and use their results.

다음 코드는 파일 I/O가 진행되는 동안 코드의 실행이 중단되는 동기식 코드입니다:

<?code-excerpt "lib/sync_number_of_keys.dart"?>
```dart
const String filename = 'with_keys.json';

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
const String filename = 'with_keys.json';

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

![Flowchart-like figure showing app code executing from start to exit, waiting
for native I/O in between](/assets/img/language/concurrency/basics-await.png)

### Streams

Dart also supports asynchronous code in the form of streams. Streams
provide values in the future and repeatedly over time. A promise to provide a
series of `int` values over time has the type `Stream<int>`.

In the following example, the stream created with `Stream.periodic`
repeatedly emits a new `int` value every second.

<?code-excerpt "lib/stream_syntax.dart"?>
```dart
Stream<int> stream = Stream.periodic(const Duration(seconds: 1), (i) => i * i);
```

#### await-for and yield

Await-for is a type of for loop that executes each subsequent iteration of the
loop as new values are provided. In other words, it’s used to “loop over”
streams. In this example, a new value will be emitted from the function
`sumStream` as new values are emitted from the stream that’s provided as an
argument. The `yield` keyword is used rather than `return` in functions that 
return streams of values.

<?code-excerpt "lib/await_for_syntax.dart"?>
```dart
Stream<int> sumStream(Stream<int> stream) async* {
  var sum = 0;
  await for (final value in stream) {
    yield sum += value;
  }
}
```

If you'd like to learn more about using `async`, `await`, `Stream`s and
`Future`s,  visit the [asynchronous programming codelab][].

[비동기 프로그래밍 codelab]: /codelabs/async-await

## Isolates

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
[뮤텍스 또는 락](https://en.wikipedia.org/wiki/Lock_(computer_science)) 그리고
[데이터 레이스](https://en.wikipedia.org/wiki/Race_condition#Data_race)를
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
시작되는 스레드 입니다:

![A figure showing a main isolate, which runs `main()`, responds to events,
and then exits](/assets/img/language/concurrency/basics-main-isolate.png)

단일 isolate 프로그램도 [async-await][]를 사용하여 비동기 작업이
완료될 때까지 기다렸다가 다음 코드를 진행하면 원할하게 실행할 수 있습니다.
제대로 작성된 앱은 빠른 시작 후 가능한 빨리 이벤트 사이클에 진입합니다.
앱은 필요하다면 비동기 명령을 사용하여 큐에 대기 중인 이벤트에 바로 응답합니다.

[async-await]: {{site.url}}/codelabs/async-await

### Isolate 생명 주기

다음 그림에서 볼 수 있듯이,
모든 isolate는 `main()` 함수 같은 Dart 코드를
실행하면서 시작합니다. 이 Dart 코드는 사용자 입력 처리나
파일 I/O와 같은 이벤트 리스너를 등록할 수 있습니다.
Isolate에서 실행된 Dart 코드가 종료된 후에도
이벤트를 처리해야 하는 경우에는 isolate가 계속 유지됩니다.
이벤트의 처리가 끝난 후, isolate는 종료됩니다.

![A more general figure showing that any isolate runs some code, optionally responds to events, and then exits](/assets/img/language/concurrency/basics-isolate.png)


### 이벤트 처리

클라이언트 앱에서 main isolate의 이벤트 큐에는 리페인트 요청, 클릭된 알림 또는
기타 UI 이벤트가 포함될 수 있습니다. 예를 들어, 다음 그림에서
리페인트 이벤트 이후 하나의 탭 이벤트 그리고 두 개의 리페인트 이벤트가 큐에 진입합니다.
이벤트 루프는 FIFO(First In First Out) 순서로 큐에 있는 이벤트를 처리합니다.

![A figure showing events being fed, one by one, into the event loop](/assets/img/language/concurrency/event-loop.png)

`main()` 메서드가 실행된 후에 이벤트 큐의 처리가 시작되며,
이때 리페인트 이벤트가 첫 번째로 처리됩니다. 그 뒤로 main isolate는
탭 이벤트를 처리하고 이어서 리페인트 이벤트를 처리합니다.

![A figure showing the main isolate executing event handlers, one by one](/assets/img/language/concurrency/event-handling.png)

동기 명령이 긴 처리 시간을 소요한다면,
앱의 반응성은 떨어집니다.
다음 그림에서, 탭을 처리하는 코드는 긴 시간을 소요하여,
이어지는 이벤트의 처리가 지연됩니다.
앱은 마치 멈춰있는 것처럼 보일 것이고,
앱이 수행하는 애니메이션은 버벅거릴 것입니다.

![A figure showing a tap handler with a too-long execution time](/assets/img/language/concurrency/event-jank.png)

클라이언트 앱에서, 너무 긴 동기 명령은
[버벅거리는 UI 애니메이션][jank]을 야기합니다.
더 심해지면 UI가 완전히 반응하지 않을 수 있습니다.

[jank]: {{site.flutter-docs}}/perf/rendering-performance


### 백그라운드 워커

[큰 JSON 파일을 파싱][json]하는 것처럼 긴 시간을 소요하는 계산 때문에
UI가 반응하지 않는다면, 해당 계산을 워커 isolate로 옮기는 선택지가 있으며
일반적으로 이러한 isolate를 _백그라운드 워커_ 라고 합니다.
다음 그림에서 계산을 수행하는 간단한 워커 isolate를 생성합니다.
워커 isolate는 종료될 때 계산 결과를 메시지로 반환합니다.

[json]: {{site.flutter-docs}}/cookbook/networking/background-parsing

![A figure showing a main isolate and a simple worker isolate](/assets/img/language/concurrency/isolate-bg-worker.png)

각 Isolate는 메시지를 통해 객체를 전달할 수 있으며,
이 객체의 모든 내용은 전달 가능한 조건을 만족해야 합니다.
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
  [웹](#web)에서 `compute` 함수는 현재 이벤트 루프에서 지정된 함수를
  실행하는 것으로 대체됩니다.
  오직 네이티브 플랫폼을 대상으로 할 때
  사용자 친화적인 API를 위해 `Isolate.run()`을 사용하세요.
{{site.alert.end}}

[네이티브와 네이티브가 아닌 플랫폼]: /overview#platform
[Flutter의 `compute()` 함수]: {{site.flutter-api}}/flutter/foundation/compute-constant.html

#### 새로운 isolate에서 메소드 실행

아래의 main isolate는 새로운 isolate를 생성합니다:

<?code-excerpt "lib/simple_worker_isolate.dart (main)"?>
```dart
int slowFib(int n) => n <= 1 ? 1 : slowFib(n - 1) + slowFib(n - 2);

// Compute without blocking current isolate.
void fib40() async {
  var result = await Isolate.run(() => slowFib(40));
  print('Fib(40) = $result');
}
```

### Performance and isolate groups

When an isolate calls [`Isolate.spawn()`][], the two isolates have the same
executable code and are in the same _isolate group_. Isolate groups enable
performance optimizations such as sharing code; a new isolate immediately runs
the code owned by the isolate group. Also, `Isolate.exit()` works only when the
isolates are in the same isolate group.

In some special cases, you might need to use [`Isolate.spawnUri()`][], which
sets up the new isolate with a copy of the code that's at the specified URI.
However, `spawnUri()` is much slower than `spawn()`, and the new isolate isn't
in its spawner's isolate group. Another performance consequence is that message
passing is slower when isolates are in different groups.

[`Isolate.spawnUri()`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-isolate/Isolate/spawnUri.html

### Limitations of isolates

#### Isolates aren't threads

If you’re coming to Dart from a language with multithreading, it’d be reasonable
to expect isolates to behave like threads, but that isn’t the case. Each isolate
has its own state, ensuring that none of the state in an isolate is
accessible from any other isolate. Therefore, isolates are limited by their
access to their own memory.

For example, if you have an application with a
global mutable variable, that variable will be a separate
variable in your spawned isolate. If you mutate that variable in the spawned
isolate, it will remain untouched in the main isolate. This is how isolates are
meant to function, and it's important to keep in mind when you’re considering
using isolates.

#### Message types

Messages sent via [`SendPort`][]
can be almost any type of Dart object, but there are a few exceptions:

- Objects with native resources, such as [`Socket`][].
- [`ReceivePort`][]
- [`DynamicLibrary`][]
- [`Finalizable`][]
- [`Finalizer`][]
- [`NativeFinalizer`][]
- [`Pointer`][]
- [`UserTag`][]
- Instances of classes that are marked with `@pragma('vm:isolate-unsendable')`

Apart from those exceptions, any object can be sent.
Check out the [`SendPort.send`][] documentation for more information.

Note that `Isolate.spawn()` and `Isolate.exit()` abstract over `SendPort` 
objects, so they're subject to the same limitations.

[`SendPort.send`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-isolate/SendPort/send.html
[`Socket`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-io/Socket-class.html
[`DynamicLibrary`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-ffi/DynamicLibrary-class.html
[`Finalizable`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-ffi/Finalizable-class.html
[`Finalizer`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Finalizer-class.html
[`NativeFinalizer`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-ffi/NativeFinalizer-class.html
[`Pointer`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-ffi/Pointer-class.html
[`UserTag`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-developer/UserTag-class.html

<a id="web"></a>
## 웹에서의 동시성

All Dart apps can use `async-await`, `Future`, and `Stream`
for non-blocking, interleaved computations. The [Dart web platform][], however,
does not support isolates. Dart web apps can use [web workers][] to run scripts
in background threads similar to isolates. Web workers' functionality and
capabilities differ somewhat from isolates, though.

For instance, when web workers send data between threads, they copy the data
back and forth. Data copying can be very slow, though, especially for large
messages. Isolates do the same, but also provide APIs that can more efficiently
_transfer_
the memory that holds the message instead.

Creating web workers and isolates also differs. You can only create web workers
by declaring a separate program entrypoint and compiling it separately. Starting
a web worker is similar to using `Isolate.spawnUri` to start an isolate. You can
also start an isolate with `Isolate.spawn`, which requires fewer resources
because it
[reuses some of the same code and data](#performance-and-isolate-groups)
as the spawning isolate. Web workers don't have an equivalent API.

[Dart web platform]: /overview#platform
[web workers]: https://developer.mozilla.org/docs/Web/API/Web_Workers_API/Using_web_workers


## Additional resources

- If you’re using many isolates, consider
  the [`IsolateNameServer`][]
  in Flutter, or
  [`package:isolate_name_server`][] that provides
  similar functionality for non-Flutter Dart applications.
- Read more about [Actor model][], which Dart's isolates are based on.
- Additional documentation on `Isolate` APIs:
    - [`Isolate.exit()`][]
    - [`Isolate.spawn()`][]
    - [`ReceivePort`][]
    - [`SendPort`][]

[`IsolateNameServer`]: https://api.flutter.dev/flutter/dart-ui/IsolateNameServer-class.html
[`package:isolate_name_server`]: https://pub.dev/packages/isolate_name_server
[Actor model]: https://en.wikipedia.org/wiki/Actor_model
[`Isolate.run()`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-isolate/Isolate/run.html
[`Isolate.exit()`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-isolate/Isolate/exit.html
[`Isolate.spawn()`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-isolate/Isolate/spawn.html
[`ReceivePort`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-isolate/ReceivePort-class.html
[`SendPort`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-isolate/SendPort-class.html
