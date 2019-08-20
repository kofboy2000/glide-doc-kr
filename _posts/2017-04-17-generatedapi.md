---
layout: page
title: "Generated API"
category: doc
date: 2018-08-15 16:19:51
order: 3
disqus: 1
translators: [kofboy2000]
---

원문보기：[링크](http://bumptech.github.io/glide/doc/generatedapi.html)

* TOC
{:toc}

### 소개

Glide v4 는 [어노테이션 프로세서 (Annotation Processor)][1] 를 사용하여 Application 이 [``RequestBuilder``][2], [``RequestOptions``][3], 그리고 통합 라이브러리의 모든 옵션에 접근할 수 있는 API 를 만들수 있습니다.

Generated API 는 두 가지 용도로 사용 됩니다.
1. Integration libraries can extend Glide’s API with custom options.
2. Applications can extend Glide’s API by adding methods that bundle commonly used options.

상기 두 가지 경우 모두 [``RequestOptions``][3] 상속 받는 클래스를 직접 만들어 할 수도 있으나, 그럴 경우 상대적으로 더 많은 수고를 들이게 되고 덜 유려한 API 를 생성하게 됩니다.

### 시작하기

#### 유용성

Generated API 는 현재 Application 에서만 가능 합니다. Generated API 를 Application 에 제한 함으로써 N 개의 구현이 아닌, 하나의 구현만 가질수 있도록 하였고, 이로 인해 하나의 Application 에 하나의 라이브러리만 존재할 수 있도록 하였습니다. 결과적으로, import 를 관리하기 간단하고 모든 콜 패쓰(call paths) 에서 정확한 옵션을 적용 받을 수 있게 하였습니다. 이러한 제약은 (실험적으로든 다른 방법으로든) 향후 버전에서 해제될 수도 있습니다.

#### Java

Application 에서 Generated API 를 사용하려면 두 단계를 수행하셔야 합니다.

1. Glide 의 어노테이션 프로세서(annotation processor)를 추가하여야 합니다.

   ```groovy
   repositories {
     mavenCentral()
   }

   dependencies {
     annotationProcessor 'com.github.bumptech.glide:compiler:4.8.0'
   }
   ```

   자세한 내용은 [다운로드 및 설치][12] 를 참조하시기 바랍니다.

2. [``AppGlideModule``][4] 를 구현하여 application 에 포함시켜 줍니다.

   ```java
   package com.example.myapp;

   import com.bumptech.glide.annotation.GlideModule;
   import com.bumptech.glide.module.AppGlideModule;

   @GlideModule
   public final class MyAppGlideModule extends AppGlideModule {}
   ```

geneated API 를 사용하기 위해 꼭 특정 함수를 구현해야 하지 않으며,  `AppGlideModule` 를 상속 받고, `@GlideModule` 어노테이션이 있다면 `AppGlideModule` 를 빈 클래스로 두셔도 괜찮습니다.

[``AppGlideModule``][4] 은 반드시 [``@GlideModule``][5] 어노테이션이 있어야 합니다. 그렇지 않을 경우, `Glide` 가 module 을 찾을 수 없으며 로그에서 ``Glide`` 로 tag 된, module 을 찾을 수 없다는 경고를 보게 될 것 입니다.

**주의：** 라이브러리는 [`AppGlideModule`][4] 를 가져서는 **안됩니다.** 자세한 내용은 [환경 설정][15] 페이지를 확인하시기 바랍니다.

#### Kotlin

만약 Kotlin 을 사용하신다면,

1. 위 Java 섹션에서 언급한 Glide 모듈을 모두 포함하여 줍니다. ([``AppGlideModule``][4], [``LibraryGlideModule``][13], [``GlideExtension``][6] ).

2. Glide 의 annotation processor 는 ``annotationProcessor`` 대신 ``kapt`` 를 사용 합니다.

   ```groovy
   dependencies {
     kapt 'com.github.bumptech.glide:compiler:4.8.0'
   }
   ```
  ``build.gradle`` 파일에 ``kotlin-kapt`` 추가 하는 것을 잊지 마시기 바랍니다.

   ```groovy
   apply plugin: 'kotlin-kapt'
   ```

    추가로, 다른 annotation processor가 있다면 이들을 모두 ``annotationProcessor`` 에서 ``kapt``로 변경 합니다.

   ```groovy
   dependencies {
     kapt "android.arch.lifecycle:compiler:1.0.0"
     kapt 'com.github.bumptech.glide:compiler:4.8.0'
   }
   ```

   ``kapt`` 에 더 알고 싶으신 분은 [공식 문서][14] 를 확인하시기 바랍니다.

#### Android Studio

Android Studio 에서는 어노테이션 프로세스 (annotation processor) 와 generated API 를 사용하시는데 별 무리 없이 동작할 것 입니다. 다만, ``AppGlideModule`` 를 처음 추가하거나 Glide 에 관련된 수정이 있다면 프로젝트를 한번은 재빌드(rebuild) 하셔야 할 수도 있습니다. 만약 API 가 import 되지 않거나 동작이 잘 안될 경우 다음과 같이 rebuild 를 수행 합니다.
1. Build 메뉴를 열고.
2. Rebuild Project 를 클릭 합니다.

### Generated API 사용

Generated API 는 `GlideApp` 이라는 이름을 기본으로 가지고, Application 에서 제공하는 [`AppGlideModule`][4] 구현과 동일한 패키지에서 제공 됩니다. 해당 API 는 `Glide.with()` 대신에 `GlideApp.with()` 로 호출하여 사용 할 수 있습니다.

```java
GlideApp.with(fragment)
   .load(myUrl)
   .placeholder(R.drawable.placeholder)
   .fitCenter()
   .into(imageView);
```

``Glide.with()`` 와는 다르게 ``fitCenter()`` 나 ``placeholder()`` 과 같은 옵션들을 Builder 를 통해 바로 호출할 수 있고, [``RequestOptions``][3] 객체를 별도로 사용할 필요도 없습니다.
​    
### GlideExtension

Glide Generated API 는 Application 과 Library 에서 상속 받을 수 있습니다. annotation 을 이용하여 새로운 옵션을 만들거나, 기존의 옵션을 수정하거나 혹은 새로운 타입을 만들 수 있습니다.

[``@GlideExtension``][6] 로 Glide API 를 확장하는 클래스 임을 명시 하며, Glide API 를 상속 받는 모든 클래스에는 해당 어노테이션을 사용하여야 합니다.

[``@GlideExtension``][6] annotation 이 달린 클래스는 utility 클래스여야 합니다. private 의 빈 생성자와 final 의 static 메소드만 가질 수 있습니다. 또한 static 변수를 가지며, 다른 클래스나 오브젝트를 참조할 수 있습니다.

Application 이나 라이브러리는 [``@GlideExtension``][6] 클래스를 원하는 만큼 구현할 수 있습니다. [``AppGlideModule``][4] 이 있는 경우 구현된 모든 [``@GlideExtension``][6] 는 하나의 API 로 통합(merge)되며, 만약 Conflict 가 날 경우 Glide 의 annotation processor 에 의해 컴파일 에러가 발생하게 됩니다.

`@GlideExtention` 는 두가지 타입의 메소드를 정의 할 수 있습니다.

1. [``GlideOption``][7]  - [``RequestOptions``][3] 에 커스텀 옵션을 추가할 수 있습니다.
2. [``GlideType``][8] - 새로운 리소스 타입을 추가 할 수 있습니다. (GIF, SVG 등)


#### GlideOption

[``@GlideOption``][7] 은 [``RequestOptions``][3] 를 상속 받은 메소드임을 나타내 줍니다. ``GlideOption`` 은 다음과 같은 경우 유용하게 사용 할 수 있습니다.

1. Application 전체에서 자주 사용되는 옵션 그룹을 정의 할 수 있습니다.
2. 새로운 옵션을 추가 할 수 있습니다. 주로 Glide 의 [``Option``][10] 과 함께 사용 됩니다.

옵션 그룹을 정의하기 위해서는 아래와 같이 사용할 수 있습니다.

```java
@GlideExtension
public class MyAppExtension {
  // Size of mini thumb in pixels.
  private static final int MINI_THUMB_SIZE = 100;

  private MyAppExtension() { } // utility class

  @NonNull
  @GlideOption
  public static BaseRequestOptions<?> miniThumb(BaseRequestOptions<?> options) {
      return options
      .fitCenter()
      .override(MINI_THUMB_SIZE);
  }
```

상기 코드는 [``RequestOptions``][3] 의 서브 클래스와 메소드를 다음과 같이 생성하게 됩니다.

```java
public class GlideOptions extends RequestOptions {

  public GlideOptions miniThumb() {
    return (GlideOptions) MyAppExtension.miniThumb(this);
  }

  ...
}
```

첫번째 인수를 [``RequestOptions``][9] 로 정의해 주기만 한다면, 원하는 만큼 인수들을 추가 할 수 있습니다.

```java
@GlideOption
public static BaseRequestOptions<?> miniThumb(BaseRequestOptions<?> options, int size) {
  return options
    .fitCenter()
    .override(size);
}
```

위의 코드는 새로 생성된 메소드에 다음과 같이 추가 되게 됩니다.

```java
public GlideOptions miniThumb(int size) {
  return (GlideOptions) MyAppExtension.miniThumb(this);
}
```

``GlideApp`` 을 이용하여 새롭게 생성된 커스텀 메소드를 호출 할 수 있습니다.

```java
GlideApp.with(fragment)
   .load(url)
   .miniThumb(thumbnailSize)
   .into(imageView);
```

``@GlideOption`` 이 붙은 메소드는 static 함수이며, `BaseRequestOptions<?>` 를 리턴하여야 합니다. 또한, 이렇게 생성된 API 들은  ``Glide`` 나 ``RequestOptions`` 클래스에서는 사용할 수 없습니다.

#### GlideType

[``@GlideType``][8] 어노테이션이 붙은 메소드는 [``RequestManager``][11] 를 상속 받게 되어 있습니다. ``@GlideType`` 는 특정 디폴트 옵션들과 함께 새로운 타입을 정의할 수 있게 해줍니다.

예르 들어, GIF 를 지원하고 싶을 경우, ``@GlideType`` 를 다음과 같이 추가해 줍니다.

```java
@GlideExtension
public class MyAppExtension {
  private static final RequestOptions DECODE_TYPE_GIF = decodeTypeOf(GifDrawable.class).lock();

  @NonNull
  @GlideType(GifDrawable.class)
  public static RequestBuilder<GifDrwable> asGif(RequestBuilder<GifDrawable> requestBuilder) {
      return requestBuilder
      .transition(new DrawableTransitionOptions())
      .apply(DECODE_TYPE_GIF);
  }
}
```

이럴 경우 [``RequestManager``][11] 에 다음과 같은 메소드가 생성되게 됩니다.

```java
public class GlideRequests extends RequesetManager {

  public GlideRequest<GifDrawable> asGif() {
      return (GlideRequest<GifDrawable> MyAppExtension.asGif(this.as(GifDrawable.class));
  }

  ...
}
```

``GlideApp`` 를 사용하여 다음과 같이 새로운 타입을 사용할 수 있습니다.

```java
GlideApp.with(fragment)
  .asGif()
  .load(url)
  .into(imageView);
```

``@GlideType``의 annotation 이 표기된 메소드는 [``RequestBuilder<T>``][2] 를 첫번째 파라미터로 가져야 하며 이떄, ``<T>``의 값은 [``@GlideType``][8] annotation 에서 정의한 클래스 여야 합니다. 메소드는 [``@GlideExtension``][6] 이 명시된 클래스 내에 있어야 하며, static 으로 선언되어야 하며, `RequestBuilder<T>` 를 리턴 값으로 가져야 합니다.


[1]: https://docs.oracle.com/javase/8/docs/api/javax/annotation/processing/Processor.html
[2]: {{ site.baseurl }}/javadocs/400/com/bumptech/glide/RequestBuilder.html
[3]: {{ site.baseurl }}/javadocs/400/com/bumptech/glide/request/RequestOptions.html
[4]: {{ site.baseurl }}/javadocs/400/com/bumptech/glide/module/AppGlideModule.html
[5]: {{ site.baseurl }}/javadocs/400/com/bumptech/glide/annotation/GlideModule.html
[6]: {{ site.baseurl }}/javadocs/400/com/bumptech/glide/annotation/GlideExtension.html
[7]: {{ site.baseurl }}/javadocs/400/com/bumptech/glide/annotation/GlideOption.html
[8]: {{ site.baseurl }}/javadocs/400/com/bumptech/glide/annotation/GlideType.html
[9]: {{ site.baseurl }}/javadocs/400/com/bumptech/glide/request/RequestOptions.html
[10]: {{ site.baseurl }}/javadocs/400/com/bumptech/glide/load/Option.html
[11]: {{ site.baseurl }}/javadocs/400/com/bumptech/glide/RequestManager.html
[12]: {{ site.baseurl }}/doc/download-setup.html
[13]: {{ site.baseurl }}/javadocs/400/com/bumptech/glide/module/LibraryGlideModule.html
[14]: https://kotlinlang.org/docs/reference/kapt.html
[15]: {{ site.baseurl }}/doc/configuration.html#avoid-appglidemodule-in-libraries
