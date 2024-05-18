---
title: "Asynchronous programming: futures, async, await"
description: Learn about and practice writing asynchronous code in DartPad!
js: [{url: '/assets/js/inject_dartpad.js', defer: true}]
---
<?code-excerpt replace="/ *\/\/\s+ignore_for_file:[^\n]+\n//g; /(^|\n) *\/\/\s+ignore:[^\n]+\n/$1/g; /(\n[^\n]+) *\/\/\s+ignore:[^\n]+\n/$1\n/g"?>
<?code-excerpt plaster="none"?>

이 코드랩에서는 future와 `async`, `await` 키워드를 사용하여
비동기 코드 작성법을 배웁니다. 임베디드 DartPad 에디터로
예제 코드를 실행하고 연습하며 배운 것을 테스트할 수 있습니다.

이 코드랩을 최대한 활용하려면 다음을 알고 있어야 합니다:

* [기초 Dart 문법](/language)에 대한 지식.
* 다른 언어에서 비동기 코드를 작성해본 경험.

이 코드랩에서는 다음의 주제를 다룹니디:

* `async`와 `await` 키워드를 언제 어떻게 사용해야 하는지.
* `async`와 `await`의 사용이 실행 순서에 어떤 영향을 미치는지.
* `try-catch`을 사용하여 어떻게 `async` 함수에
  사용된 비동기 호출에서 발생하는 에러를 다루는지.

40-60분이면 이 코드랩을 완료할 수 있습니다.

:::note
This page uses embedded DartPads to display examples and exercises.
{% render 'dartpads-embedded-troubleshooting.md' %}
:::

The exercises in this codelab have partially completed code snippets.
You can use DartPad to test your knowledge by completing the code and
clicking the **Run** button.
**Don't edit the test code in the `main` function or below**.

If you need help, expand the **Hint** or **Solution** dropdown
after each exercise.

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

:::secondary Key terms
* **synchronous operation**: A synchronous operation blocks other operations
  from executing until it completes.
* **synchronous function**: A synchronous function only performs synchronous
  operations.
* **asynchronous operation**: Once initiated, an asynchronous operation allows
  other operations to execute before it completes.
* **asynchronous function**: An asynchronous function performs at least one
  asynchronous operation and can also perform _synchronous_ operations.
:::


## future란?

future(소문자 "f")는 [Future][] (대문자 "F") 클래스의 인스턴스입니다.
future는 비동기 작업의 결과를 나타내며 미완료(uncompleted), 완료(completed) 중 한 가지 상태를 가집니다.

:::note
_Uncompleted_ is a Dart term referring to the state of a future
before it has produced a value.
:::

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

<?code-excerpt "async_await/bin/futures_intro.dart (no-error)"?>
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
  // Imagine that this function is fetching user info but encounters a bug.
  return Future.delayed(
    const Duration(seconds: 2),
    () => throw Exception('Logout failed: user ID is invalid'),
  );
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

:::secondary Quick review
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
:::

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
```dart
void main() [!async!] { ··· }
```

If the function has a declared return type, 
then update the type to be `Future<T>`, 
where `T` is the type of the value that the function returns.
If the function doesn't explicitly return a value,
then the return type is `Future<void>`:

<?code-excerpt "async_await/bin/get_order.dart (main-sig)" replace="/Future<\w+\W/[!$&!]/g;  /$/ ··· }/g"?>
```dart
[!Future<void>!] main() async { ··· }
```

Now that you have an `async` function, 
you can use the `await` keyword to wait for a future to complete:

<?code-excerpt "async_await/bin/get_order.dart (print-order)" replace="/await/[!$&!]/g"?>
```dart
print([!await!] createOrderMessage());
```

As the following two examples show, the `async` and `await` keywords 
result in asynchronous code that looks a lot like synchronous code.
The only differences are highlighted in the asynchronous example, 
which—if your window is wide enough—is 
to the right of the synchronous example.

<div class="container">
<div class="row">
<div class="col-sm">

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

```plaintext
Fetching user order...
Your order is: Instance of '_Future<String>'
```

</div>
<div class="col-sm">

#### Example: asynchronous functions

<?code-excerpt "async_await/bin/get_order.dart" replace="/(\s+\/\/ )(Imagine.*? is )(.*)/$1$2$1$3/g; /async|await/[!$&!]/g; /(Future<\w+\W)( [^f])/[!$1!]$2/g; /4/2/g"?>
```dart
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
```

```plaintext
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

:::secondary Key terms
* **async**: You can use the `async` keyword before a function's body to mark it as
  asynchronous.
* **async function**:  An `async` function is a function labeled with the `async`
  keyword.
* **await**: You can use the `await` keyword to get the completed result of an
  asynchronous expression. The `await` keyword only works within an `async` function.
:::

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

<div class="table-wrapper">

| Function           | Type signature                   | Description                                    |
|--------------------|----------------------------------|------------------------------------------------|
| fetchRole()        | `Future<String> fetchRole()`     | Gets a short description of the user's role.   |
| fetchLoginAmount() | `Future<int> fetchLoginAmount()` | Gets the number of times a user has logged in. |

{:.table .table-striped}
</div>


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

```dart:run-dartpad:theme-dark:height-380px:ga_id-practice_using
// Part 1
// Call the provided async function fetchRole()
// to return the user role.
Future<String> reportUserRole() async {
  // TODO: Implement the reportUserRole function here.
}

// Part 2
// TODO: Implement the reportUserRole function here.
// Call the provided async function fetchLoginAmount()
// to return the number of times that the user has logged in.
reportLogins() {}

// The following functions those provided to you to simulate
// asynchronous operations that could take a while.

Future<String> fetchRole() => Future.delayed(_halfSecond, () => _role);
Future<int> fetchLoginAmount() => Future.delayed(_halfSecond, () => _logins);

// The following code is used to test and provide feedback on your solution.
// There is no need to read or modify it.

void main() async {
  print('Testing...');
  List<String> messages = [];
  const passed = 'PASSED';
  const testFailedMessage = 'Test failed for the function:';
  const typoMessage = 'Test failed! Check for typos in your return value';
  try {
    messages
      ..add(_makeReadable(
          testLabel: 'Part 1',
          testResult: await _asyncEquals(
            expected: 'User role: administrator',
            actual: await reportUserRole(),
            typoKeyword: _role,
          ),
          readableErrors: {
            typoMessage: typoMessage,
            'null':
                'Test failed! Did you forget to implement or return from reportUserRole?',
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
      ..add(_makeReadable(
          testLabel: 'Part 2',
          testResult: await _asyncEquals(
            expected: 'Total number of logins: 42',
            actual: await reportLogins(),
            typoKeyword: _logins.toString(),
          ),
          readableErrors: {
            typoMessage: typoMessage,
            'null':
                'Test failed! Did you forget to implement or return from reportLogins?',
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
      print('Success. All tests passed!');
    } else {
      messages.forEach(print);
    }
  } on UnimplementedError {
    print(
        'Test failed! Did you forget to implement or return from reportUserRole?');
  } catch (e) {
    print('Tried to run solution, but received an exception: $e');
  }
}

const _role = 'administrator';
const _logins = 42;
const _halfSecond = Duration(milliseconds: 500);

// Test helpers.
String _makeReadable({
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

// Assertions used in tests.
Future<String> _asyncEquals({
  required String expected,
  required dynamic actual,
  required String typoKeyword,
}) async {
  var strActual = actual is String ? actual : actual.toString();
  try {
    if (expected == actual) {
      return 'PASSED';
    } else if (strActual.contains(typoKeyword)) {
      return 'Test failed! Check for typos in your return value';
    } else {
      return strActual;
    }
  } catch (e) {
    return e.toString();
  }
}
```

<details>
  <summary title="Expand for a hint on the async-await exercise.">Hint</summary>

  Did you remember to add the `async` keyword to the `reportUserRole` function?
  
  Did you remember to use the `await` keyword before invoking `fetchRole()`?
  
  Remember: `reportUserRole` needs to return a `Future`.

</details>

<details>
  <summary title="Expand for the solution of the async-await exercise.">Solution</summary>

  ```dart
  Future<String> reportUserRole() async {
    final username = await fetchRole();
    return 'User role: $username';
  }
  
  Future<String> reportLogins() async {
    final logins = await fetchLoginAmount();
    return 'Total number of logins: $logins';
  }
  ```

</details>

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
[try-catch clauses](/language/error-handling#catch)
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

<div class="table-wrapper">

| Function           | Type signature                      | Description                                                      |
|--------------------|-------------------------------------|------------------------------------------------------------------|
| fetchNewUsername() | `Future<String> fetchNewUsername()` | Returns the new username that you can use to replace an old one. |

{:.table .table-striped}
</div>

Use `async` and `await` to implement an asynchronous `changeUsername()` function
that does the following:

* Calls the provided asynchronous function `fetchNewUsername()` 
  and returns its result.
  * Example return value from `changeUsername()`: `"jane_smith_92"`
* Catches any error that occurs and returns the string value of the error.
  * You can use the
    [toString()]({{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/ArgumentError/toString.html)
    method to stringify both
    [Exceptions]({{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Exception-class.html) 
    and
    [Errors.]({{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Error-class.html)

```dart:run-dartpad:theme-dark:height-380px:ga_id-practice_errors
// TODO: Implement changeUsername here.
changeUsername() {}

// The following function is provided to you to simulate
// an asynchronous operation that could take a while and
// potentially throw an exception.

Future<String> fetchNewUsername() =>
    Future.delayed(const Duration(milliseconds: 500), () => throw UserError());

class UserError implements Exception {
  @override
  String toString() => 'New username is invalid';
}

// The following code is used to test and provide feedback on your solution.
// There is no need to read or modify it.

void main() async {
  final List<String> messages = [];
  const typoMessage = 'Test failed! Check for typos in your return value';

  print('Testing...');
  try {
    messages
      ..add(_makeReadable(
          testLabel: '',
          testResult: await _asyncDidCatchException(changeUsername),
          readableErrors: {
            typoMessage: typoMessage,
            _noCatch:
                'Did you remember to call fetchNewUsername within a try/catch block?',
          }))
      ..add(_makeReadable(
          testLabel: '',
          testResult: await _asyncErrorEquals(changeUsername),
          readableErrors: {
            typoMessage: typoMessage,
            _noCatch:
                'Did you remember to call fetchNewUsername within a try/catch block?',
          }))
      ..removeWhere((m) => m.contains(_passed))
      ..toList();

    if (messages.isEmpty) {
      print('Success. All tests passed!');
    } else {
      messages.forEach(print);
    }
  } catch (e) {
    print('Tried to run solution, but received an exception: $e');
  }
}

// Test helpers.
String _makeReadable({
  required String testResult,
  required Map<String, String> readableErrors,
  required String testLabel,
}) {
  if (readableErrors.containsKey(testResult)) {
    final readable = readableErrors[testResult];
    return '$testLabel $readable';
  } else {
    return '$testLabel $testResult';
  }
}

Future<String> _asyncErrorEquals(Function fn) async {
  final result = await fn();
  if (result == UserError().toString()) {
    return _passed;
  } else {
    return 'Test failed! Did you stringify and return the caught error?';
  }
}

Future<String> _asyncDidCatchException(Function fn) async {
  var caught = true;
  try {
    await fn();
  } on UserError catch (_) {
    caught = false;
  }

  if (caught == false) {
    return _noCatch;
  } else {
    return _passed;
  }
}

const _passed = 'PASSED';
const _noCatch = 'NO_CATCH';
```

<details>
  <summary title="Expand for a hint on the error-handling exercise.">Hint</summary>

  Implement `changeUsername` to return the string from `fetchNewUsername` or,
  if that fails, the string value of any error that occurs.
  
  Remember: You can use a [try-catch statement](/language/error-handling#catch)
  to catch and handle errors.

</details>

<details>
  <summary title="Expand for the solution of the error-handling exercise.">Solution</summary>

  ```dart
  Future<String> changeUsername() async {
    try {
      return await fetchNewUsername();
    } catch (err) {
      return err.toString();
    }
  }
  ```

</details>

## Exercise: Putting it all together

It's time to practice what you've learned in one final exercise.
To simulate asynchronous operations, this exercise provides the asynchronous
functions `fetchUsername()` and `logoutUser()`:

<div class="table-wrapper">

| Function        | Type signature                   | Description                                                                   |
|-----------------|----------------------------------|-------------------------------------------------------------------------------|
| fetchUsername() | `Future<String> fetchUsername()` | Returns the name associated with the current user.                            |
| logoutUser()    | `Future<String> logoutUser()`    | Performs logout of current user and returns the username that was logged out. |

{:.table .table-striped}
</div>

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

```dart:run-dartpad:theme-dark:height-380px:ga_id-putting_it_all_together
// Part 1
addHello(String user) {}

// Part 2
// Call the provided async function fetchUsername()
// to return the username.
greetUser() {}

// Part 3
// Call the provided async function logoutUser()
// to log out the user.
sayGoodbye() {}

// The following functions are provided to you to use in your solutions.

Future<String> fetchUsername() => Future.delayed(_halfSecond, () => 'Jean');

Future<String> logoutUser() => Future.delayed(_halfSecond, _failOnce);

// The following code is used to test and provide feedback on your solution.
// There is no need to read or modify it.

void main() async {
  const didNotImplement =
      'Test failed! Did you forget to implement or return from';

  final List<String> messages = [];

  print('Testing...');
  try {
    messages
      ..add(_makeReadable(
          testLabel: 'Part 1',
          testResult: await _asyncEquals(
              expected: 'Hello Jerry',
              actual: addHello('Jerry'),
              typoKeyword: 'Jerry'),
          readableErrors: {
            _typoMessage: _typoMessage,
            'null': '$didNotImplement addHello?',
            'Hello Instance of \'Future<String>\'':
                'Looks like you forgot to use the \'await\' keyword!',
            'Hello Instance of \'_Future<String>\'':
                'Looks like you forgot to use the \'await\' keyword!',
          }))
      ..add(_makeReadable(
          testLabel: 'Part 2',
          testResult: await _asyncEquals(
              expected: 'Hello Jean',
              actual: await greetUser(),
              typoKeyword: 'Jean'),
          readableErrors: {
            _typoMessage: _typoMessage,
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
      ..add(_makeReadable(
          testLabel: 'Part 3',
          testResult: await _asyncDidCatchException(sayGoodbye),
          readableErrors: {
            _typoMessage:
                '$_typoMessage. Did you add the text \'Thanks, see you next time\'?',
            'null': '$didNotImplement sayGoodbye?',
            _noCatch:
                'Did you remember to call logoutUser within a try/catch block?',
            'Instance of \'Future<String>\' Thanks, see you next time':
                'Did you remember to use the \'await\' keyword in the sayGoodbye function?',
            'Instance of \'_Future<String>\' Thanks, see you next time':
                'Did you remember to use the \'await\' keyword in the sayGoodbye function?',
          }))
      ..add(_makeReadable(
          testLabel: 'Part 3',
          testResult: await _asyncEquals(
              expected: 'Success! Thanks, see you next time',
              actual: await sayGoodbye(),
              typoKeyword: 'Success'),
          readableErrors: {
            _typoMessage:
                '$_typoMessage. Did you add the text \'Thanks, see you next time\'?',
            'null': '$didNotImplement sayGoodbye?',
            _noCatch:
                'Did you remember to call logoutUser within a try/catch block?',
            'Instance of \'Future<String>\' Thanks, see you next time':
                'Did you remember to use the \'await\' keyword in the sayGoodbye function?',
            'Instance of \'_Future<String>\' Thanks, see you next time':
                'Did you remember to use the \'await\' keyword in the sayGoodbye function?',
            'Instance of \'_Exception\'':
                'CAUGHT Did you remember to return a string?',
          }))
      ..removeWhere((m) => m.contains(_passed))
      ..toList();

    if (messages.isEmpty) {
      print('Success. All tests passed!');
    } else {
      messages.forEach(print);
    }
  } catch (e) {
    print('Tried to run solution, but received an exception: $e');
  }
}

// Test helpers.
String _makeReadable({
  required String testResult,
  required Map<String, String> readableErrors,
  required String testLabel,
}) {
  String? readable;
  if (readableErrors.containsKey(testResult)) {
    readable = readableErrors[testResult];
    return '$testLabel $readable';
  } else if ((testResult != _passed) && (testResult.length < 18)) {
    readable = _typoMessage;
    return '$testLabel $readable';
  } else {
    return '$testLabel $testResult';
  }
}

Future<String> _asyncEquals({
  required String expected,
  required dynamic actual,
  required String typoKeyword,
}) async {
  final strActual = actual is String ? actual : actual.toString();
  try {
    if (expected == actual) {
      return _passed;
    } else if (strActual.contains(typoKeyword)) {
      return _typoMessage;
    } else {
      return strActual;
    }
  } catch (e) {
    return e.toString();
  }
}

Future<String> _asyncDidCatchException(Function fn) async {
  var caught = true;
  try {
    await fn();
  } on Exception catch (_) {
    caught = false;
  }

  if (caught == true) {
    return _passed;
  } else {
    return _noCatch;
  }
}

const _typoMessage = 'Test failed! Check for typos in your return value';
const _passed = 'PASSED';
const _noCatch = 'NO_CATCH';
const _halfSecond = Duration(milliseconds: 500);

String _failOnce() {
  if (_logoutSucceeds) {
    return 'Success!';
  } else {
    _logoutSucceeds = true;
    throw Exception('Logout failed');
  }
}

bool _logoutSucceeds = false;
```

<details>
  <summary title="Expand for a hint on the 'Putting it all together' exercise.">Hint</summary>

  The `greetUser` and `sayGoodbye` functions should be asynchronous,
  while `addHello` should be a normal, synchronous function.

  Remember: You can use a [try-catch statement](/language/error-handling#catch)
  to catch and handle errors.

</details>

<details>
  <summary title="Expand for the solution of the 'Putting it all together' exercise.">Solution</summary>

  ```dart
  String addHello(String user) => 'Hello $user';
  
  Future<String> greetUser() async {
    final username = await fetchUsername();
    return addHello(username);
  }
  
  Future<String> sayGoodbye() async {
    try {
      final result = await logoutUser();
      return '$result Thanks, see you next time';
    } catch (e) {
      return 'Failed to logout user: $e';
    }
  }
  ```

</details>

## What's next?

Congratulations, you've finished the codelab! If you'd like to learn more, here
are some suggestions for where to go next:

- Play with [DartPad]({{site.dartpad}}).
- Try another [codelab](/codelabs).
- Learn more about futures and asynchronous code in Dart:
  - [Streams tutorial](/tutorials/language/streams):
    Learn how to work with a sequence of asynchronous events.
  - [Concurrency in Dart](/language/concurrency):
    Understand and learn how to implement concurrency in Dart.
  - [Asynchrony support](/language/async):
    Dive in to Dart's language and library support for asynchronous coding.
  - [Dart videos from Google][Dart videos]:
    Watch one or more of the videos about asynchronous coding.
- Get the [Dart SDK](/get-dart)!

[Dart videos]: {{yt.playlist}}PLjxrf2q8roU0Net_g1NT5_vOO3s_FR02J
[Future]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-async/Future-class.html
[style guide]: /effective-dart/style
[documentation guide]: /effective-dart/documentation
[usage guide]: /effective-dart/usage
[design guide]: /effective-dart/design
