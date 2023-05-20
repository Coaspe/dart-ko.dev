---
title: Mixins
description: Dart의 클래스에 기능을 추가하는 방법에 대해 학습합니다.
toc: false
---

<?code-excerpt replace="/ *\/\/\s+ignore_for_file:[^\n]+\n//g; /(^|\n) *\/\/\s+ignore:[^\n]+\n/$1/g; /(\n[^\n]+) *\/\/\s+ignore:[^\n]+\n/$1\n/g; / *\/\/\s+ignore:[^\n]+//g; /([A-Z]\w*)\d\b/$1/g"?>

Mixins은 다수의 클래스 계층에서 클래스의 코드를 재사용 할 수 있는 방법입니다.
Mixins은 일괄적인 멤버 구현을 제공합니다.

한 개 이상의 mixin 이름과 함께 `with` 키워드를 적용하여 mixin을 사용하세요.
다음 코드처럼 `with` 키워드와 사용 할 mixin의 이름을 명시하면 됩니다:

<?code-excerpt "misc/lib/language_tour/classes/orchestra.dart (Musician and Maestro)" replace="/(with.*) \{/[!$1!] {/g"?>
{% prettify dart tag=pre+code %}
class Musician extends Performer [!with Musical!] {
  // ···
}

class Maestro extends Person [!with Musical, Aggressive, Demented!] {
  Maestro(String maestroName) {
    name = maestroName;
    canConduct = true;
  }
}
{% endprettify %}

`mixin` 선언을 사용하여 mixin을 정의하세요.
드물게 mixin _과_ class를 모두 정의해야하는 상황이 온다면,
[`mixin class` 선언](#class-mixin-or-mixin-class)을 사용하세요.

Mixins과 mixin 클래스에 `extends`문을 사용할 수 없고,
제너러티브 생성자를 가지면 안 됩니다:

<?code-excerpt "misc/lib/language_tour/classes/orchestra.dart (Musical)"?>
```dart
mixin Musical {
  bool canPlayPiano = false;
  bool canCompose = false;
  bool canConduct = false;

  void entertainMe() {
    if (canPlayPiano) {
      print('Playing piano');
    } else if (canConduct) {
      print('Waving hands');
    } else {
      print('Humming to self');
    }
  }
}
```

Mixin을 사용할 수 있는 타입을 제한할 수도 있습니다.
예를 들어, mixin이 정의하지 않은 메서드를 호출할 수 있는지에 따라 달라질 수 있습니다.
다음 예제처럼 `on` 키워드로 사용할 수 있는 부모 클래스를 제한함으로써 mixin의 사용을 제한할 수 있습니다:

<?code-excerpt "misc/lib/language_tour/classes/orchestra.dart (mixin-on)" plaster="none" replace="/on Musician2/[!on Musician!]/g" ?>
```dart
class Musician {
  // ...
}
mixin MusicalPerformer [!on Musician!] {
  // ...
}
class SingerDancer extends Musician with MusicalPerformer {
  // ...
}
```

위의 코드에서 `Musician` 클래스를 확장, 구현하는 클래스들만 `MusicalPerformer` mixin을 사용 할 수 있습니다.
`SingerDancer`가 `Musician`을 확장하기 때문에, `SingerDancer`는 `MusicalPerformer` mixin을 사용 할 수 있습니다.

## `class`, `mixin`, or `mixin class`?

{{site.alert.version-note}}
  The `mixin class` declaration requires a [language version][] of at least 3.0.
{{site.alert.end}}

A `mixin` declaration defines a mixin. A `class` declaration defines a [class][].
A `mixin class` declaration defines a class that is usable as both a regular class
and a mixin, with the same name and the same type.

Any restrictions that apply to classes or mixins also apply to mixin classes:

- Mixins can't have `extends` or `with` clauses, so neither can a `mixin class`.
- Classes can't have an `on` clause, so neither can a `mixin class`. 

### `abstract mixin class`

You can achieve similar behavior to the `on` directive for a mixin class. 
Make the mixin class `abstract` and define the abstract methods its behavior 
depends on:

```dart
abstract mixin class Musician {
  // No 'on' clause, but an abstract method that other types must define if 
  // they want to use (mix in or extend) Musician: 
  void playInstrument(String instrumentName);

  void playPiano() {
    playInstrument('Piano');
  }
  void playFlute() {
    playInstrument('Flute');
  }
}

class Virtuoso with Musician { // Use Musician as a mixin
  void playInstrument(String instrumentName) {
    print('Plays the $instrumentName beautifully');
  }  
} 

class Novice extends Musician { // Use Musician as a class
  void playInstrument(String instrumentName) {
    print('Plays the $instrumentName poorly');
  }  
} 
```

By declaring the `Musician` mixin as abstract, you force any type that uses
it to define the abstract method upon which its behavior depends. 

This is similar to how the `on` directive ensures a mixin has access to any
interfaces it depends on by specifying the superclass of that interface.

[language version]: /guides/language/evolution#language-versioning
[class]: /language/classes
[class modifiers]: /language/class-modifiers
