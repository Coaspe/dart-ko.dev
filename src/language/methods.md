---
title: 메서드
description: Dart의 메서드에 대해 학습합니다.
---

메서드는 객체가 특정한 행동을 할 수 있도록 해주는 함수입니다.

## 인스턴스 메서드

객체의 인스턴스 메서드는 인스턴스 변수와 `this`에 접근 할 수 있습니다.
다음 예제의 `distanceTo()` 메서드가 인스턴스 메서드의 예입니다:

<?code-excerpt "misc/lib/language_tour/classes/point.dart (class-with-distanceTo)" plaster="none"?>
```dart
import 'dart:math';

class Point {
  final double x;
  final double y;

  Point(this.x, this.y);

  double distanceTo(Point other) {
    var dx = x - other.x;
    var dy = y - other.y;
    return sqrt(dx * dx + dy * dy);
  }
}
```

## 연산자

연산자는 특별한 이름을 가진 인스턴스 메소드 입니다.
Dart는 클래스 내에서 다음의 연산자들을 재정의 할 수 있습니다:

`<`  | `+`  | `|`  | `>>>`
`>`  | `/`  | `^`  | `[]`
`<=` | `~/` | `&`  | `[]=`
`>=` | `*`  | `<<` | `~`
`-`  | `%`  | `>>` | `==`
{:.table}

{{site.alert.note}}
  위의 표에 `!=` 같은 [연산자][operators]가 없다는 것을 알 수 있습니다.
  그런 표현들은 신택틱 슈가(syntactic sugar)이기 때문입니다.
  예를 들어, `e1 != e2` 같은 표현은 `!(e1 == e2)` 의 신택틱 슈가입니다.
{{site.alert.end}}

{%- comment %}
  Internal note from https://github.com/dart-lang/site-www/pull/2691#discussion_r506184100:
  -  `??`, `&&` and `||` are excluded because they are lazy / short-circuiting operators
  - `!` is probably excluded for historical reasons
{% endcomment %}

연산자 선언은 내장된 `operator` 식별자를 사용합니다.
다음의 예제는 vector 덧셈(`+`), 뺄셈(`-`) 그리고 항등(`==`)을 정의합니다.

<?code-excerpt "misc/lib/language_tour/classes/vector.dart"?>
```dart
class Vector {
  final int x, y;

  Vector(this.x, this.y);

  Vector operator +(Vector v) => Vector(x + v.x, y + v.y);
  Vector operator -(Vector v) => Vector(x - v.x, y - v.y);

  @override
  bool operator ==(Object other) =>
      other is Vector && x == other.x && y == other.y;

  @override
  int get hashCode => Object.hash(x, y);
}

void main() {
  final v = Vector(2, 3);
  final w = Vector(2, 2);

  assert(v + w == Vector(4, 5));
  assert(v - w == Vector(0, 1));
}
```


## Getter, setter

Getter와 setter는 객체의 프로퍼티를 읽고(get) 쓰는(set) 함수 입니다.
모든 인스턴스 변수는 암묵적으로 getter와 setter를 가진다는 것을 기억하세요.
`get`과 `set` 키워드를 사용하여 getter와 setter를 구현하므로써 추가적인 프로퍼티를 생성 할 수 있습니다.


<?code-excerpt "misc/lib/language_tour/classes/rectangle.dart"?>
```dart
class Rectangle {
  double left, top, width, height;

  Rectangle(this.left, this.top, this.width, this.height);

  // right, bottom 이라는 두 개의 계산된 프로퍼티 정의.
  double get right => left + width;
  set right(double value) => left = value - width;
  double get bottom => top + height;
  set bottom(double value) => top = value - height;
}

void main() {
  var rect = Rectangle(3, 4, 20, 15);
  assert(rect.left == 3);
  rect.right = 12;
  assert(rect.left == -8);
}
```
<!-- 번역이 확실하지 않음 -->
Getter 및 setter를 사용하는 이점은 인스턴스 변수를 먼저 사용한 다음 코드를 변경하지 않고
나중에 메서드로 래핑할 수 있다는 것입니다
즉, 먼저 정의한 다음 원래 코드에 영향을 주지 않고 변경할 수 있습니다.

{{site.alert.note}}
  증가 (++)와 같은 연산자는 getter가 명백히 정의되어 있든 말든 특정 방법으로 동작합니다.
  예상치 못한 부작용을 피하기 위해 연산자는 getter를 정확히 한 번 호출하여 값을 임시 변수에 저장하세요.
{{site.alert.end}}

## 추상 메서드

인스턴스, getter, setter 메서드는 추상화될 수 있습니다.
추상화란 인터페이스만 구현한 상태로 나머지 부분은 다른 클래스들에게 맡기는 것을 의미합니다.
추상 메서드는 오직 [추상 클래스][abstract classes]에 존재할 수 있습니다.

메서드를 추상화 하려면, 메서드 바디 대신에 세미콜론 (;)을 사용하세요:

<?code-excerpt "misc/lib/language_tour/classes/doer.dart"?>
```dart
abstract class Doer {
  // 인스턴스 변수와 메서드 정의 ...

  void doSomething(); // 추상 메서드 정의.
}

class EffectiveDoer extends Doer {
  void doSomething() {
    // 메서드를 구현하여 더 이상 추상적이지 않게 만듬 ...
  }
}
```

[operators]: /language/operators
[abstract classes]: /language/class-modifiers#abstract
