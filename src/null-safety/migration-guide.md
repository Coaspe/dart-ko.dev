---
title: Null 안전성으로 마이그레이션
description: 기존의 코드를 null 안전성을 준수하는 코드로 수정하는 방법을 배웁니다.
---

{% include yt_shims.liquid %}

{{site.alert.version-note}}
  Dart 2.19는 `dart migrate` 툴을 포함하며,
  null 안전성 마이그레이션을 지원하는 마지막 릴리즈입니다.
  패키지를 null 안전성으로 마이그레이트하고 싶다면,
  최신 Dart 2.19.6 SDK를 사용하세요.
  더 자세한 사항은,
  [Dart 3와 null 안전성](/null-safety#dart-3-and-null-safety)을 참고하세요.
{{site.alert.end}}

이 페이지는 언제 어떻게 [null 안전성][]을 준수하는 코드로 마이그레이션 해야 하는지 설명합니다.
각 패키지를 마이그레이션하기 위한 기본 단계는 다음과 같습니다:

1. 의존하는 패키지 마이그레이션이 완료될 때까지 [**기다리세요**](#step1-wait).
2. 상호작용형 마이그레이션 도구를 사용하여 패키지 코드를 [**마이그레이션 하세요**](#step2-migrate).
3. 패키지 코드를 [**정적으로 분석하세요**](#step3-analyze).
4. 변경 사항이 제대로 작동하는지 [**테스트 하세요**](#step4-test).
5. 해당 패키지가 이미 pub.dev에 있다면,
   null-safe 버전을 **프리릴리즈** 버전으로 [**퍼블리시 하세요**](#step5-publish).

{{site.alert.tip}}
  애플리케이션이나 라이브러리가 크다면, 
  [큰 Dart 프로젝트를 위한 점진적인 null 안전성 마이그레이션][]을 참고하세요.
{{site.alert.end}}

{{site.alert.info}}
  **앱을 마이그레이션하는 것은 패키지를 마이그레이션하는 것과 기술적으로 동일합니다.**
  앱을 마이그레이션하기 전에,
  의존하는 모든 패키지 마이그레이션이 준비되었는지 확인하세요.
{{site.alert.end}}

To get an informal look at the experience of using the migration tool,
watch this video:

<iframe
  {{yt-std-size}}
  title="Learn about the null-safety migration tool"
  src="{{yt-embed}}/eBr5tlumwlg"
  {{yt-set}}>
</iframe>

[null 안전성]: /null-safety
[큰 Dart 프로젝트를 위한 점진적인 null 안전성 마이그레이션]: https://medium.com/dartlang/gradual-null-safety-migration-for-large-dart-projects-85acb10b64a9


## 1. 마이그레이션을 대기하세요 {#step1-wait}

우리는 개발자들이 코드를 순차적으로 마이그레이션할 것을 권고하며,
먼저 의존 관계의 가장 말단에 있는 의존성을 마이그레이션할 것을 권장합니다.
예를 들어, C가 B에 의존하고 B가 A에 의존하는 경우 A -> B -> C의 순서로
마이그레이션 해야 합니다.

![Illustration of C/B/A sentence](/assets/img/null-safety/null-safety-migration-order.png){:width="454px"}<br>

모든 의존성의 마이그레이션이 완료되기 전에 [마이그레이션을 _수행할 수 있지만_][Unsound null safety],
그렇게 하면 마이그레이션이 완료된 후에 코드를 다시 변경해야 할 수 있습니다.
예를 들어, 함수가 nullable 매개변수를 받을 수 있다고 추측할 경우, 의존 관계에 있는
패키지의 마이그레이션으로 인해 해당 매개변수가 non-nullable로 변경되면, nullable 매개변수를 전달할 때
컴파일 에러가 발생합니다.

{{site.alert.info}}
  **당신의 패키지에 의존하는 패키지를 마이그레이션하기 전에 당신의 패키지를 먼저 마이그레이션 해야합니다.**
  Dart 2.12 이상을 사용하는 경우에 마이그레이션된 패키지는 마이그레이션되지 않은 패키지와 앱에
  사용할 수 있습니다. 예를 들어, Dart와 Flutter의 핵심 라이브러리는 이제 null 안전성을
  준수하도록 마이그레이션되었으며 마이그레이션되지 않은 앱도 여전히 사용할 수 있습니다.
{{site.alert.end}}

이 섹션에서는 null 안전성 모드에서 `dart pub outdated`를 사용하여
종속성을 확인하고 업데이트하는 방법에 대해 설명합니다. 다음 지침은
코드의 **버전 관리**가 되고 있고, 언제든지 모든 변경을 롤백할 수 있다고 가정합니다.

<a id="switch-to-the-latest-stable-dart-release"></a>
### Switch to the Dart 2.19.6 release

Dart SDK를 최신 **Dart 2.19.6 릴리즈**로 전환하세요.
이 버전은 Flutter 3.7.12 SDK에 포함되어있습니다.

Dart 버전이 2.19.6인지 확인하세요:

```terminal
$ dart --version
Dart SDK version: 2.19.6
```

### 종속 상태 확인

다음 커맨드를 사용하여 패키지 의존성의 마이그레이션 상태를 체크합니다:

```terminal
$ dart pub outdated --mode=null-safety
```

모든 패키지가 null 안전성을 지원한다면, 마이그레이션을 진행하면 됩니다.
그렇지 않는다면 **Resolvable** 열에 나열된 null 안전성 버전으로
마이그레이션 하세요.

{{site.alert.info}}
  **왜 모든 의존성이 null 안전성을 지원해야 할까요?**
  앱의 모든 의존성이 null 안전성을 지원하는 경우 견고한 null 안전성을 만족하는
  환경에서 _앱을 실행_ 할 수 있습니다. 마찬가지로 개발 의존성이 null 안전성을 지원하면,
  견고한 null 안전성을 만족하는 환경에서 앱을 _테스트_ 할 수 있습니다.
  코드 생성과 같은 이유로 null-safe한 개발 의존성이 필요한 경우가 있습니다.
{{site.alert.end}}

다음은 간단한 패키지에 대한 예제입니다.
각 패키지의 녹색 체크 표시는 해당 버전이 null 안전성을 지원함을 나타냅니다:

![Output of dart pub outdated](/assets/img/null-safety/pub-outdated-output.png)

위의 출력은 패키지의 의존성이 사용 가능한 null 안전성을 지원하는 프리 릴리즈 버전을
가지고 있음을 보여줍니다.

패키지 의존성 중 일부가 아직 null 안전성을 지원하지 않는 경우,
해당 패키지 작성자에게 연락하는 것을 추천합니다.
[pub.dev][]의 패당 패키지 페이지에서 작성자의 연락처를 찾을 수 있습니다.

[pub.dev]: {{site.pub}}


### 의존성 업데이트

패키지 코드를 마이그레이션하기 전에,
패키지의 의존성을 null-safe한 버전으로 업데이트 하세요:

1. Null 안전성을 지원하는 최신 버전으로 업그레이드하고 싶다면 `dart pub upgrade --null-safety` 커맨드를 실행하세요.
   **Note:** 이 커맨드는 `pubspec.yaml` 파일을 수정합니다.

2. `dart pub get` 커맨드를 실행하세요.


## 2. Migrate {#step2-migrate}

Most of the changes that your code needs to be null safe
are easily predictable.
For example, if a variable can be `null`,
[its type needs a `?` suffix][nullable type]. 
If a named parameter shouldn't be nullable,
mark it [`required`][required]
or give it a [default value][].

You have two options for migrating:

* [Use the migration tool][migration tool],
  which can make most of the easily predictable changes for you.
* [Migrate your code by hand.](#migrating-by-hand)

{{site.alert.tip}}
  For additional help while migrating code, check the
  [null safety FAQ][].
{{site.alert.end}}

[nullable type]: /null-safety#creating-variables
[required]: /null-safety/understanding-null-safety#required-named-parameters
[default value]: /language/functions#default-parameters
[migration tool]: #migration-tool
[null safety FAQ]: /null-safety/faq


### Using the migration tool {#migration-tool}

The migration tool takes a package of null-unsafe Dart code
and converts it to null safety.
You can guide the tool's conversion by
adding [hint markers][] to your Dart code.

[hint markers]: #hint-markers

Before starting the tool, make sure you're ready:

* Use the 2.19.6 release of the Dart SDK.
* Use `dart pub outdated --mode=null-safety` to make sure that
  all dependencies are null safe and up-to-date.
  
Start the migration tool by running the `dart migrate` command
in the directory that contains the package's `pubspec.yaml` file:

```terminal
$ dart migrate
```

If your package is ready to migrate,
then the tool produces a line like the following:

```terminal
View the migration suggestions by visiting:

  http://127.0.0.1:60278/Users/you/project/mypkg.console-simple?authToken=Xfz0jvpyeMI%3D
```

Visit that URL in a Chrome browser
to see an interactive UI
where you can guide the migration process:

![Screenshot of migration tool](/assets/img/null-safety/migration-tool.png)

For every variable and type annotation,
you can see what nullability the tool infers.
For example, in the preceding screenshot,
the tool infers that the `ints` list (previously a list of `int`)
in line 1 is nullable, and thus should be a list of `int?`.


#### Understanding migration results

To see the reasons for each change (or non-change),
click its line number in the **Proposed Edits** pane.
The reasons appear in the **Edit Details** pane.

For example, consider the following code,
from before null safety:

```dart
var ints = const <int>[0, null];
var zero = ints[0];
var one = zero + 1;
var zeroOne = <int>[zero, one];
```

The default migration when this code is outside a function
(it's different within a function)
is backward compatible but not ideal:

```dart
var ints = const <int?>[0, null];
var zero = ints[0];
var one = zero! + 1;
var zeroOne = <int?>[zero, one];
```

By clicking the **line 3** link,
you can see the migration tool's reasons for
adding the `!`.
Because you know that `zero` can't be null,
you can improve the migration result.


#### Improving migration results {#hint-markers}

When analysis infers the wrong nullability,
you can override its proposed edits by inserting temporary hint markers:

* In the **Edit Details** pane of the migration tool,
  you can insert hint markers using the
  **Add `/*?*/` hint** and **Add `/*!*/` hint** buttons.

  These buttons add comments to your file immediately,
  and there's **no Undo**.
  If you don't want a hint that the tool inserted,
  you can use your usual code editor to remove it.

* You can use an editor to add hint markers,
  even while the tool is still running.
  Because your code hasn't opted into null safety yet,
  you can't use new null-safety features.
  You can, however, make changes like refactoring
  that don't depend on null-safety features.

  When you've finished editing your code,
  click **Rerun from sources** to pick up your changes.

The following table shows the hint markers that you can use
to change the migration tool's proposed edits.

|------------------+--------------------------------------------------------------------------|
| Hint marker      | Effect on the migration tool                                             |
|------------------|--------------------------------------------------------------------------|
| <code><em>expression</em>&nbsp;/*!*/</code> | Adds a `!` to the migrated code, casting _expression_ to its underlying non-nullable type. |
| <code><em>type</em> /*!*/</code> | Marks _type_ as non-nullable. |
| `/*?*/`          | Marks the preceding type as nullable.                       |
| `/*late*/`       | Marks the variable declaration as `late`, indicating that it has late initialization. |
| `/*late final*/` | Marks the variable declaration as `late final`, indicating that it has late, one-time initialization. |
| `/*required*/`   | Marks the parameter as `required`.                                        |
{:.table .table-striped}

A single hint can have ripple effects elsewhere in the code.
In the example from before,
manually adding a `/*!*/` marker where `zero` is assigned its value (on line 2)
makes the migration tool infer the type of `zero` as `int` instead of `int?`.
This type change can affect code that directly or indirectly uses `zero`.

```dart
var zero = ints[0]/*!*/;
```

With the above hint, the migration tool changes its proposed edits,
as the following code snippets show.
Line 3 no longer has a `!` after `zero`,
and in line 4 `zeroOne` is inferred to be
a list of `int`, not `int?`.

<table markdown="1">
<tr>
<th>First migration</th>
<th>Migration with hint</th>
</tr>
<tr markdown="1">
  <td markdown="1">
```dart
var ints = const <int?>[0, null];
var zero = ints[0];
var one = zero! + 1;
var zeroOne = <int?>[zero, one];
```
  </td>
  <td markdown="1">
```dart
var ints = const <int?>[0, null];
var zero = ints[0]/*!*/;
var one = zero + 1;
var zeroOne = <int>[zero, one];
```
  </td>
</tr>
</table>

#### Opting out files

Although we recommend migrating all at once,
sometimes that isn't practical,
especially in a large app or package.
To opt out a file or directory,
click its green checkbox.
Later, when you apply changes,
each opted out file will be unchanged
except for a 2.9 [version comment][].

For more information about incremental migration, see
[Unsound null safety][].

Note that only fully migrated apps and packages 
are compatible with Dart 3.

[version comment]: /guides/language/evolution#per-library-language-version-selection


#### Applying changes

When you like all of the changes
that the migration tool proposes, click **Apply migration**.
The migration tool deletes the hint markers and
saves the migrated code.
The tool also updates the minimum SDK constraint in the pubspec,
which opts the package into null safety.

The next step is to [statically analyze your code](#step3-analyze).
If it's valid, then [test your code](#step4-test).
Then, if you've published your code on pub.dev,
[publish a null-safe prerelease](#step5-publish).


### Migrating by hand

If you prefer not to use the migration tool,
you can migrate manually.

We recommend that you **first migrate leaf libraries**—libraries 
that don't import other files from the package.
Then migrate libraries that directly depend on the leaf libraries.
End by migrating the libraries that have the most
intra-package dependencies.

For example, say you have a `lib/src/util.dart` file
that imports other (null-safe) packages and core libraries,
but that doesn't have any `import '<local_path>'` directives.
Consider migrating `util.dart` first,
and then migrating simple files that depend only on `util.dart`.
If any libraries have cyclic imports
(for example, A imports B which imports C, and C imports A),
consider migrating those libraries together.

To migrate a package by hand, follow these steps:

1. Edit the package's `pubspec.yaml` file,
   setting the minimum SDK constraint to at least `2.12.0`:

   ```yaml
   environment:
     sdk: '>=2.12.0 <3.0.0'
   ```

2. Regenerate the [package configuration file][]:

   ```terminal
   $ dart pub get
   ```

   [package configuration file]: https://github.com/dart-lang/language/blob/main/accepted/2.8/language-versioning/package-config-file-v2.md

   Running `dart pub get` with a lower SDK constraint of at least `2.12.0`
   sets the default language version of
   every library in the package to a minimum of 2.12,
   opting them all in to null safety.

3. Open the package in your IDE. <br>
   You're likely to see a lot of analysis errors.
   That's OK.

4. Migrate the code of each Dart file,
   using the analyzer to identify static errors. <br>
   Eliminate static errors by adding `?`, `!`, `required`, and `late`,
   as needed.

See [Unsound null safety][]
for more help on migrating code by hand.

[Unsound null safety]: /null-safety/unsound-null-safety


## 3. Analyze {#step3-analyze}

Update your packages
(using `dart pub get` in your IDE or on the command line).
Then use your IDE or the command line
to perform [static analysis][] on your code:

```terminal
$ dart pub get
$ dart analyze     # or `flutter analyze`
```

[static analysis]: /tools/analysis


## 4. Test {#step4-test}

If your code passes analysis, run tests:

```terminal
$ dart test       # or `flutter test`
```

You might need to update tests that expect null values.

If you need to make large changes to your code,
then you might need to remigrate it.
If so, revert your code changes before using the migration tool again.


## 5. Publish {#step5-publish}

We encourage you to publish packages—possibly as prereleases—as 
soon as you migrate:

* [Set the package version to indicate a breaking change.](#package-version)
* [Update the SDK constraints and package dependencies.](#check-your-pubspec)
* [Publish the package](/tools/pub/publishing).
  If you don't consider this version to be a stable release, 
  then [publish the package as a prerelease][].

[publish the package as a prerelease]: /tools/pub/publishing#publishing-prereleases

### Update the package version {#package-version}

Update the version of the package
to indicate a breaking change:

* If your package is already at `1.0.0` or greater,
  increase the major version.
  For example, if the previous version is `2.3.2`,
  the new version is `3.0.0`.

* If your package hasn't reached `1.0.0` yet,
  _either_ increase the minor version _or_ update the version to `1.0.0`.
  For example, if the previous version is `0.3.2`,
  the new version is either `0.4.0` or `1.0.0`.

### Check your pubspec

Before you publish a stable null safety version of a package, 
we strongly recommend following these pubspec rules:

* Set the Dart lower SDK constraint to the lowest stable version
  that you've tested against (at least `2.12.0`).
* Use stable versions of all direct dependencies.

## Welcome to null safety

If you made it this far,
you should have a fully migrated, null-safe Dart package.

If all of the packages you depend on are migrated too,
then your program is sound with respect to null-reference errors.
You should see output like this when running or compiling your code:

```terminal
Compiling with sound null safety
```

From all of the Dart team, *thank you* for migrating your code.
