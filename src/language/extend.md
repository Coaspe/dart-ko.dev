---
title: 클래스 확장
description: 클래스를 확장하는 방법에 대해 학습합니다.
prevpage:
  url: /language/methods
  title: 메서드
nextpage:
  url: /language/mixins
  title: 믹스인
---

자식 클래스를 만들고 싶다면, `extends`를 사용하세요.
해당 클래스 안에서 부모 클래스를 참조하고 싶다면 `super`를 사용하면 됩니다:

<?code-excerpt "misc/lib/language_tour/classes/extends.dart" replace="/extends|super/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
class Television {
  void turnOn() {
    _illuminateDisplay();
    _activateIrSensor();
  }
  // ···
}

class SmartTelevision [!extends!] Television {
  void turnOn() {
    [!super!].turnOn();
    _bootNetworkInterface();
    _initializeMemory();
    _upgradeApps();
  }
  // ···
}
{% endprettify %}

`extends`의 다른 사용법을 알고 싶다면,
[매개변수화된 타입][parameterized types]의
Generics 페이지를 참고하세요.

## 멤버 재정의

자식 클래스는 [연산자][operators]를 포함한 인스턴스 메서드, getter, setter를 오버라이드 하는 것이 가능합니다.
`@override` 표기를 사용하여 의도적으로 멤버를 재정의 할 수 있습니다:

<?code-excerpt "misc/lib/language_tour/metadata/television.dart (override)" replace="/@override/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
class Television {
  // ···
  set contrast(int value) {...}
}

class SmartTelevision extends Television {
  [!@override!]
  set contrast(num value) {...}
  // ···
}
{% endprettify %}

재정의 메서드 선언은 그 메서드가 재정의하는 메서드와 여러 가지 방법으로 매치되어야 합니다:

* 반환 타입은 반드시 재정의되는 함수의 반환 타입(서브 타입도 가능)과 동일해야 합니다.
* 인자의 타입은 오버라이딩 되는 함수의 인수 타입(supertype도 가능)과 반드시 동일해야 합니다.
  앞선 예제에서, `SmartTelevision`의 setter인 `contrast`는 인자의 타입을 `int`의 supertype인 `num`으로 변경합니다.
* 만약 재정의되는 함수가 _n_개의 positional 매개변수를 가진다면,
  재정의하는 함수 또한 _n_개의 positional 매개변수를 가져야 합니다.
* [제네릭 메서드][generic method]는 제네릭이 아닌 메서드를 재정의 할 수 없고,
  그 반대도 마찬가지 입니다.

메서드의 매개변수나 인스턴스 변수의 타입을 축소하고 싶은 때가 있을 겁니다.
이런 행동은 보통의 룰을 어기는 것이고, 런타임에서 에러를 발생시킬 수도 있는 다운 캐스팅과 비슷합니다.
여전히 코드가 타입 에러를 발생시키지 않는다고 확신할 수 있다면, 타입을 축소하는 것은 가능합니다.
이런 경우에, [`covariant` 키워드](/guides/language/sound-problems#the-covariant-keyword)
를 매개변수 선언에 사용하면 됩니다.
자세한 정보를 원한다면, [Dart 언어 설명서][Dart language specification]를 참고하세요.

{{site.alert.warning}}
  `==` 를 재정의하면, `hashCode` getter도 재정의해야 합니다.
  [Implementing map keys](/guides/libraries/library-tour#implementing-map-keys)에
  `==`와 `hashCode`를 재정의하는 예제가 있습니다.
{{site.alert.end}}

## noSuchMethod()

코드가 존재하지 않는 함수나 인스턴스 변수에 접근하는 것을 감지, 처리하고 싶다면 `noSuchMethod()` 함수를 재정의하세요:

<?code-excerpt "misc/lib/language_tour/classes/no_such_method.dart" replace="/noSuchMethod(?!,)/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
class A {
  // Unless you override noSuchMethod, using a
  // non-existent member results in a NoSuchMethodError.
  @override
  void [!noSuchMethod!](Invocation invocation) {
    print('You tried to use a non-existent member: '
        '${invocation.memberName}');
  }
}
{% endprettify %}

구현되지 않은 메서드가 다음 중 **하나**라도 만족한다면, 해당 메서드를 **호출 할 수 없습니다**.

* 리시버가 static 타입 `dynamic`일 때.

* 리시버는 구현되지 않은 메서드(추상 메서드는 가능)를 정의하는 static 타입을 가지며,
  리시버의 dynamic 타입은 클래스 `Object`와 다른 `noSuchMethod()`를 구현 했을 때.

더 자세한 정보를 원한다면, [noSuchMethod forwarding specification](https://github.com/dart-lang/language/blob/main/archive/feature-specifications/nosuchmethod-forwarding.md)을
참고하세요.

[parameterized types]: /language/generics#매개변수화된-타입-제한하기
[operators]: /language/methods#연산자
[generic method]: /language/generics#제네릭-메서드-사용하기
[Dart language specification]: /guides/language/spec
