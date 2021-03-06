---
layout: page
title: "다운로드 및 설치"
category: doc
date: 2018/8/14 05:54
order: 1
disqus: 1
translators: [kofboy2000]
---

원문보기：[링크](http://bumptech.github.io/glide/doc/download-setup.html)

* TOC
{:toc}
### Android SDK 요구사항

**Min Sdk Version** - Glide v4 는 [Ice Cream Sandwich][10] (API level 14) 이상에서 동작 합니다.

**Compile Sdk Version** - Glide는 SDK 버전 **27(Oreo MR1)** 이상에서 컴파일해야 합니다.

**Support Library Version** - Glide 는 Support library 버전 **27** 을 사용 합니다.

다른 버전의 Support library 를 사용하려면 build.gradle 파일에서 `"com.android.support"`을 제외해야 합니다. 예를 들어 26 버전을 사용하시려면 아래를 참고하시기 바랍니다.

```groovy
dependencies {
  implementation ("com.github.bumptech.glide:glide:4.9.0") {
    exclude group: "com.android.support"
  }
  implementation "com.android.support:support-fragment:26.1.0"
}
```

Glide 가 참조하는 버전의 Support libarary 가 아닌 다른 버전의 Support library 를 사용하면 다음과 같은 Runtime Exception 이 발생할 수 있습니다.:

```
java.lang.NoSuchMethodError: No static method getFont(Landroid/content/Context;ILandroid/util/TypedValue;ILandroid/widget/TextView;)Landroid/graphics/Typeface; in class Landroid/support/v4/content/res/ResourcesCompat; or its super classes (declaration of 'android.support.v4.content.res.ResourcesCompat'
at android.support.v7.widget.TintTypedArray.getFont(TintTypedArray.java:119)
```

또한 Glide 의 API generator 에 문제가 발생하여 `GlideApp` 클래스가 생성되지 않을 수도 있습니다.
자세한 내용은 이슈 [#2730][8] 를 참조하시기 바랍니다.

### 다운로드

Glide 의 공개 버전은 여러 가지 방법으로 다운로드 받으실 수 있습니다.

#### Jar

GitHub 에서 [최신 jar 파일][1]을 다운로드 받으실 수 있습니다. Android [v4 Support library][2] 도 포함해야 되는 것을 잊지 마시기 바랍니다.

#### Gradle

Gradle 을 사용하는 경우 Maven Central 또는 JCenter 를 사용하여 Gradle dependency 를 추가할 수 있습니다. 이 경우에도 Support libarary 를 추가하셔야 합니다.

```groovy
repositories {
  mavenCentral()
  maven { url 'https://maven.google.com' }
}

dependencies {
    compile 'com.github.bumptech.glide:glide:4.9.0'
    // Skip this if you don't want to use integration libraries or configure Glide.
    annotationProcessor 'com.github.bumptech.glide:compiler:4.9.0'
}
```

주의：`@aar` 자체를 Gradle 에 추가하는 것은 피하시길 바랍니다. 만약 꼭 필요하다면 `transitive=true` 를 추가하시고 APK 필요한 모든 클래스들을 추가하시기 바랍니다.

```groovy
dependencies {
    implementation ("com.github.bumptech.glide:glide:4.9.0@aar") {
        transitive = true
    }
}
```
`@aar` 은 기본적으로 Gradle 의 dependency 를 제거하는 ["Artifact Only"][9] 표기법 입니다.

dependency 에 추가하지 않고 `@aar` 자체를 추가한 후 `transitive=true` 를 선언하지 않는다면 다음과 같은 Runtime exception 이 발생 할 수 있습니다.

```
java.lang.NoClassDefFoundError: com.bumptech.glide.load.resource.gif.GifBitmapProvider
    at com.bumptech.glide.load.resource.gif.ByteBufferGifDecoder.<init>(ByteBufferGifDecoder.java:68)
    at com.bumptech.glide.load.resource.gif.ByteBufferGifDecoder.<init>(ByteBufferGifDecoder.java:54)
    at com.bumptech.glide.Glide.<init>(Glide.java:327)
    at com.bumptech.glide.GlideBuilder.build(GlideBuilder.java:445)
    at com.bumptech.glide.Glide.initializeGlide(Glide.java:257)
    at com.bumptech.glide.Glide.initializeGlide(Glide.java:212)
    at com.bumptech.glide.Glide.checkAndInitializeGlide(Glide.java:176)
    at com.bumptech.glide.Glide.get(Glide.java:160)
    at com.bumptech.glide.Glide.getRetriever(Glide.java:612)
    at com.bumptech.glide.Glide.with(Glide.java:684)
```

#### Maven

Maven 을 사용하신다면 Glide 를 dependency 에 추가하여 사용 하실 수 있습니다. 이곳에서도, support libarary 추가를 잊지 마시기 바랍니다.

```xml
<dependency>
  <groupId>com.github.bumptech.glide</groupId>
  <artifactId>glide</artifactId>
  <version>4.8.0</version>
  <type>aar</type>
</dependency>
<dependency>
  <groupId>com.google.android</groupId>
  <artifactId>support-v4</artifactId>
  <version>r7</version>
</dependency>
<dependency>
  <groupId>com.github.bumptech.glide</groupId>
  <artifactId>compiler</artifactId>
  <version>4.8.0</version>
  <optional>true</optional>
</dependency>
```

### 설치

빌드 환경에 따라서 몇가지 추가 설정이 필요할 수도 있습니다.

#### 권한
사용하는 모든 데이터가 어플리케이션 내에 저장 되어 있을 경우 별도 권한은 필요 하지 않습니다. 즉, 대부분의 어플리케이션은 장치에서 이미지를 로드하거나((DCIM, Pictures 또는 SD 카드의 다른 곳) 인터넷에서 이미지를 로드 합니다. 따라서, 경우에 따라 아래에 나열된 권한 중 하나 이상을 포함하여야 합니다.

##### Internet
URL 또는 네트워크 연결을 통해 이미지를 로드하려는 경우 ``AndroidManifest.xml`` 에  ``INTERNET`` 및 ``ACCESS_NETWORK_STATE`` 권한을 추가해야 합니다.

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="your.package.name"

    <uses-permission android:name="android.permission.INTERNET"/>
    <!--
    Allows Glide to monitor connectivity status and restart failed requests if users go from a
    a disconnected to a connected network state.
    -->
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>

    <application>
      ...
    </application>
</manifest>
```

``ACCESS_NETWORK_STATE`` 는 Glide 가 URL 을 불러오는데 필요한 것은 아니지만 Glide 가 불안정한 네트워크 상태나 비행기 모드를 처리하는데 필요합니다. 자세한 내용은 아래의 연결 상태 확인 및 모니터링을 확인해 보시기 바랍니다.

##### 연결 상태 확인 및 모니터링
URL에서 이미지를 로드하는 경우, Glide 는 사용자의 연결 상태를 모니터링하고 사용자가 다시 연결할 때 실패한 요청을 재시작하여 네트워크 연결이 끊어지는 문제를 자동으로 해결하도록 도와줍니다. 어플리케이션에 ACCESS_NETWORK_STATE 가 선언 되어 있으면, Glide 는 연결 상태를 자동으로 모니터링하고 추가 작업은 필요하지 않습니다.

ConnectivityMonitor 로그 태그를 보시면 Glide가 네트워크 상태를 모니터링하는지 확인할 수 있습니다.

```
adb shell setprop log.tag.ConnectivityMonitor DEBUG
```

``ACCESS_NETWORK_STATE`` 권한을 추가한 경우 다음과 같이 logcat에 로그가 표시됩니다.

```
11-18 18:51:23.673 D/ConnectivityMonitor(16236): ACCESS_NETWORK_STATE permission granted, registering connectivity monitor
11-18 18:48:55.135 V/ConnectivityMonitor(15773): connectivity changed: false
11-18 18:49:00.701 V/ConnectivityMonitor(15773): connectivity changed: true
```

권한이 설정되지 않았다면, 아래와 같은 에러가 발생 하게 됩니다. ：

```
11-18 18:51:23.673 D/ConnectivityMonitor(16236): ACCESS_NETWORK_STATE permission missing, cannot register connectivity monitor
```

##### 저장 공간
DCIM 또는 Pictures 와 같은 로컬 폴더에서 이미지를 로드하려면 ``READ_EXTERNAL_STORAGE`` 권한을 추가해야 합니다.

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="your.package.name"

    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />

    <application>
      ...
    </application>
</manifest>
```

[``ExternalPreferredCacheDiskCacheFactory``][7] 를 사용하여 Glide 의 캐시를 저장 공간에 저장하려면, WRITE_EXTERNAL_STORAGE 이 필요합니다.

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="your.package.name"

    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />

    <application>
      ...
    </application>
</manifest>
```

#### Proguard

proguard 를 사용한다면 ``proguard.cfg`` 에 아래와 같이 추가해 줍니다.
```
-keep public class * implements com.bumptech.glide.module.GlideModule
-keep public class * extends com.bumptech.glide.module.AppGlideModule
-keep public enum com.bumptech.glide.load.ImageHeaderParser$** {
  **[] $VALUES;
  public *;
}
```

target API 레벨이 Android API 27 이하 버전일 경우 아래 라인도 추가해 줍니다.
```pro
-dontwarn com.bumptech.glide.load.resource.bitmap.VideoDecoder
```

VideoDecoder는 API 27 API를 사용하므로, 이전 버전의 Android 단말에서는 새로운 API를 호출하지 않더라도 proguard 경고를 발생시킬 수 있습니다.

DexGuard 를 사용할 경우 아래 라인도 추가해 줍니다.
```pro
# for DexGuard only
-keepresourcexmlelements manifest/application/meta-data@value=GlideModule
```

#### Jack

Glide 의 빌드 설정은 [Jack][3] 기능 중 현재 지원하지 않는 기능을 필요로 합니다. Jack 은 최근에 [deprecated][4] 되었고 Glide 에서 필요로 하는 기능이 추가될 것 같지는 않습니다. Java 8 으로 컴파일이 필요한 경우 아래를 참조 하시기 바랍니다.

#### Java 8
Android Gradle 플러그인의 Android Studio 3.0 및 버전 3.0부터 Java 8을 사용하여 프로젝트와 Glide를 컴파일할 수 있습니다. 자세한 내용은 [Use Java 8 LaAnguage Features][5] 를 참조하시기 바랍니다.

Glide itself does not use or require you to use Java 8 to compile or use Glide in your project. Glide will eventually require Java 8 to compile, but we will do our best to allow time for developers to update their applications first, so it’s likely that Java 8 won’t be a requirement for months or years (as of 11/2017).
Glide 자체가 Java 8 을 사용하지도 않았고, Glide 를 사용하기 위해 Java 8 으로 컴파일 되어야 하는 것도 아닙니다. Glide 가 결국 Java 8 으로 컴파일 되어야 하겠지만, 최대한 시간을 들여 개발자들이 본인들의 어플리케이션을 업데이트 시킬 수 있도록 할 것 입니다. 따라서 Java 8 이 향후 얼마 간은 필수 요구사항이 될 것 같지는 않습니다. (11/2017 기준)

#### Kotlin

Kotlin 으로 구현된 클래스에서 Glide의 annotation 을 사용하는 경우 annotationProcessor 대신 Glide 의 annotation 프로세서(anntation processor) 에 kapt dependency 를 포함해야 합니다.

```groovy
dependencies {
  kapt 'com.github.bumptech.glide:compiler:4.9.0'
}
```

`build.gradle` 에 `kotlin-kapt` 도 포함해야 합니다.

```groovy
apply plugin: 'kotlin-kapt'
```

자세한 내용은 [Generated API][6] 를 참조하시기 바랍니다.

[1]: https://github.com/bumptech/glide/releases/download/v3.6.0/glide-3.6.0.jar
[2]: http://developer.android.com/tools/support-library/features.html#v4
[3]: https://source.android.com/source/jack
[4]: https://android-developers.googleblog.com/2017/03/future-of-java-8-language-feature.html
[5]: https://developer.android.com/studio/write/java8-support.html
[6]: {{ site.baseurl }}/doc/generatedapi.html#kotlin
[7]: {{ site.baseurl }}/javadocs/431/com/bumptech/glide/load/engine/cache/ExternalPreferredCacheDiskCacheFactory.html
[8]: https://github.com/bumptech/glide/issues/2730
[9]: https://docs.gradle.org/current/userguide/dependency_management.html#ssub:artifact_dependencies
[10]: https://developer.android.com/about/versions/android-4.0-highlights.html
