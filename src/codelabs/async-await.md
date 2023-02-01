---
title: "비동기 프로그래밍: futures, async, await"
description: DartPad로 비동기 코드를 작성법을 배우고 연습해봅시다!
js: [{url: 'https://dartpad.dev/inject_embed.dart.js', defer: true}]
---
<?code-excerpt replace="/ *\/\/\s+ignore_for_file:[^\n]+\n//g; /(^|\n) *\/\/\s+ignore:[^\n]+\n/$1/g; /(\n[^\n]+) *\/\/\s+ignore:[^\n]+\n/$1\n/g"?>
<?code-excerpt plaster="none"?>

이 코드랩에서는 future와 `async`, `await` 키워드를 사용하여
비동기 코드 작성법을 배웁니다. 임베디드 DartPad 에디터로
예제 코드를 실행하고 연습하며 배운 것을 테스트할 수 있습니다.

이 코드랩을 최대한 활용하려면 다음을 알고 있어야 합니다:

* [기본 Dart 문법](/samples)에 대한 지식.
* 다른 언어에서 비동기 코드를 작성해 본 경험.

이 코드랩에서는 다음의 주제를 다룹니디:

* `async`와 `await` 키워드를 언제 어떻게 사용해야 하는지.
* `async`와 `await`의 사용이 실행 순서에 어떤 영향을 미치는지.
* `try-catch`을 사용하여 어떻게 `async` 함수에
  사용된 비동기 호출에서 발생하는 에러를 다루는지.

40-60분이면 이 코드랩을 완료할 수 있습니다.

{{site.alert.note}}
  이 페이지는 예제와 연습을 수행할 때 임베디드 DartPad를 사용합니다.
  {% include dartpads-embedded-troubleshooting.md %}
{{site.alert.end}}

## 비동기 코드는 왜 중요한가?

비동기 작업을 사용하면 다른 작업이 완료되기를 기다리는 동안
프로그램이 작업을 완료할 수 있습니다. 다음은 자주 사용되는 비동기 작업입니다:

* 네트워크를 통해 데이터를 가져올 때.
* 데이터 베이스에 쓰기를 수행할 때.
* 파일로부터 데이터를 읽을 때.

이와 같은 비동기 작업은 결과를 보통 `Future`로 제공하고,
결과가 다수의 파트를 가지고 있다면 `Stream`으로 제공합니다.
이러한 작업들은 프로그램에 비동기성을 도입합니다. 이 비동기성을 다루기 위해서
다른 일반적인 Dart 함수들도 비동기화되어야 합니다.

이러한 비동기 결과와 상호작용하려면 `async`와 `await` 키워드를
사용하면 됩니다. 대부분의 비동기 함수들은 본질적으로 비동기 연산에
의존하는 비동기 Dart 함수입니다.

### 예제: 옳지 않는 비동기 함수 사용례

다음 예제는 비동기 함수(`fetchUserOrder()`)를
잘못 사용한 경우를 보여줍니다. 이 예제를 실행하기 전에, 문제가 무엇일지 생각해보세요 --
출력이 어떻게 될까요?

<?code-excerpt "async_await/bin/get_order_sync_bad.dart" remove="Fetching"?>
```dart:run-dartpad:height-380px:ga_id-incorrect_usage
// 이 예제 같이 비동기 Dart 코드를 작성하면 *안 됩니다*.

String createOrderMessage() {
  var order = fetchUserOrder();
  return 'Your order is: $order';
}

Future<String> fetchUserOrder() =>
    // 이 함수가 더 복잡하고 느린 함수라고 상상해보세요.
    Future.delayed(
      const Duration(seconds: 2),
      () => 'Large Latte',
    );

void main() {
  print(createOrderMessage());
}
```

위의 예제가 `fetchUserOrder()` 함수의 비동기 작업
결과 값을 출력하지 못하는 이유는 다음과 같습니다:

* `fetchUserOrder()`는 일정 시간의 딜레이 이후에
  사용자의 주문을 설명하는 문자열("Large Latte")을 제공합니다.
* `createOrderMessage()`는 사용자의 주문을 받기 위해 
  `fetchUserOrder()`를 호출해야 하고, 이 함수가 끝날 때까지 기다립니다.
  `createOrderMessage()` 함수는 `fetchUserOrder()`가 끝나는 것을 *기다리지 않기* 때문에,
  `createOrderMessage()`는 `fetchUserOrder()`가 제공하는 문자열을 얻지 못합니다.
* 대신에, `createOrderMessage()`는 작업이 완료되지 않은 future를 얻습니다.
  Future에 대해서는 다음 섹션에서 배웁니다.
* `createOrderMessage()`가 사용자의 주문을 나타내는 값을 얻지 못했기 때문에,
  콘솔에 "Large Latte"를 출력하지 못했고,
  "Your order is: Instance of '_Future\<String\>'"가 대신 출력됩니다.

다음 섹션에서 `fetchUserOrder()`가 원하는 값("Large Latte")을 콘솔에 출력하는 데
필요한 코드를 작성할 수 있도록 future와 
`async`와 `await`를 사용하여 future를 사용하는 법을 배웁니다.

{{site.alert.secondary}}
  **주요 용어:**

  * **동기 작업**: 동기 작업은 해당 작업이 완료되기 전에 다른 작업의 실행을 블락합니다.
  * **동기 함수**: 동기 함수는 동기 작업만을 수행합니다.
  * **비동기 작업**: 비동기 작업은 해당 작업이 완료되기 전에도 다른 작업을 실행할 수 있게 합니다.
  * **비동기 함수**: 비동기 함수는 적어도 한 개 이상의 비동기 작업을 수행하며 _동기_ 작업도 수행이 가능합니다.
{{site.alert.end}}


## future란?

future(소문자 "f")는 [Future][] (대문자 "F") 클래스의 인스턴스입니다.
future는 비동기 작업의 결과를 나타내며 미완료(uncompleted), 완료(completed) 중 한 가지 상태를 가집니다.

{{site.alert.note}}
  _미완료_ 는 future가 값을 생성하기 전의 상태를 나타내는 Dart 용어입니다.
{{site.alert.end}}

### 미완료

비동기 함수를 호출하면, 미완료된 future를 반환합니다.
해당 future는 함수의 비동기 작업이 끝나거나 에러 발생을 기다립니다.

### 완료

비동기 작업이 성공적으로 끝나면,
future는 값으로 완료되고 그렇지 않으면 에러로 완료됩니다.

#### 값으로 완료

`Future<T>` 타입의 future는 `T` 타입의 값으로 완료됩니다.
예를 들어, `Future<String>` 타입의 future는 문자열 값을 생성합니다.
future의 타입이 `Future<void>`이면
사용가능한 값을 생성하지 않습니다.

#### 에러로 완료

어떤 이유로 함수가 수행하는 비동기 작업이 실패하면,
future는 에러로 완료됩니다.

### 예제: future

다음 예제에서 `fetchUserOrder()`는 콘솔에 출력후 완료되는 future를 반환합니다.
`fetchUserOrder()` 함수는 사용 가능한 값을 반환하지 않기 때문에
반환 타입은 `Future<void>`입니다. 예제를 실행하기 전에,
"Large Latte" 또는 "Fetching user order..." 중에 처음 출력될
문자열이 무엇인지 예상해보세요.

<?code-excerpt "async_await/bin/futures_intro.dart"?>
```dart:run-dartpad:height-300px:ga_id-introducting_futures
Future<void> fetchUserOrder() {
  // 이 함수가 서비스 또는 데이터 베이스에서 사용자 정보를 가져오는 함수라고 생각해보세요.
  return Future.delayed(const Duration(seconds: 2), () => print('Large Latte'));
}

void main() {
  fetchUserOrder();
  print('Fetching user order...');
}
```

위의 코드에서 `fetchUserOrder()`는 8번째 라인의 `print()` 호출보다 먼저 실행되지만,
"Fetching user order..."가 `fetchUserOrder()`의 출력인 "Large Latte" 보다
먼저 출력됩니다. 이는 `fetchUserOrder()`가 "Large Latte"를 출력하기 전에
딜레이되기 때문입니다.

### 예제: 에러로 완료

어떻게 future가 에러로 완료되는지 다음 예제를 실행해서 확인하세요.
이후의 섹션에서 이 에러를 다루는 법을 배웁니다.

<?code-excerpt "async_await/bin/futures_intro.dart (error)" replace="/Error//g"?>
```dart:run-dartpad:height-300px:ga_id-completing_with_error
Future<void> fetchUserOrder() {
// 이 함수가 사용자 정보를 가져오던 중에 버그가 발생한다고 생각해보세요.
  return Future.delayed(const Duration(seconds: 2),
      () => throw Exception('Logout failed: user ID is invalid'));
}

void main() {
  fetchUserOrder();
  print('Fetching user order...');
}
```

이 예제에서, `fetchUserOrder()`는 사용자 ID가 무효하다고
알려주는 에러로 완료됩니다.
지금까지 future와 future가 어떻게 완료되는지에 대해 배웠습니다.
그렇다면 비동기 함수의 결과 값을 어떻게 사용해야 할까요?
다음 섹션에서 `async`와 `await` 키워드를 사용하여
결과 값을 얻는 방법에 대해 배워봅시다.

{{site.alert.secondary}}
  **리뷰:**

  * A [Future\<T\>][Future] instance produces a value of type `T`.
  * If a future doesn't produce a usable value, 
    then the future's type is `Future<void>`.
  * A future can be in one of two states: uncompleted or completed.
  * When you call a function that returns a future, 
    the function queues up work to be done and returns an uncompleted future.
  * When a future's operation finishes, 
    the future completes with a value or with an error.

  **Key terms:**

  * **Future**: the Dart [Future][] class.
  * **future**: an instance of the Dart `Future` class.
{{site.alert.end}}

## Working with futures: async and await

The `async` and `await` keywords provide a declarative way
to define asynchronous functions and use their results. 
Remember these two basic guidelines when using `async` and `await`:

* __To define an async function, add `async` before the function body:__
* __The `await` keyword works only in `async` functions.__

Here's an example  that converts `main()` 
from a synchronous to asynchronous function.

First, add the `async` keyword before the function body:

<?code-excerpt "async_await/bin/get_order_sync_bad.dart (main-sig)" replace="/main\(\)/$& async/g; /async/[!$&!]/g; /$/ ··· }/g"?>
{% prettify dart tag=pre+code %}
void main() [!async!] { ··· }
{% endprettify %}

If the function has a declared return type, 
then update the type to be `Future<T>`, 
where `T` is the type of the value that the function returns.
If the function doesn't explicitly return a value,
then the return type is `Future<void>`:

<?code-excerpt "async_await/bin/get_order.dart (main-sig)" replace="/Future<\w+\W/[!$&!]/g;  /$/ ··· }/g"?>
{% prettify dart tag=pre+code %}
[!Future<void>!] main() async { ··· }
{% endprettify %}

Now that you have an `async` function, 
you can use the `await` keyword to wait for a future to complete:

<?code-excerpt "async_await/bin/get_order.dart (print-order)" replace="/await/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
print([!await!] createOrderMessage());
{% endprettify %}

As the following two examples show, the `async` and `await` keywords 
result in asynchronous code that looks a lot like synchronous code.
The only differences are highlighted in the asynchronous example, 
which—if your window is wide enough—is 
to the right of the synchronous example.

<div class="container">
<div class="row">
<div class="col-sm" markdown="1">
#### Example: synchronous functions

<?code-excerpt "async_await/bin/get_order_sync_bad.dart (no-warning)" replace="/(\s+\/\/ )(Imagine.*? is )(.*)/$1$2$1$3/g"?>
```dart
String createOrderMessage() {
  var order = fetchUserOrder();
  return 'Your order is: $order';
}

Future<String> fetchUserOrder() =>
    // Imagine that this function is
    // more complex and slow.
    Future.delayed(
      const Duration(seconds: 2),
      () => 'Large Latte',
    );

void main() {
  print('Fetching user order...');
  print(createOrderMessage());
}
```

{:.console-output}
```nocode
Fetching user order...
Your order is: Instance of '_Future<String>'
```
</div>
<div class="col-sm" markdown="1">
#### Example: asynchronous functions

<?code-excerpt "async_await/bin/get_order.dart" replace="/(\s+\/\/ )(Imagine.*? is )(.*)/$1$2$1$3/g; /async|await/[!$&!]/g; /(Future<\w+\W)( [^f])/[!$1!]$2/g; /4/2/g"?>
{% prettify dart tag=pre+code %}
[!Future<String>!] createOrderMessage() [!async!] {
  var order = [!await!] fetchUserOrder();
  return 'Your order is: $order';
}

Future<String> fetchUserOrder() =>
    // Imagine that this function is
    // more complex and slow.
    Future.delayed(
      const Duration(seconds: 2),
      () => 'Large Latte',
    );

[!Future<void>!] main() [!async!] {
  print('Fetching user order...');
  print([!await!] createOrderMessage());
}
{% endprettify %}

{:.console-output}
```nocode
Fetching user order...
Your order is: Large Latte
```
</div>
</div>
</div>

The asynchronous example is different in three ways:

* The return type for `createOrderMessage()` changes 
  from `String` to `Future<String>`.
* The **`async`** keyword appears before the function bodies for
  `createOrderMessage()` and `main()`.
* The **`await`** keyword appears before calling the asynchronous functions
  `fetchUserOrder()` and `createOrderMessage()`.

{{site.alert.secondary}}
  **Key terms:**

  * **async**: You can use the `async` keyword before a function's body to mark it as
    asynchronous.
  * **async function**:  An `async` function is a function labeled with the `async`
    keyword.
  * **await**: You can use the `await` keyword to get the completed result of an
    asynchronous expression. The `await` keyword only works within an `async` function.
{{site.alert.end}}

### Execution flow with async and await

An `async` function runs synchronously until the first `await` keyword. 
This means that within an `async` function body, 
all synchronous code before the first `await` keyword executes immediately.

### Example: Execution within async functions

Run the following example to see how execution proceeds
within an `async` function body. 
What do you think the output will be?

<?code-excerpt "async_await/bin/async_example.dart" remove="/\/\/ print/"?>
```dart:run-dartpad:height-530px:ga_id-execution_within_async_function
Future<void> printOrderMessage() async {
  print('Awaiting user order...');
  var order = await fetchUserOrder();
  print('Your order is: $order');
}

Future<String> fetchUserOrder() {
  // Imagine that this function is more complex and slow.
  return Future.delayed(const Duration(seconds: 4), () => 'Large Latte');
}

void main() async {
  countSeconds(4);
  await printOrderMessage();
}

// You can ignore this function - it's here to visualize delay time in this example.
void countSeconds(int s) {
  for (var i = 1; i <= s; i++) {
    Future.delayed(Duration(seconds: i), () => print(i));
  }
}
```

After running the code in the preceding example, try reversing lines 2 and 3:

<?code-excerpt "async_await/bin/async_example.dart (swap-stmts)" replace="/\/\/ (print)/$1/g"?>
```dart
var order = await fetchUserOrder();
print('Awaiting user order...');
```

Notice that timing of the output shifts, now that `print('Awaiting user order')`
appears after the first `await` keyword in `printOrderMessage()`.

### Exercise: Practice using async and await

The following exercise is a failing unit test
that contains partially completed code snippets. 
Your task is to complete the exercise by writing code to make the tests pass.
You don't need to implement `main()`.

To simulate asynchronous operations, call the following functions, 
which are provided for you:

| Function           | Type signature                   | Description                                    |
|--------------------|----------------------------------|------------------------------------------------|
| fetchRole()        | `Future<String> fetchRole()`     | Gets a short description of the user's role.   |
| fetchLoginAmount() | `Future<int> fetchLoginAmount()` | Gets the number of times a user has logged in. |
{:.table .table-striped}


#### Part 1: `reportUserRole()`

Add code to the `reportUserRole()` function so that it does the following:
{% comment %}
  Some bulleted items are intentionally lacking punctuation to avoid
  confusing the users about characters in string values
{% endcomment -%}

* Returns a future that completes with the following
  string: `"User role: <user role>"`
  * Note: You must use the actual value returned by `fetchRole()`; 
    copying and pasting the example return value won't make the test pass.
  * Example return value: `"User role: tester"`
* Gets the user role by calling the provided function `fetchRole()`.

####  Part 2: `reportLogins()`

Implement an `async` function `reportLogins()` so that it does the following:

* Returns the string `"Total number of logins: <# of logins>"`.
  * Note: You must use the actual value returned by `fetchLoginAmount()`; 
    copying and pasting the example return value won't make the test pass.
  * Example return value from `reportLogins()`: `"Total number of logins: 57"`
* Gets the number of logins by calling the provided function `fetchLoginAmount()`.

<?code-excerpt "async_await/lib/practice_using/main.dart"?>
```dart:start-dartpad:theme-dark:height-380px:ga_id-practice_using:file-main.dart
// Part 1
// You can call the provided async function fetchRole()
// to return the user role.
Future<String> reportUserRole() async {
  TODO('Your implementation goes here.');
}

// Part 2
// Implement reportLogins here
// You can call the provided async function fetchLoginAmount()
// to return the number of times that the user has logged in.
reportLogins() {}
```

<?code-excerpt "async_await/lib/practice_using/solution.dart"?>
```dart:file-solution.dart
Future<String> reportUserRole() async {
  var username = await fetchRole();
  return 'User role: $username';
}

Future<String> reportLogins() async {
  var logins = await fetchLoginAmount();
  return 'Total number of logins: $logins';
}
```

<?code-excerpt "async_await/lib/practice_using/test.dart"?>
```dart:file-test.dart
const role = 'administrator';
const logins = 42;
const passed = 'PASSED';
const testFailedMessage = 'Test failed for the function:';
const typoMessage = 'Test failed! Check for typos in your return value';
const didNotImplement =
    'Test failed! Did you forget to implement or return from ';
const oneSecond = Duration(seconds: 1);
List<String> messages = [];
Future<String> fetchRole() => Future.delayed(oneSecond, () => role);
Future<int> fetchLoginAmount() => Future.delayed(oneSecond, () => logins);

void main() async {
  try {
    messages
      ..add(makeReadable(
          testLabel: 'Part 1',
          testResult: await asyncEquals(
              expected: 'User role: administrator',
              actual: await reportUserRole(),
              typoKeyword: role),
          readableErrors: {
            typoMessage: typoMessage,
            'null': '$didNotImplement reportUserRole?',
            'User role: Instance of \'Future<String>\'':
                '$testFailedMessage reportUserRole. Did you use the await keyword?',
            'User role: Instance of \'_Future<String>\'':
                '$testFailedMessage reportUserRole. Did you use the await keyword?',
            'User role:':
                '$testFailedMessage reportUserRole. Did you return a user role?',
            'User role: ':
                '$testFailedMessage reportUserRole. Did you return a user role?',
            'User role: tester':
                '$testFailedMessage reportUserRole. Did you invoke fetchRole to fetch the user\'s role?',
          }))
      ..add(makeReadable(
          testLabel: 'Part 2',
          testResult: await asyncEquals(
              expected: 'Total number of logins: 42',
              actual: await reportLogins(),
              typoKeyword: logins.toString()),
          readableErrors: {
            typoMessage: typoMessage,
            'null': '$didNotImplement reportLogins?',
            'Total number of logins: Instance of \'Future<int>\'':
                '$testFailedMessage reportLogins. Did you use the await keyword?',
            'Total number of logins: Instance of \'_Future<int>\'':
                '$testFailedMessage reportLogins. Did you use the await keyword?',
            'Total number of logins: ':
                '$testFailedMessage reportLogins. Did you return the number of logins?',
            'Total number of logins:':
                '$testFailedMessage reportLogins. Did you return the number of logins?',
            'Total number of logins: 57':
                '$testFailedMessage reportLogins. Did you invoke fetchLoginAmount to fetch the number of user logins?',
          }))
      ..removeWhere((m) => m.contains(passed))
      ..toList();

    if (messages.isEmpty) {
      _result(true);
    } else {
      _result(false, messages);
    }
  } on UnimplementedError {
    _result(false, [
      '$didNotImplement reportUserRole?',
    ]);
  } catch (e) {
    _result(false, ['Tried to run solution, but received an exception: $e']);
  }
}

////////////////////////////////////////
///////////// Test Helpers /////////////
////////////////////////////////////////
String makeReadable({
  required String testResult,
  required Map<String, String> readableErrors,
  required String testLabel,
}) {
  if (readableErrors.containsKey(testResult)) {
    var readable = readableErrors[testResult];
    return '$testLabel $readable';
  } else {
    return '$testLabel $testResult';
  }
}

///////////////////////////////////////
//////////// Assertions ///////////////
///////////////////////////////////////
Future<String> asyncEquals({
  required String expected,
  required dynamic actual,
  required String typoKeyword,
}) async {
  var strActual = actual is String ? actual : actual.toString();
  try {
    if (expected == actual) {
      return passed;
    } else if (strActual.contains(typoKeyword)) {
      return typoMessage;
    } else {
      return strActual;
    }
  } catch (e) {
    return e.toString();
  }
}
```

```text:end-dartpad:file-hint.txt
Did you remember to add the async keyword to the reportUserRole() function?

Did you remember to use the await keyword before invoking fetchRole()?

Remember: reportUserRole() needs to return a future!
```

{{site.alert.note}}
  If your code passes the tests, you can ignore
  [info-level messages.](/guides/language/analysis-options#customizing-analysis-rules)
{{site.alert.end}}

## Handling errors

To handle errors in an `async` function, use try-catch:

<?code-excerpt "async_await/bin/try_catch.dart (try-catch)" remove="print(order)"?>
```dart
try {
  print('Awaiting user order...');
  var order = await fetchUserOrder();
} catch (err) {
  print('Caught error: $err');
}
```

Within an `async` function, you can write 
[try-catch clauses](/guides/language/language-tour#catch)
the same way you would in synchronous code.

### Example: async and await with try-catch

Run the following example to see how to handle an error
from an asynchronous function. 
What do you think the output will be?

<?code-excerpt "async_await/bin/try_catch.dart"?>
```dart:run-dartpad:height-530px:ga_id-try_catch
Future<void> printOrderMessage() async {
  try {
    print('Awaiting user order...');
    var order = await fetchUserOrder();
    print(order);
  } catch (err) {
    print('Caught error: $err');
  }
}

Future<String> fetchUserOrder() {
  // Imagine that this function is more complex.
  var str = Future.delayed(
      const Duration(seconds: 4),
      () => throw 'Cannot locate user order');
  return str;
}

void main() async {
  await printOrderMessage();
}
```

### Exercise: Practice handling errors

The following exercise provides practice handling errors with asynchronous code,
using the approach described in the previous section. To simulate asynchronous
operations, your code will call the following function, which is provided for you:


| Function           | Type signature                      | Description                                                      |
|--------------------|-------------------------------------|------------------------------------------------------------------|
| fetchNewUsername() | `Future<String> fetchNewUsername()` | Returns the new username that you can use to replace an old one. |
{:.table .table-striped}

Use `async` and `await` to implement an asynchronous `changeUsername()` function
that does the following:

* Calls the provided asynchronous function `fetchNewUsername()` 
  and returns its result.
  * Example return value from `changeUsername()`: `"jane_smith_92"`
* Catches any error that occurs and returns the string value of the error.
  * You can use the
    [toString()]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/ArgumentError/toString.html)
    method to stringify both
    [Exceptions]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Exception-class.html) 
    and
    [Errors.]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Error-class.html)

<?code-excerpt "async_await/lib/practice_errors/main.dart"?>
```dart:start-dartpad:theme-dark:height-380px:ga_id-practice_errors:file-main.dart
// Implement changeUsername here
changeUsername() {}
```

<?code-excerpt "async_await/lib/practice_errors/solution.dart"?>
```dart:file-solution.dart
Future<String> changeUsername() async {
  try {
    return await fetchNewUsername();
  } catch (err) {
    return err.toString();
  }
}
```

<?code-excerpt "async_await/lib/practice_errors/test.dart"?>
```dart:file-test.dart
List<String> messages = [];
bool logoutSucceeds = false;
const passed = 'PASSED';
const noCatch = 'NO_CATCH';
const typoMessage = 'Test failed! Check for typos in your return value';
const oneSecond = Duration(seconds: 1);

class UserError implements Exception {
  String errMsg() => 'New username is invalid';
}

Future<String> fetchNewUsername() {
  var str = Future.delayed(oneSecond, () => throw UserError());
  return str;
}

void main() async {
  try {
    messages
      ..add(makeReadable(
          testLabel: '',
          testResult: await asyncDidCatchException(changeUsername),
          readableErrors: {
            typoMessage: typoMessage,
            noCatch:
                'Did you remember to call fetchNewUsername within a try/catch block?',
          }))
      ..add(makeReadable(
          testLabel: '',
          testResult: await asyncErrorEquals(changeUsername),
          readableErrors: {
            typoMessage: typoMessage,
            noCatch:
                'Did you remember to call fetchNewUsername within a try/catch block?',
          }))
      ..removeWhere((m) => m.contains(passed))
      ..toList();

    if (messages.isEmpty) {
      _result(true);
    } else {
      _result(false, messages);
    }
  } catch (e) {
    _result(false, ['Tried to run solution, but received an exception: $e']);
  }
}

////////////////////////////////////////
///////////// Test Helpers /////////////
////////////////////////////////////////
String makeReadable({
  required String testResult,
  required Map<String, String> readableErrors,
  required String testLabel,
}) {
  if (readableErrors.containsKey(testResult)) {
    var readable = readableErrors[testResult];
    return '$testLabel $readable';
  } else {
    return '$testLabel $testResult';
  }
}

void passIfNoMessages(List<String> messages, Map<String, String> readable) {
  if (messages.isEmpty) {
    _result(true);
  } else {
    final userMessages = messages
        .where((message) => readable.containsKey(message))
        .map((message) => readable[message]!)
        .toList();
    print(messages);

    _result(false, userMessages);
  }
}

///////////////////////////////////////
//////////// Assertions ///////////////
///////////////////////////////////////
Future<String> asyncErrorEquals(Function fn) async {
  var result = await fn();
  if (result == UserError().toString()) {
    return passed;
  } else {
    return 'Test failed! Did you stringify and return the caught error?';
  }
}

Future<String> asyncDidCatchException(Function fn) async {
  var caught = true;
  try {
    await fn();
  } on UserError catch (_) {
    caught = false;
  }

  if (caught == false) {
    return noCatch;
  } else {
    return passed;
  }
}
```

```text:end-dartpad:file-hint.txt
Implement changeUsername() to return the string from fetchNewUsername() or
(if that fails) the string value of any error that occurs.
You'll need a try-catch statement to catch and handle errors.
```

{% comment %}
TODO: Consider summary section before final exercise
TODO: Consider adding new content to final section (not repeating same stuff)
{% endcomment -%}

## Exercise: Putting it all together

It's time to practice what you've learned in one final exercise.
To simulate asynchronous operations, this exercise provides the asynchronous
functions `fetchUsername()` and `logoutUser()`:

| Function        | Type signature                   | Description                                                                   |
|-----------------|----------------------------------|-------------------------------------------------------------------------------|
| fetchUsername() | `Future<String> fetchUsername()` | Returns the name associated with the current user.                            |
| logoutUser()    | `Future<String> logoutUser()`    | Performs logout of current user and returns the username that was logged out. |
{:.table .table-striped}

Write the following:

####  Part 1: `addHello()`

* Write a function `addHello()` that takes a single `String` argument.
* `addHello()` returns its `String` argument preceded by `'Hello '`.<br>
  Example: `addHello('Jon')` returns `'Hello Jon'`.

####  Part 2: `greetUser()`

* Write a function `greetUser()` that takes no arguments.
* To get the username, `greetUser()` calls the provided asynchronous
  function `fetchUsername()`.
* `greetUser()` creates a greeting for the user by calling `addHello()`,
  passing it the username, and returning the result.<br>
  Example: If `fetchUsername()` returns `'Jenny'`, then
  `greetUser()` returns `'Hello Jenny'`.

####  Part 3: `sayGoodbye()`

* Write a function `sayGoodbye()` that does the following:
  * Takes no arguments.
  * Catches any errors.
  * Calls the provided asynchronous function `logoutUser()`.
* If `logoutUser()` fails, `sayGoodbye()` returns any string you like.
* If `logoutUser()` succeeds, `sayGoodbye()` returns the string
  `'<result> Thanks, see you next time'`, where `<result>` is
  the string value returned by calling `logoutUser()`.

<?code-excerpt "async_await/lib/putting_together/main.dart"?>
```dart:start-dartpad:theme-dark:height-380px:ga_id-putting_it_all_together:file-main.dart
// Part 1
addHello(String user) {}

// Part 2
// You can call the provided async function fetchUsername()
// to return the username.
greetUser() {}

// Part 3
// You can call the provided async function logoutUser()
// to log out the user.
sayGoodbye() {}
```

<?code-excerpt "async_await/lib/putting_together/solution.dart"?>
```dart:file-solution.dart
String addHello(String user) => 'Hello $user';

Future<String> greetUser() async {
  var username = await fetchUsername();
  return addHello(username);
}

Future<String> sayGoodbye() async {
  try {
    var result = await logoutUser();
    return '$result Thanks, see you next time';
  } catch (e) {
    return 'Failed to logout user: $e';
  }
}
```

<?code-excerpt "async_await/lib/putting_together/test.dart"?>
```dart:file-test.dart
List<String> messages = [];
bool logoutSucceeds = false;
const passed = 'PASSED';
const noCatch = 'NO_CATCH';
const typoMessage = 'Test failed! Check for typos in your return value';
const didNotImplement =
    'Test failed! Did you forget to implement or return from ';
const oneSecond = Duration(seconds: 1);

Future<String> fetchUsername() => Future.delayed(oneSecond, () => 'Jean');
String failOnce() {
  if (logoutSucceeds) {
    return 'Success!';
  } else {
    logoutSucceeds = true;
    throw Exception('Logout failed');
  }
}

Future<String> logoutUser() => Future.delayed(oneSecond, failOnce);

void main() async {
  try {
    messages
      ..add(makeReadable(
          testLabel: 'Part 1',
          testResult: await asyncEquals(
              expected: 'Hello Jerry',
              actual: addHello('Jerry'),
              typoKeyword: 'Jerry'),
          readableErrors: {
            typoMessage: typoMessage,
            'null': '$didNotImplement addHello?',
            'Hello Instance of \'Future<String>\'':
                'Looks like you forgot to use the \'await\' keyword!',
            'Hello Instance of \'_Future<String>\'':
                'Looks like you forgot to use the \'await\' keyword!',
          }))
      ..add(makeReadable(
          testLabel: 'Part 2',
          testResult: await asyncEquals(
              expected: 'Hello Jean',
              actual: await greetUser(),
              typoKeyword: 'Jean'),
          readableErrors: {
            typoMessage: typoMessage,
            'null': '$didNotImplement greetUser?',
            'HelloJean':
                'Looks like you forgot the space between \'Hello\' and \'Jean\'',
            'Hello Instance of \'Future<String>\'':
                'Looks like you forgot to use the \'await\' keyword!',
            'Hello Instance of \'_Future<String>\'':
                'Looks like you forgot to use the \'await\' keyword!',
            '{Closure: (String) => dynamic from Function \'addHello\': static.(await fetchUsername())}':
                'Did you place the \'\$\' character correctly?',
            '{Closure \'addHello\'(await fetchUsername())}':
                'Did you place the \'\$\' character correctly?',
          }))
      ..add(makeReadable(
          testLabel: 'Part 3',
          testResult: await asyncDidCatchException(sayGoodbye),
          readableErrors: {
            typoMessage:
                '$typoMessage. Did you add the text \'Thanks, see you next time\'?',
            'null': '$didNotImplement sayGoodbye?',
            noCatch:
                'Did you remember to call logoutUser within a try/catch block?',
            'Instance of \'Future<String>\' Thanks, see you next time':
                'Did you remember to use the \'await\' keyword in the sayGoodbye function?',
            'Instance of \'_Future<String>\' Thanks, see you next time':
                'Did you remember to use the \'await\' keyword in the sayGoodbye function?',
          }))
      ..add(makeReadable(
          testLabel: 'Part 3',
          testResult: await asyncEquals(
              expected: 'Success! Thanks, see you next time',
              actual: await sayGoodbye(),
              typoKeyword: 'Success'),
          readableErrors: {
            typoMessage:
                '$typoMessage. Did you add the text \'Thanks, see you next time\'?',
            'null': '$didNotImplement sayGoodbye?',
            noCatch:
                'Did you remember to call logoutUser within a try/catch block?',
            'Instance of \'Future<String>\' Thanks, see you next time':
                'Did you remember to use the \'await\' keyword in the sayGoodbye function?',
            'Instance of \'_Future<String>\' Thanks, see you next time':
                'Did you remember to use the \'await\' keyword in the sayGoodbye function?',
            'Instance of \'_Exception\'':
                'CAUGHT Did you remember to return a string?',
          }))
      ..removeWhere((m) => m.contains(passed))
      ..toList();

    if (messages.isEmpty) {
      _result(true);
    } else {
      _result(false, messages);
    }
  } catch (e) {
    _result(false, ['Tried to run solution, but received an exception: $e']);
  }
}

////////////////////////////////////////
///////////// Test Helpers /////////////
////////////////////////////////////////
String makeReadable({
  required String testResult,
  required Map<String, String> readableErrors,
  required String testLabel,
}) {
  String? readable;
  if (readableErrors.containsKey(testResult)) {
    readable = readableErrors[testResult];
    return '$testLabel $readable';
  } else if ((testResult != passed) && (testResult.length < 18)) {
    readable = typoMessage;
    return '$testLabel $readable';
  } else {
    return '$testLabel $testResult';
  }
}

void passIfNoMessages(List<String> messages, Map<String, String> readable) {
  if (messages.isEmpty) {
    _result(true);
  } else {
    final userMessages = messages
        .where((message) => readable.containsKey(message))
        .map((message) => readable[message]!)
        .toList();
    print(messages);

    _result(false, userMessages);
  }
}

///////////////////////////////////////
//////////// Assertions ///////////////
///////////////////////////////////////
Future<String> asyncEquals({
  required String expected,
  required dynamic actual,
  required String typoKeyword,
}) async {
  var strActual = actual is String ? actual : actual.toString();
  try {
    if (expected == actual) {
      return passed;
    } else if (strActual.contains(typoKeyword)) {
      return typoMessage;
    } else {
      return strActual;
    }
  } catch (e) {
    return e.toString();
  }
}

Future<String> asyncDidCatchException(Function fn) async {
  var caught = true;
  try {
    await fn();
  } on Exception catch (_) {
    caught = false;
  }

  if (caught == true) {
    return passed;
  } else {
    return noCatch;
  }
}
```

```text:end-dartpad:file-hint.txt
The greetUser() and sayGoodbye() functions are asynchronous;
addHello() isn't.
```

## What's next?

Congratulations, you've finished the codelab! If you'd like to learn more, here
are some suggestions for where to go next:

- Play with [DartPad.]({{site.dartpad}})
- Try another [codelab](/codelabs).
- Learn more about futures and asynchrony:
  - [Streams tutorial](/tutorials/language/streams):
    Learn how to work with a sequence of asynchronous events.
  - [Concurrency in Dart](/guides/language/concurrency)
    Understand and learn how to implement concurrency in Dart.
  - [Dart videos from Google:][Dart videos]
    Watch one or more of the videos about asynchronous coding.
    Or, if you prefer, read the articles that are based on these videos.
    (Start with [isolates and event loops.][article])
- [Get the Dart SDK.](/get-dart)

If you're interested in using embedded DartPads, like this codelab does, see
[best practices for using DartPad in tutorials][].

[best practices for using DartPad in tutorials]: /resources/dartpad-best-practices
[Dart videos]: https://www.youtube.com/playlist?list=PLjxrf2q8roU0Net_g1NT5_vOO3s_FR02J
[article]: https://medium.com/dartlang/dart-asynchronous-programming-isolates-and-event-loops-bffc3e296a6a
[Future]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-async/Future-class.html
[style guide]: /guides/language/effective-dart/style
[documentation guide]: /guides/language/effective-dart/documentation
[usage guide]: /guides/language/effective-dart/usage
[design guide]: /guides/language/effective-dart/design
