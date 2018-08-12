---
layout: default
title: "Fast and efficient image loading for Android"
---


원문보기：[링크](http://bumptech.github.io/glide/)

### Glide 란

Glide 는 부드러운 스크롤링에 중점을 둔 빠르고 효율적인 안드로이드 이미지 로딩 라이브러리 입니다. 쉬운 API, 뛰어난 성능의 리소스 디코딩 파이프라인과 자동 리소스 풀링을 제공 합니다.

![](https://github.com/bumptech/glide/blob/master/static/glide_logo.png?raw=true)

Glide 는 비디오 스틸, 움직이는 GIF 나 이미지를 불러오거나 디코딩 및 디스플레이 해주는 기능을 지원 하며, 개발자로 하여금 거의 모든 네트워크 스택(network stack) 에 연결 가능한 유연한 API 를 제공 합니다.
기본적으로 Glide 는 커스텀 HttpUrlConnection 를 베이스로 하되, Google 의 Volley 나 Square 의 OkHttp 같은 라이브러리도 포함하고 있습니다.

Glide 의 주된 촛점은 매끄럽고 빠르게 모든 종류의 이미지 목록을 스크롤하는 것이지만, 원격으로 이미지를 가져오고
크기를 조정하고 디스플레이 해야 하는 거의 모든 경우에도 효과적입니다.

#### API

Glide 는 사용자가 대부분의 요청을 한 줄로 만들 수 있는 간단하고 유연한 API를 제공합니다:

```java
Glide.with(fragment)
    .load(url)
    .into(imageView);
```

#### 성능

Glide 는 Android 이미지 로딩 성능의 두가지 주요 측면을 고려하였습니다.

* 이미지를 디코딩 하는 속도
* 이미지를 디코딩하는 동안 발생하는 버벅임(jank)의 정도

사용자가 앱에서 좋은 경험을 하려면 이미지가 빨리 나타나야 할 뿐만 아니라 메인 스레드 I/O 에서 버벅거리지 않고 빠르게 나타나야 하며, 과도한 가비지 컬렉션을 유발하지 않아야 합니다.

Glide는 Android에서 이미지 로딩이 가능한 한 빠르고 매끄러워질 수 있도록 다음과 같은 일을 하고 있습니다.

* 스마트하고 자동적인 다운샘플링과 캐싱으로 저장공간 절약 및 디코드 시간을 최소화합니다.
* 바이트 배열이나 비트맵 같은 리소스를 최대한 재사용함으로 가비지 콜렉션 및 힙 조각화를 최소화 합니다.
* Android 라이프사이클과 깊이 연관 되어 활성화된 액티비티와 프래그먼트의 요청을 우선 순위에 따라 수행 하고, 백그라운드에서 죽는 것을 피하기 위해 필요시에만 리소스 릴리즈를 수행 합니다.

### 시작하기

먼저 [다운로드 및 설치][1] 페이지를 방문해서 Glide 앱 설정에 대해 배우시기 바랍니다. 그리고 [시작하기][2] 를 살펴보시면 기본 동작에 대해 배우실 수 있을 것 입니다. 더 많은 도움말과 예를 보려면 나머지 문서 섹션을 계속 진행하거나 많은 [샘플][3] 중 하나를 참조하시기 바랍니다.

### 요구사항

Glide v4 는 [Ice Cream Sandwich][4] (API level 14) 이상에서 동작 합니다.

[1]: doc/download-setup.html
[2]: doc/getting-started.html
[3]: ref/samples.html
[4]: https://developer.android.com/about/versions/android-4.0-highlights.html
