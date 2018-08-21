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

1. Glide 의 어노테이션 프로세서(annotation processor)를 추가하여야 합니다.：

   ```groovy
   repositories {
     mavenCentral()
   }

   dependencies {
     annotationProcessor 'com.github.bumptech.glide:compiler:4.7.1'
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

만약 Kotlin 을 사용하신다면 아래를 참조해주시기 바랍니다.

1. 위 Java 섹션에서 언급한 Glide 모듈을 모두 포함하여 주시기 바랍니다. ([``AppGlideModule``][4], [``LibraryGlideModule``][13], [``GlideExtension``][6] ).

2. 使用 Kotlin 实现注解类，但需要添加一个 ``kapt`` 依赖以替换 Glide 的``annotationProcessor`` 依赖：

   ```groovy
   dependencies {
     kapt 'com.github.bumptech.glide:compiler:4.7.1'
   }
   ```
   注意，你还需要在你的 ``build.gradle`` 文件中包含 ``kotlin-kapt`` 插件：

   ```groovy
   apply plugin: 'kotlin-kapt'
   ```

    此外，如果你有其他的注解处理器，它们都必须全部被从 ``annotationProcessor`` 转换为 ``kapt``：

   ```groovy
   dependencies {
     kapt "android.arch.lifecycle:compiler:1.0.0"
     kapt 'com.github.bumptech.glide:compiler:4.7.1'
   }
   ```

   关于``kapt``的使用，请查看[官方文档][14]。

#### Android Studio

Android Studio 在大多数时候都可以正确地处理注解处理器 (annotation processor) 和 generated API。然而，当你第一次添加你的 ``AppGlideModule`` 或做了某些类型的修改后，你可能需要重新构建 (rebuild) 你的项目。 无论何时，如果你发现 API 没有被 import ，或看起来已经过期，你可以通过以下方法重新构建：
1. 打开 Build 菜单；
2. 点击 Rebuild Project。

### 使用 Generated API

Generated API 默认名为 `GlideApp` ，与 Application 模块中 [`AppGlideModule`][4]的子类包名相同。在 Application 模块中将 `Glide.with()` 替换为 `GlideApp.with()`，即可使用该 API 去完成加载工作：

```java
GlideApp.with(fragment)
   .load(myUrl)
   .placeholder(R.drawable.placeholder)
   .fitCenter()
   .into(imageView);
```

与 ``Glide.with()`` 不同，诸如  ``fitCenter()`` 和 ``placeholder()`` 等选项在 Builder 中直接可用，并不需要再传入单独的 [``RequestOptions``][3] 对象。
​    
### GlideExtension

Glide Generated API 可在 Application 和 Library 中被扩展。扩展使用被注解的静态方法来添加新的选项、修改现有选项、甚至添加额外的类型支持。

[``@GlideExtension``][6] 注解用于标识一个扩展 Glide API 的类。任何扩展 Glide API 的类都必须使用这个注解来标记，否则其中被注解的方法就会被忽略。

被 [``@GlideExtension``][6] 注解的类应以工具类的思维编写。这种类应该有一个私有的、空的构造方法，应为 final 类型，并且仅包含静态方法。被注解的类可以含有静态变量，可以引用其他的类或对象。

在 Application 模块中可以根据需求实现任意多个被 [``@GlideExtension``][6] 注解的类，在 Library 模块中同样如此。当 [``AppGlideModule``][4] 被发现时，所有有效的 [Glide 扩展类][6] 会被合并，所有的选项在 API 中均可以被调用。合并冲突会导致 Glide 的 Annotation Processor 抛出编译错误。

被 `@GlideExtention` 注解的类有两种扩展方式：

1. [``GlideOption``][7]  - 为 [``RequestOptions``][3] 添加一个自定义的选项。
2. [``GlideType``][8] - 添加对新的资源类型的支持(GIF，SVG 等等)。


#### GlideOption

用 [``@GlideOption``][7] 注解的静态方法用于扩展 [``RequestOptions``][3] 。``GlideOption`` 可以：

1. 定义一个在 Application 模块中频繁使用的选项集合。
2. 创建新的选项，通常与 Glide 的 [``Option``][10] 类一起使用。

要定义一个选项集合，你可以这么写：

```java
@GlideExtension
public class MyAppExtension {
  // Size of mini thumb in pixels.
  private static final int MINI_THUMB_SIZE = 100;

  private MyAppExtension() { } // utility class

  @GlideOption
  public static void miniThumb(RequestOptions options) {
    options
      .fitCenter()
      .override(MINI_THUMB_SIZE);
  }
```

这将会在 [``RequestOptions``][3] 的子类中生成一个方法，类似这样：

```java
public class GlideOptions extends RequestOptions {

  public GlideOptions miniThumb() {
    MyAppExtension.miniThumb(this);
  }

  ...
}
```

你可以为方法任意添加参数，但要保证第一个参数为 [``RequestOptions``][9]。

```java
@GlideOption
public static void miniThumb(RequestOptions options, int size) {
  options
    .fitCenter()
    .override(size);
}
```

在自动生成的方法中新添的参数同样被加了进来：

```java
public GlideOptions miniThumb(int size) {
  MyAppExtension.miniThumb(this);
}
```

之后你就可以使用生成的 ``GlideApp`` 类调用你的自定义方法：

```java
GlideApp.with(fragment)
   .load(url)
   .miniThumb(thumbnailSize)
   .into(imageView);
```

使用 ``@GlideOption`` 标记的方法应该为静态方法，并且返回值为空。请注意，这些生成的方法在一般的 ``Glide`` 和 ``RequestOptions`` 类里不可用。

#### GlideType

被 [``@GlideType``][8] 注解的静态方法用于扩展 [``RequestManager``][11] 。被 ``@GlideType`` 注解的方法允许你添加对新的资源类型的支持，包括指定默认选项。

例如，为添加对 GIF 的支持，你可以添加一个被 ``@GlideType`` 注解的方法：

```java
@GlideExtension
public class MyAppExtension {
  private static final RequestOptions DECODE_TYPE_GIF = decodeTypeOf(GifDrawable.class).lock();

  @GlideType(GifDrawable.class)
  public static void asGif(RequestBuilder<GifDrawable> requestBuilder) {
    requestBuilder
      .transition(new DrawableTransitionOptions())
      .apply(DECODE_TYPE_GIF);
  }
}
```

这样会生成一个包含对应方法的 [``RequestManager``][11] ：

```java
public class GlideRequests extends RequesetManager {

  public RequestBuilder<GifDrawable> asGif() {
    RequestBuilder<GifDrawable> builder = as(GifDrawable.class);
    MyAppExtension.asGif(builder);
    return builder;
  }

  ...
}
```

之后你可以使用生成的 ``GlideApp`` 类调用你的自定义类型：

```java
GlideApp.with(fragment)
  .asGif()
  .load(url)
  .into(imageView);
```

被 ``@GlideType`` 标记的方法必须使用 [``RequestBuilder<T>``][2] 作为其第一个参数，这里的泛型 ``<T>`` 对应 [``@GlideType``][8] 注解中传入的类。该方法应为静态方法，且返回值为空。方法必须定义在一个被 [``@GlideExtension``][6] 注解标记的类中。


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
