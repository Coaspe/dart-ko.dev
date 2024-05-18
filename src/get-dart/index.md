---
title: Dart SDK 설치
description: Dart 웹, 커맨드라인, 서버 앱 개발에 필요한 라이브러리, 커맨드라인 도구 설치.
js:
- url: /assets/js/get-dart/install.js
  defer: true
---

이 페이지는 Dart SDK 다운로드 방법에 대해 알려줍니다.
Dart SDK는 Dart 커맨드라인, 서버, 그리고 Flutter가 아닌
웹앱을 개발할 때 필요한 라이브러리와 커맨드라인 도구를 가지고 있습니다.
자세한 사항은 [Dart SDK 개요](/tools/sdk)를 참고하세요.

## Dart SDK 설치 {#install}

아래에 설명된 대로,
패키지 매니저를 사용하여 Dart SDK를
쉽게 설치하고 업데이트할 수 있습니다.
또한 [SDK 소스를 빌드][]하거나, [Dart 도커 이미지][]를 사용할 수도 있습니다.
[SDK의 zip 압축 파일을 다운로드][]하여 [원하는 릴리즈 채널](#release-channels)을
설치하는 것도 가능합니다.

{% comment %}
NOTE to editors: Keep the zip file link as the last thing in the paragraph,
so it's easy to find (but not more tempting than package managers).
{% endcomment %}

*Note*: Flutter SDK는 Dart SDK 전체를 포함하고 있으며,
`bin` 폴더에 Dart의 [`dart`](/tools/dart-tool)
커맨드라인 인터페이스를 가지고 있습니다.

{{site.alert.warn}}
  {% include_relative archive/_sdk-terms.md %}
{{site.alert.end}}

<ul class="tabs__top-bar">
  <li class="tab-link current" data-tab="tab-sdk-install-windows">윈도우</li>
  <li class="tab-link" data-tab="tab-sdk-install-linux">리눅스</li>
  <li class="tab-link" data-tab="tab-sdk-install-mac">macOS</li>
</ul>
<div id="tab-sdk-install-windows" class="tabs__content current" markdown="1">
{% include_relative _windows.md %}
</div>
<div id="tab-sdk-install-linux" class="tabs__content" markdown="1">
{% include_relative _linux.md %}
</div>
<div id="tab-sdk-install-mac" class="tabs__content" markdown="1">
{% include_relative _mac.md %}
</div>

## 시스템 요구사항

Dart SDK는 윈도우, 리눅스, 그리고 macOS를 지원합니다.

### 윈도우

* **지원 버전:** Windows 10, 11.
* **지원 아키텍처:** x64, IA32, ARM64.<br>
  ARM64의 지원은 실험 단계에 있으며, dev 그리고 beta 채널에서만 사용할 수 있습니다.

### 리눅스

* **지원 버전:** 표준 버전의 [Debian stable][]과 [Ubuntu LTS][]가 지원됩니다.
* **지원 아키텍쳐:** x64, IA32, ARM64, ARM, RISC-V (RV64GC).<br>
  RISC-V의 지원은 실험 단계에 있으며, dev와 beta 채널에서만 사용할 수 있습니다.

### macOS

* **지원 버전:** 최신 3가지 주요 버전.
2023년 11월 기준으로 Dart는 다음 macOS를 지원합니다:
  - macOS 12 (Monterey)
  - macOS 13 (Ventura)
  - macOS 14 (Sonoma)
* **지원 아키텍처:** x64, ARM64.

## 릴리즈 채널 및 버전 문자열 {#release-channels}

Dart SDK는 3가지 릴리즈 채널이 있습니다:

* **Stable** 채널: 3개월마다 업데이트되는 **안정적인 릴리즈**;
  현재 `[calculating]`{:.build-rev-stable}.
  
  이 버전은 프로덕션 목적으로 적합합니다.
  
* **Beta** 채널: **프리뷰 릴리즈**로 보통 매월 한 차례씩 업데이트 됩니다;
  현재 `[calculating]`{:.build-rev-beta}.
  
  Beta 채널 빌드는 stable 채널을 미리 사용해보는 빌드입니다. 이 채널의
  SDK를 사용하여 테스트하는 것을 추천하지만, 앱 출시는 권장하지 않으며,
  이 채널의 구성을 통해 새로운 기능을 미리 보거나 향후 버전과의 호환성을
  테스트할 수 있습니다.
  
* **Dev** 채널: **사전 릴리즈**로 보통 격주로 업데이트 됩니다;
  현재 `[calculating]`{:.build-rev-dev}.
  
  Dev 채널의 배포는 최신 업데이트가 적용된 버전이며, 손상되거나 지원되지 않으며,
  확인되지 않은 변경 사항이 포함되어 있을 수 있습니다.

Dart SDK의 **Stable** 릴리즈 채널의 버전 번호는
`1.24.3` 및 `2.1.0` 같은 `x.y.z` 형식의 문자열로 설정됩니다. 하이픈이나 문자가 없는 점으로
구분된 정수로 구성되며, `x`는 기본 버전, `y`는 보조 버전, `z`는 패치 버전입니다.

Dart SDK의 불안정 릴리즈인 **Beta** 그리고 **dev** 채널 릴리즈는
`2.8.0-20.11.beta`와 같이 `x.y.z-a.b.<beta|dev>` 형식의 버전을 가집니다.
하이픈 앞의 부분은 **Stable** 릴리즈의 체계를 따르고, 하이픈 뒤의 `a`와 `b`는 각각
사전 릴리즈 및 사전 릴리즈 패치 버전이며, `beta` 또는 `dev`는 채널을 나타냅니다.

Stable 채널 릴리즈는 이 페이지의 [설치 섹션](#install)을 따라하거나,
[패키지 매니저][] 또는 [Dart 도커 이미지][], 또는
[SDK의 zip 압축 파일을 다운로드][]를 통해
stable, beta, dev 채널 릴리즈를 다운로드 할 수 있습니다.

[SDK constraints]: /tools/pub/pubspec#sdk-constraints
[SDK 소스를 빌드]: https://github.com/dart-lang/sdk/wiki/Building
[Dart libraries]: /libraries
[Dart 도커 이미지]: https://hub.docker.com/_/dart
[SDK의 zip 압축 파일을 다운로드]: /get-dart/archive
[Debian stable]: https://www.debian.org/releases
[Ubuntu LTS]: https://wiki.ubuntu.com/Releases
[flutter]: https://flutter.dev/docs/get-started/install
[site SDK version]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/{{site.data.pkg-vers.SDK.vers}}/index.html
[패키지 매니저]: https://github.com/dart-lang/sdk/wiki/Installing-beta-and-dev-releases-with-brew,-choco,-and-apt-get
