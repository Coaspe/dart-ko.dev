---
title: Dart의 숫자
description: Dart의 숫자가 웹에서 어떻게 다른지, 그 차이가 언제 중요한지, 그리고 코드를 조정하는 방법에 대해 알아봅니다.
---

Dart 앱들은 대게 다수의 플랫폼을 타겟으로 합니다.
예를 들어, Flutter 앱은 iOS, Android 그리고 웹을 타겟으로 합니다.
앱이 플랫폼별 라이브러리를 사용하거나, 플랫폼에 의존하는 방식으로
숫자를 사용하지 않는 한 동일한 코드를 사용할 수 있습니다.

이번 페이지에서 네이티브와 웹의 숫자 구현 간의 차이점과
코드 작성 방법에 대해 자세히 설명합니다.

{{site.alert.secondary}}
  **다른 언어와 Dart의 숫자 구현**

  Dart는 성능, 코드 크기, 그리고 플랫폼 상호운용성을 위해
  숫자를 위한 플랫폼별 표현과 시멘틱을 항상 허용합니다.

  마찬가지로 C/C++에서 정수 값을 나타내는 `int` 타입은 네이티브 머신 아키텍쳐 (16-, 32-, 64-bit)에
  가장 잘 매핑되도록 플랫폼에 특화되어 있습니다.
  Java에서 분수 값을 나타내는 `float`과 `double` 타입은 원래 모든 플랫폼에서 IEEE 754를 엄격하게
  따르도록 설계되었지만, 효율성의 이유로 이 제약은 완화되었습니다
  (정확환 일관성을 위해서는 `strictfp`가 필요합니다.).
{{site.alert.end}}


## Dart 숫자 표현

Dart에서 모든 숫자는 일반 `Object` 타입 계층의 부분이고,
두 가지 구체적인 숫자 타입으로 정수를 나타내는 `int`와
분수를 나타내는 `double`이 존재합니다.

<img 
  src="/assets/img/number-class-hierarchy.svg" 
  alt="Object is the parent of num, which is the parent of int and double">

플랫폼에 따라서 숫자 타입의 구현에 차이가 있습니다.
특히, Dart는 두 가지 다른 타입을 타겟으로 컴파일합니다.

* **네이티브:** 대부분의 경우이며, 64-bit 모바일 또는 데스크탑 프로세서입니다.
* **웹:** 기본 실행 엔진으로 JavaScript가 사용됩니다.

다음 테이블은 일반적으로 Dart 숫자가 어떻게 구현되는지 보여줍니다:

<div class="table-wrapper">
  <table class="table table-striped nowrap">
    <tr>
    <th>표현</th>
    <th>네이티브 <code>int</code></th>
    <th>네이티브 <code>double</code></th>
    <th>웹 <code>int</code></th>
    <th>웹 <code>double</code></th>
    </tr>
    <tr>
    <td><a href="https://en.wikipedia.org/wiki/Two%27s_complement">
      64-bit 부호화된 2의 보수</a>
    </td>
    <td>✅</td>
    <td></td>
    <td></td>
    <td></td>
    </tr>
    <tr>
    <td>
      <a href="https://en.wikipedia.org/wiki/Double-precision_floating-point_format">64-bit 부동소수점</a>
    </td>
    <td></td>
    <td>✅</td>
    <td>✅</td>
    <td>✅</td>
    </tr>
  </table>
</div>

네이티브를 타겟으로 하는 경우,
`int`는 부호화된 64-bit 정수 표현으로
`double`은 64-bit IEEE 부동소수점 표현으로 프로세서에 맞게 매핑됩니다.

반면에 Dart가 JavaScript를 컴파일하고 상호 운용하는 웹에서는
64-bit 배정도 부동소수점을 단일 숫자 표현으로 사용합니다.
효율성을 위해 Dart는 `int`와 `double`을 64-bit 배정도 부동소수점으로 매핑합니다.
사용할 수 있는 타입 계층은 여전히 동일하지만,
숨겨진 구현이 서로 다르고 얽혀 있습니다.

다음 그림은 네이티브와 웹의 플랫폼별 숫자 타입 계층을 보여줍니다.
그림에서 알 수 있듯이,
네이티브의 구체적인 `int` 타입은 `int` 인터페이스만 구현합니다.
그러나 웹의 `int`에 대한 타입은 `int`와 `double`을 모두 구현합니다.

<img 
  src="/assets/img/number-platform-specific.svg" 
  alt="Implementation classes vary by platform; for JavaScript, the class that implements int also implements double">


{{site.alert.note}}
  Dart는 효율성을 위해 `int`와 `double`을
  다른 방식으로 표현하지만, 이 구현 클래스들은 숨겨져 있습니다(위의 파란색).
  일반적으로, 플랫폼별 타입을 무시해도 되고 `int`와 `double`을 구체적인 타입으로 생각해도 됩니다.
{{site.alert.end}}

웹의 `int`는 분수 부분이 없는 배정도 부동소수점 값으로 표현됩니다.
배정도 부동소수점은 53 비트의 정수 정밀도를 제공하므로 실제로 잘 작동합니다.
그러나, `int` 값은 동시에 항상 `double` 값입니다.


## 동작의 차이

대부분의 정수와 double 산술은 본질적으로 동일하게
작동합니다. 그러나, 특히 코드가 정밀도, 문자열 포매팅 또는 런타임 유형에
대해 엄격하다면 중요한 차이가 존재합니다.

이 섹션에서 설명한 것 처럼 산술 결과가 다를 경우 **플랫폼마다** 다르게 작동하며
**변경**될 수 있습니다.

{{site.alert.note}}
  이 페이지에서 설명하는 플랫폼별 동작은 더 일관적이거나 더 성능이 향상되도록 변경될 수 있습니다.
{{site.alert.end}}


### 정밀도

다음 표는 정밀도에 따라 숫자 표현이 달라지는 것을 보여줍니다.
`math`는 `dart:math` 라이브러리를 나타내며
`math.pow(2, 53)`은 2<sup>53</sup>입니다.
웹에서, 정수는 53 비트 이후에는 정밀도를 잃습니다.
2<sup>53</sup>와 2<sup>53</sup>+1는 같은 값으로 매핑됩니다.
네이티브의 숫자는 63 비트로 값을 나타내고 1 비트로 부호를 나타내므로 위의 두 값은 다른 값으로 매핑됩니다.

2<sup>63</sup>-1 to 2<sup>63</sup>를 비교하면
오버플로의 효과를 확인해볼 수 있습니다.
네이티브에서 후자는 2의 보수 산술에 의해 -2<sup>63</sup>로 오버플로 됩니다.
웹에서는 이러한 값들이 정밀도의 손실로 인한 근사치로
표현되기 때문에 오버플로가 발생하지 않습니다.

<div class="table-wrapper">
  <table class="table table-striped nowrap">
    <tr>
    <th>표현</th>
    <th>네이티브</th>
    <th>웹</th>
    </tr>
    <tr>
    <td><code>math.pow(2, 53) - 1</code></td>
    <td><code>9007199254740991</code></td>
    <td><code>9007199254740991</code></td>
    </tr>
    <tr>
    <td><code>math.pow(2, 53)</code></td>
    <td><code>9007199254740992</code></td>
    <td><code>9007199254740992</code></td>
    </tr>
    <tr>
    <td><code>math.pow(2, 53) + 1</code></td>
    <td><code>9007199254740993</code></td>
    <td><code>9007199254740992</code></td>
    </tr>
    <tr>
    <td><code>math.pow(2, 62)</code></td>
    <td><code>4611686018427387904</code></td>
    <td><code>4611686018427388000</code></td>
    </tr>
    <tr>
    <td><code>math.pow(2, 63) - 1</code></td>
    <td><code>9223372036854775807</code></td>
    <td><code>9223372036854776000</code></td>
    </tr>
    <tr>
    <td><code>math.pow(2, 63)</code></td>
    <td><code>-9223372036854775808</code></td>
    <td><code>9223372036854776000</code></td>
    </tr>
    <tr>
    <td><code>math.pow(2, 64)</code></td>
    <td><code>0</code></td>
    <td><code>18446744073709552000</code></td>
    </tr>
  </table>
</div>

### Identity

네이티브 플랫폼에서 `double`과 `int`는 구분되는 타입입니다:
어떤 값도 `double`이면서 동시에 `int`일 수 없습니다.
하지만 웹에서는 그렇지 않습니다.
이런 차이 때문에 플랫폼마다 참조하는 객체가 다를 수 있지만,
동등성 (`==`)은 그렇지 않습니다.

다음 표는 `==`과 `identical()`을 사용한 표현식의 결과를 보여줍니다.
`==`를 사용한 표현식은 네이티브와 웹에서의 결과가 같지만,
`identical()`을 사용한 표현식의 결과는 보통 다릅니다.

<div class="table-wrapper">
  <table class="table table-striped nowrap">
    <tr>
    <th>표현식</th>
    <th>네이티브</th>
    <th>웹</th>
    </tr>
    <tr>
    <td><code>1.0 == 1</code></td>
    <td><code>true</code></td>
    <td><code>true</code></td>
    </tr>
    <tr>
    <td><code>identical(1.0, 1)</code></td>
    <td><code>false</code></td>
    <td><code>true</code></td>
    </tr>
    <tr>
    <td><code>0.0 == -0.0</code></td>
    <td><code>true</code></td>
    <td><code>true</code></td>
    </tr>
    <tr>
    <td><code>identical(0.0, -0.0)</code></td>
    <td><code>false</code></td>
    <td><code>true</code></td>
    </tr>
    <tr>
    <td><code>double.nan == double.nan</code></td>
    <td><code>false</code></td>
    <td><code>false</code></td>
    </tr>
    <tr>
    <td><code>identical(double.nan, double.nan)</code></td>
    <td><code>true</code></td>
    <td><code>false</code></td>
    </tr>
    <tr>
    <td><code>double.infinity == double.infinity</code></td>
    <td><code>true</code></td>
    <td><code>true</code></td>
    </tr>
    <tr>
    <td><code>identical(double.infinity, double.infinity)</code></td>
    <td><code>true</code></td>
    <td><code>true</code></td>
    </tr>
  </table>
</div>

### 타입과 타입 체킹

웹의 기본 `int` 타입은 분수 부분이 없는 배정도 값을 가지며 `double`의 서브타입과 비슷합니다.
사실 웹에서 `x is int` 형태의 타입 체킹은 `x`가 분수 부분이 0인 숫자 (`double`)라면
true를 반환합니다.


결과적으로 웹에서는 다음 사항이 해당됩니다:

* 모든 Dart 숫자 (`num` 타입의 값)은 `double` 입니다.
* Dart 숫자는 `double`이면서 동시에 `int` 입니다.

이런 사항들은 `is` 체킹과 `runtimeType` 프로퍼티에 영향을 미칩니다.
`double.infinity`가 `int`로 평가되는 것은 부작용이고,
이런 결과는 플랫폼별 동작이므로 미래에 변경될 것입니다.

<div class="table-wrapper">
  <table class="table table-striped nowrap">
    <tr>
    <th>표현식</th>
    <th>네이티브</th>
    <th>웹</th>
    </tr>
    <tr>
    <td><code>1 is int</code></td>
    <td><code>true</code></td>
    <td><code>true</code></td>
    </tr>
    <tr>
    <td><code>1 is double</code></td>
    <td><code>false</code></td>
    <td><code>true</code></td>
    </tr>
    <tr>
    <td><code>1.0 is int</code></td>
    <td><code>false</code></td>
    <td><code>true</code></td>
    </tr>
    <tr>
    <td><code>1.0 is double</code></td>
    <td><code>true</code></td>
    <td><code>true</code></td>
    </tr>
    <tr>
    <td><code>(0.5 + 0.5) is int</code></td>
    <td><code>false</code></td>
    <td><code>true</code></td>
    </tr>
    <tr>
    <td><code>(0.5 + 0.5) is double</code></td>
    <td><code>true</code></td>
    <td><code>true</code></td>
    </tr>
    <tr>
    <td><code>3.14 is int</code></td>
    <td><code>false</code></td>
    <td><code>false</code></td>
    </tr>
    <tr>
    <td><code>3.14 is double</code></td>
    <td><code>true</code></td>
    <td><code>true</code></td>
    </tr>
    <tr>
    <td><code>double.infinity is int</code></td>
    <td><code>false</code></td>
    <td><code>true</code></td>
    </tr>
    <tr>
    <td><code>double.nan is int</code></td>
    <td><code>false</code></td>
    <td><code>false</code></td>
    </tr>
    <tr>
    <td><code>1.0.runtimeType</code></td>
    <td><code>double</code></td>
    <td><code>int</code></td>
    </tr>
    <tr>
    <td><code>1.runtimeType</code></td>
    <td><code>int</code></td>
    <td><code>int</code></td>
    </tr>
    <tr>
    <td><code>1.5.runtimeType</code></td>
    <td><code>double</code></td>
    <td><code>double</code></td>
    </tr>
  </table>
</div>

### 비트 단위 연산

웹에서는 성능의 이유로
`int`에 대한 비트 단위 (`&`, `|`, `^`, `~`) 그리고 쉬프트 (`<<`,`>>`, `>>>`) 연산자는
네이티브 JavaScript의 연산자를 사용합니다.
JavaScript에서 피연산자는 부호가 없는 32-bit 정수로 잘립니다.
이런 처리는 더 큰 수에서 예상하지 못한 결과를 가져올 수 있습니다.
특히 피연산자가 음수이거나 32 비트에 맞지 않으면,
네이티브와 웹에서 서로 다른 결과를 만들 수도 있습니다.

다음 표는 네이티브와 웹 플랫폼에서 피연산자가 음수이거나 32 비트에
가까운 수 일 때 비트 단위, 쉬프트 연산자를 어떻게 처리하는지 보여줍니다:

<div class="table-wrapper">
  <table class="table table-striped nowrap">
    <tr>
    <th>표현식</th>
    <th>네이티브</th>
    <th>웹</th>
    </tr>
    <tr>
    <td><code>-1 >> 0</code></td>
    <td><code>-1</code></td>
    <td><code>4294967295</code></td>
    </tr>
    <tr>
    <td><code>-1 ^ 2</code></td>
    <td><code>-3</code></td>
    <td><code>4294967293</code></td>
    </tr>
    <tr>
    <td><code>math.pow(2, 32).toInt()</code></td>
    <td><code>4294967296</code></td>
    <td><code>4294967296</code></td>
    </tr>
    <tr>
    <td><code>math.pow(2, 32).toInt() >> 1</code></td>
    <td><code>2147483648</code></td>
    <td><code>0</code></td>
    </tr>
    <tr>
    <td><code>(math.pow(2, 32).toInt()-1) >> 1</code></td>
    <td><code>2147483647</code></td>
    <td><code>2147483647</code></td>
    </tr>
  </table>
</div>

### 문자열 표현

웹에서 Dart는 일반적으로 숫자를 문자열로 변환하기 위해 JavaScript의 방법을 따릅니다. (예를 들어, `print`)
다음 표는 첫 번째 열의 표현식의 변환이 플랫폼에 따라 어떻게 다른지 보여줍니다.

<div class="table-wrapper">
  <table class="table table-striped nowrap">
    <tr>
    <th>표현식</th>
    <th>네이티브 <code>toString()</code></th>
    <th>웹 <code>toString()</code></th>
    </tr>
    <tr>
    <td><code>1</code></td>
    <td><code>"1"</code></td>
    <td><code>"1"</code></td>
    </tr>
    <tr>
    <td><code>1.0</code></td>
    <td><code>"1.0"</code></td>
    <td><code>"1"</code></td>
    </tr>
    <tr>
    <td><code>(0.5 + 0.5)</code></td>
    <td><code>"1.0"</code></td>
    <td><code>"1"</code></td>
    </tr>
    <tr>
    <td><code>1.5</code></td>
    <td><code>"1.5"</code></td>
    <td><code>"1.5"</code></td>
    </tr>
    <tr>
    <td><code>-0</code></td>
    <td><code>"0"</code></td>
    <td><code>"-0.0"</code></td>
    </tr>
    <tr>
    <td><code>math.pow(2, 0)</code></td>
    <td><code>"1"</code></td>
    <td><code>"1"</code></td>
    </tr>
    <tr>
    <td><code>math.pow(2, 80)</code></td>
    <td><code>"0"</code></td>
    <td><code>"1.2089258196146292e+24"</code></td>
    </tr>
  </table>
</div>

## 어떻게 해야 할까요?

일반적으로 숫자 코드를 수정할 필요가 없습니다.
Dart 코드는 수년 동안 네이티브와 웹 플랫폼 모두에서 실행되어 왔고,
숫자 구현의 차이는 거의 문제가 되지 않았습니다. 좁은 정수 범위를 반복하고
리스트를 인덱싱하는 것과 같은 일반적이고 흔한 코드는 동일하게 작동합니다.

문자열 결과를 비교하는 테스트 또는 assertion이 있는 경우 플랫폼 복원 방식으로 작성하세요.
예를 들어 숫자가 포함된 문자열 표현식의 값을 테스트한다고 가정해 봅시다:

```dart
void main() {
  var count = 10.0 * 2;
  var message = "$count cows";
  if (message != "20.0 cows") throw Exception("Unexpected: $message");
}
```

앞선 코드는 네이티브에서 에러가 발생하지 않지만,
웹에서 `message`는 `"20 cows"`(소수점 없음)이기 때문에 예외가 발생합니다.
또는 다음과 같이 조건을 작성하면 네이티브와 웹 플랫폼 모두에서 통과할 수 있습니다:

```dart
if (message != "${20.0} cows") throw ... 
```

비트 조작의 경우 모든 플랫폼에서 일관된 32-bit 청크에 명시적으로 연산하는 것을 고려하세요.
32-bit 청크의 부호화된 결과를 원한다면 `int.toSigned(32)`를 사용하세요.

정밀도가 중요한 경우에는 다른 숫자 타입을 고려하세요.
[`BigInt`][] 타입은 네이티브와 웹에서 임의 정밀도 정수를 제공합니다.
[`fixnum`][] 패키지는 웹에서 엄격한 64-bit 부호화된 숫자를 제공합니다.
이러한 타입은 코드가 훨씬 크고 느려지는 경우가 많기 때문에 사용에 주의하세요.

[`BigInt`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/BigInt-class.html
[`fixnum`]: {{site.pub-pkg}}/fixnum
