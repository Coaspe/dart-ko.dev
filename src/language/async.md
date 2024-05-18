---
title: 비동기 지원
description: Dart 언어를 사용한 비동기 코드 작성법을 알려드립니다.
short-title: 비동기
prevpage:
  url: /language/concurrency
  title: Concurrency
nextpage:
  url: /null-safety

---

<?code-excerpt replace="/ *\/\/\s+ignore_for_file:[^\n]+\n//g; /(^|\n) *\/\/\s+ignore:[^\n]+\n/$1/g; /(\n[^\n]+) *\/\/\s+ignore:[^\n]+\n/$1\n/g; / *\/\/\s+ignore:[^\n]+//g; /([A-Z]\w*)\d\b/$1/g"?>

Dart의 라이브러리에는 [`Future`][] 또는 [`Stream`][] 객체를 반환하는 함수가 많습니다.
이런 함수들을 _비동기(asynchronous)_ 함수라고 합니다.
이 함수들은 I/O 같이 시간이 오래 걸릴 수도 있는
작업이 완료되기를 기다리지 않고, 값을 반환할 수 있게 해줍니다.

`async`나 `await` 같은 키워드들은 동기적인 코드처럼 보이는
비동기 코드를 이용해 비동기 프로그래밍을 가능하게 합니다.


## Future 다루기

완료된 Future의 결과를 사용하고 싶다면, 두 가지 옵션이 있습니다:

* Use `async` and `await`, as described here and in the
  [asynchronous programming codelab](/codelabs/async-await).
* Use the Future API, as described in the
  [`dart:async` documentation](/libraries/dart-async#future).

`async`나 `await`을 사용하는 코드는 비동기적이지만, 외관상 동기적인 코드와 비슷합니다.
예를 들어, 다음은 `await`을 사용해 비동기 함수의 결과를 기다리는 코드입니다:

<?code-excerpt "misc/lib/language_tour/async.dart (await-lookUpVersion)"?>
```dart
await lookUpVersion();
```

`await`을 사용하려면, 해당 코드는 `async`로 표시된 `async` 함수 안에 있어야 합니다:

<?code-excerpt "misc/lib/language_tour/async.dart (checkVersion)" replace="/async|await/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
Future<void> checkVersion() [!async!] {
  var version = [!await!] lookUpVersion();
  // version 변수를 사용...
}
{% endprettify %}

{{site.alert.note}}
  `async` 비동기 함수는 시간이 많이 걸리는 작업을 수행할 수 있지만
  이러한 작업을 기다리지 않습니다. 대신 `async` 함수는 첫 번째 `await` 구문을 찾을 때까지 실행됩니다.
  그 후에 `Future` object를 반환하고, `await` 구문이 끝난 뒤 코드 실행을 재개합니다.
{{site.alert.end}}

`try`, `catch`, `finally`를 사용하여 `await`을 사용한 코드의 에러를 다루고, 깔끔하게 정리하세요:

<?code-excerpt "misc/lib/language_tour/async.dart (try-catch)"?>
```dart
try {
  version = await lookUpVersion();
} catch (e) {
  // 버전을 조회할 수 없을 경우 ...
}
```

`async` 함수 안에 여러 개의 `await`를 사용해도 됩니다.
예를 들어, 다음의 코드는 3번 함수의 결과를 기다립니다:

<?code-excerpt "misc/lib/language_tour/async.dart (repeated-await)"?>
```dart
var entrypoint = await findEntryPoint();
var exitCode = await runExecutable(entrypoint, args);
await flushThenExit(exitCode);
```

<code>await <em>표현식</em></code>에서 <code><em>표현식</em></code>의 값은 보통 Future 입니다;
Future가 아니라면, 자동으로 Future가 값을 감싸게 됩니다.
이 Future 객체는 객체를 반환하는 약속(promise)을 나타냅니다.
<code>await <em>표현식</em></code>의 값은 반환된 해당 객체입니다.
await 표현식은 그 객체가 사용 가능해질 때까지 실행을 멈춥니다.

**await를 사용하면서 컴파일 타임 에러가 발생했다면, `await`가 `async` 함수 안에 있는지 확인해보세요.**
예를 들어, 앱의 `main()` 함수의 바디에 `await` 함수를 사용한다면,
`main()` 는 `async`로 마크되어 있어야 합니다:

<?code-excerpt "misc/lib/language_tour/async.dart (main)" replace="/async|await/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
void main() [!async!] {
  checkVersion();
  print('In main: version is ${[!await!] lookUpVersion()}');
}
{% endprettify %}

{{site.alert.note}}
  앞의 예제에서 `async` 함수 (`checkVersion()`)의 결과를 기다리지 않고 사용했습니다.
  이런 관행은 코드가 해당 함수의 실행이 끝났다고 가정하면 문제를 일으킵니다.
  이 문제를 피하기 위해 [unawaited_futures linter rule][]을 사용하세요.
{{site.alert.end}}

Future, `async`, 그리고 `await`을 사용해보며 더 자세히 배우고 싶다면,
[비동기 프로그래밍 코드랩](/codelabs/async-await)를 참고하세요.


## async 함수 선언

`async` 함수는 바디가 `async` 식별자로 표시된 함수입니다.

`asnyc` 키워드를 함수 앞에 표시하는 것은, 함수가 Future를 반환하게 합니다.
예를 들어, String을 반환하는 다음과 같은 동기식 함수가 있습니다:

<?code-excerpt "misc/lib/language_tour/async.dart (sync-lookUpVersion)"?>
```dart
String lookUpVersion() => '1.0.0';
```

이 함수를 async 함수로 만들면 Future 값을 반환합니다:

<?code-excerpt "misc/lib/language_tour/async.dart (async-lookUpVersion)"?>
```dart
Future<String> lookUpVersion() async => '1.0.0';
```

함수의 바디에서는 Future API를 사용할 필요가 없다는 것을 알아두세요.
Dart는 필요할 때 Future 객체를 생성합니다.
함수가 쓸모 있는 값을 반환하지 않는다면, 반환 타입을 `Future<void>`로 지정하세요.

Future, `async`, 그리고 `await`을 사용해보며 더 자세히 배우고 싶다면,
[비동기 프로그래밍 코드랩](/codelabs/async-await)를 참고하세요.

{% comment %}
TODO #1117: Where else should we cover generalized void?
{% endcomment %}


## Stream 다루기

두 가지 옵션을 사용하여 Stream에서 값을 가져올 수 있습니다:

* Use `async` and an _asynchronous for loop_ (`await for`).
* Use the Stream API, as described in the
  [`dart:async` documentation](/libraries/dart-async#stream).

{{site.alert.note}}
   `await for`의 사용이 코드를 더 간결하게 만드는지 확인하고,
   스트림의 모든 결과를 확실히 기다릴 것인지 정하세요.
   예를 들어, UI 이벤트 리스너는 끝임없는 이벤트의 스트림을 전송하므로 `await for` 를 사용하면 **안됩니다**.
{{site.alert.end}}

비동기 for 루프는 다음과 같은 형태를 가집니다:

<?code-excerpt "misc/lib/language_tour/async.dart (await-for)"?>
```dart
await for (varOrType identifier in expression) {
  // Stream이 값을 내놓을 때마다 실행됩니다.
}
```

위 <code><em>표현식</em></code>의 값은 반드시 Stream 타입이어야 합니다.
실행의 순서는 다음과 같습니다:

1. Stream이 값을 내놓을 때까지 기다립니다.
2. 도출된 값을 변수로 설정하여 for 루프의 바디를 실행합니다.
3. Stream이 끝날 때까지 1과 2를 반복합니다.

Stream에 대한 리스닝을 끝내고 싶다면,
for 루프를 끝내고 stream을 unsubscribe하는 `break`나 `return`을 사용하면 됩니다.

**비동기 for 루프를 사용할 때 컴파일 타임 에러가 발생했다면,
`await for`가 `async` 함수 안에 있는지 확인해보세요.**
예를 들어, 앱의 `main()` 함수에 비동기 for 루프를 사용한다면,
`main()` 는 `async`로 마크되어 있어야 합니다:

<?code-excerpt "misc/lib/language_tour/async.dart (number_thinker)" replace="/async|await for/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
void main() [!async!] {
  // ...
  [!await for!] (final request in requestServer) {
    handleRequest(request);
  }
  // ...
}
{% endprettify %}

For more information about Dart's asynchronous programming support,
check out the [`dart:async`](/libraries/dart-async) library documentation.

[`Future`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-async/Future-class.html
[`Stream`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-async/Stream-class.html
[unawaited_futures linter rule]: /tools/linter-rules/unawaited_futures
