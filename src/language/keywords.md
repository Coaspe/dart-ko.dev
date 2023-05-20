---
title: 키워드
description: Dart의 키워드에 대해 학습합니다.
toc: false
---

다음 테이블 리스트는 Dart 언어가 특별히 관리하는 단어들입니다.

{% assign ckw = '&nbsp;<sup title="contextual keyword" alt="contextual keyword">1</sup>' %}
{% assign bii = '&nbsp;<sup title="built-in-identifier" alt="built-in-identifier">2</sup>' %}
{% assign lrw = '&nbsp;<sup title="limited reserved word" alt="limited reserved word">3</sup>' %}
<div class="table-wrapper" markdown="1">
| [abstract][]{{bii}}   | [else][]                 | [import][]{{bii}}     | [show][]{{ckw}}    |
| [as][]{{bii}}         | [enum][]                 | [in][]                | [static][]{{bii}}  |
| [assert][]            | [export][]{{bii}}        | [interface][]{{bii}}  | [super][]          |
| [async][]{{ckw}}      | [extends][]              | [is][]                | [switch][]         |
| [await][]{{lrw}}      | [extension][]{{bii}}     | [late][]{{bii}}       | [sync][]{{ckw}}    |
| [base][]{{bii}}       | [external][]{{bii}}      | [library][]{{bii}}    | [this][]           |
| [break][]             | [factory][]{{bii}}       | [mixin][]{{bii}}      | [throw][]          |
| [case][]              | [false][]                | [new][]               | [true][]           |
| [catch][]             | [final (variable)][]     | [null][]              | [try][]            |
| [class][]             | [final (class)][]{{bii}} | [on][]{{ckw}}         | [typedef][]{{bii}} |
| [const][]             | [finally][]              | [operator][]{{bii}}   | [var][]            |
| [continue][]          | [for][]                  | [part][]{{bii}}       | [void][]           |
| [covariant][]{{bii}}  | [Function][]{{bii}}      | [required][]{{bii}}   | [when][]           |
| [default][]           | [get][]{{bii}}           | [rethrow][]           | [while][]          |
| [deferred][]{{bii}}   | [hide][]{{ckw}}          | [return][]            | [with][]           |
| [do][]                | [if][]                   | [sealed][]{{bii}}     | [yield][]{{lrw}}   |
| [dynamic][]{{bii}}    | [implements][]{{bii}}    | [set][]{{bii}}        |                    |
{:.table .table-striped .nowrap}
</div>

[abstract]: /language/class-modifiers#abstract
[as]: /language/operators#타입-테스트-연산자
[assert]: /language/error-handling#assert
[async]: /language/async
[await]: /language/async
[base]: /language/class-modifiers#base
[break]: /language/loops#break-continue
[case]: /language/branches#switch
[catch]: /language/error-handling#catch
[class]: /language/classes#인스턴스-변수
[const]: /language/variables#final-const
{% comment %}
  [TODO #2950: Make sure that points to a place that talks about const constructors,
  as well as const literals and variables.]
{% endcomment %}
[continue]: /language/loops#break-continue
[covariant]: /guides/language/sound-problems#the-covariant-keyword
[default]: /language/branches#switch-case
[deferred]: /language/libraries#라이브러리-지연-로딩
[do]: /language/loops#while-do-while
[dynamic]: /language#주요-컨셉
[else]: /language/branches#if
[enum]: /language/enum
[export]: /guides/libraries/create-library-packages
[extends]: /language/클래스-확장
[extension]: /language/확장-메서드
[external]: https://spec.dart.dev/DartLangSpecDraft.pdf#External%20Functions
[factory]: /language/constructors#factory-생성자
[false]: /language/built-in-types#booleans
[final (variable)]: /language/variables#final과-const
[final (class)]: /language/class-modifiers#final
[finally]: /language/error-handling#finally
[for]: /language/loops#for-루프
[Function]: /language/functions
[get]: /language/methods#getters와-setters
[hide]: /language/libraries#라이브러리의-일부만-가져오기
[if]: /language/branches#if
[implements]: /language/classes#암묵적-인터페이스
[import]: /language/libraries#라이브러리-사용하기
[in]: /language/loops#for-루프
[interface]: /language/class-modifiers#인터페이스
[is]: /language/operators#타입-테스트-연산자
[late]: /language/variables#late-변수
[library]: /language/libraries
[mixin]: /language/mixins
[new]: /language/classes#생성자-사용하기
[null]: /language/variables#디폴트-값
[on]: /language/error-handling#catch
[operator]: /language/methods#연산자
[part]: /guides/libraries/create-library-packages#organizing-a-library-package
[required]: /language/functions#named-매개변수
[rethrow]: /language/error-handling#catch
[return]: /language/functions#반환-값
[sealed]: /language/class-modifiers#sealed
[set]: /language/methods#getters-and-setters
[show]: /language/libraries#라이브러리의-일부만-가져오기
[static]: /language/classes#클래스-변수와-메서드
[super]: /language/extend
[switch]: /language/branches#switch
[sync]: /language/functions#제너레이터
[this]: /language/constructors
[throw]: /language/error-handling#throw
[true]: /language/built-in-types#booleans
[try]: /language/error-handling#catch
[typedef]: /language/typedefs
[var]: /language/variables
[void]: /language/built-in-types
{% comment %}
  TODO #2950: Add coverage of void to the language tour.
{% endcomment %}
[when]: /language/branches#when
[with]: /language/mixins
[while]: /language/loops#while-do-while
[yield]: /language/functions#제너레이터

이 단어들을 식별자로 사용하는 것을 지양하세요.
그러나, 필요하다면 윗첨자로 표시된 단어들은 식별자로 사용이 가능합니다.

* **1**로 표시된 단어들은 **맥락적인 키워드(contextual keywords)**로
  특정한 장소에서만 의미를 가집니다.
  어디서든 유효한 식별자로 하용이 가능합니다.

* **2**로 표시된 단어들은 **내장 식별자(built-in identifiers)**로
  이 키워드들은 거의 모든 곳에서 식별자로 사용이 가능하지만,
  클래스나 타입의 이름, import 프리픽스로 사용은 불가능합니다.

* **3**으로 표시된 단어들은 [비동기 지원][asynchrony support]과
  관련된 제한된 단어들 입니다. `await` 또는 `yield`를
  `async`, `async*`, or `sync*`로 표시된 함수의 바디에서
  식별자로 사용 할 수 없습니다.

표의 나머지 단어들은 모두  **예약된 단어(reserved words)**들로,
식별자로 사용이 불가능 합니다.

[asynchrony support]: /language/async
