---
layout: page
title: "시작하기"
category: doc
date: 2018-08-14 00:50:00
order: 2
disqus: 1
translators: [kofboy2000]
---

원문보기：[링크](http://bumptech.github.io/glide/doc/getting-started.html)

* TOC
{:toc}

### 기본 사용법

Glide 이미지 로딩은 쉽고, 대개 한줄로 가능 합니다.

```java
Glide.with(fragment)
    .load(myUrl)
    .into(imageView);
```

더이상 사용하지 않는 이미지 로딩의 취소도 매우 간단합니다.

```java
Glide.with(fragment).clear(imageView);
```

더 이상 필요하지 않은 로딩를 제거하는 것도 좋지만, 꼭 그렇게 할 필요는 없습니다. [``Glide.with()``][1] 로 전달 된 Activity 나 Fragment 가 종료 될 때 Glide 는 자동으로 사용한 자원을 재활용하거나 클리어시키고 있습니다.

### Customizing requests.

Glide 는 변환, 트랜지션(transition), 캐싱(caching) 옵션과 같은 다양한 옵션을 제공하고 있습니다.

기본 옵션은 아래의 예와 같이 바로 적용이 가능 합니다.

```java
Glide.with(fragment)
  .load(myUrl)
  .placeholder(placeholder)
  .fitCenter()
  .into(imageView);
```

`RequestOptions` 를 사용하면 정의한 옵션을 개별 요청(request) 마다 공유해서 사용 할 수 있습니다.

```java
RequestOptions sharedOptions =
    new RequestOptions()
      .placeholder(placeholder)
      .fitCenter();

Glide.with(fragment)
  .load(myUrl)
  .apply(sharedOptions)
  .into(imageView1);

Glide.with(fragment)
  .load(myUrl)
  .apply(sharedOptions)
  .into(imageView2);
```

[generated API][7] 를 사용하면 사용자 지정 옵션을 더 추가하거나 확장 시킬 수 있습니다.

### ListView 와 RecyclerView

ListView 와 RecyclerView 에서의 이미지 로딩도 일반적인 View 에서의 로딩과 동일한 코드를 사용 합니다. Glide 가 자동적으로 View 의 재사용및 취소 요청을 처리 합니다.

```java
@Override
public void onBindViewHolder(ViewHolder holder, int position) {
    String url = urls.get(position);
    Glide.with(fragment)
        .load(url)
        .into(holder.imageView);
}
```

url 에 대한 null 체크도 필요하지 않습니다. Glide 가 View 를 클리어 시키거나, url 이 null 일 때 사용토록 사용자가 정의한 [placeholder Drawable][2] 나 [fallback Drawable][3] 을 설정해 주게 됩니다.

유일하게 해주어야 할 것은 재사용 가능한 ``View`` 나 [``Target``][5] 의 기존에 있던 내용들을 다시 한번 불러오거나 [``clear()``][4] API 로 명시적으로 클리어 해주어야 한 다는 것 입니다.

```java
@Override
public void onBindViewHolder(ViewHolder holder, int position) {
    if (isImagePosition(position)) {
        String url = urls.get(position);
        Glide.with(fragment)
            .load(url)
            .into(holder.imageView);
    } else {
        Glide.with(fragment).clear(holder.imageView);
        holder.imageView.setImageDrawable(specialDrawable);
    }
}
```

``View`` 에 [``clear()``][4] 나 ``into(View)`` 를 호출함으로써 이전 로드를 취소하고 Glide 호출이 완료된 후 View 의 내용이 변경되지 않도록 보장할 수 있습니다. 만약 [``clear()``][4] 을 하지 않고 새로운 로드를 시작할 경우, 이전 포지션의 뷰에서 시작된 로드는 ``Drawable`` 이 적용된 이후에 완료되며 ``View`` 가 예전 이미지로 변경 될 수 있습니다.

여기서 보신 예제는 RecycleClerView에 대한 것이지만 ListView에도 동일하게 적용 됩니다.

### View 가 아닌 타겟 (Non-View Targets)

``Bitmap`` 이나 ``Drawable`` 를 ``View`` 에 불러오는 것 외에도 사용자 지정 ``Target`` 에 의 비동기 로드를 시작 할 수 있습니다.

```java
Glide.with(context
  .load(url)
  .into(new SimpleTarget<Drawable>() {
    @Override
    public void onResourceReady(Drawable resource, Transition<Drawable> transition) {
      // Do something with the Drawable here.
    }
  });
```

사용자 지정 ``Target`` 를 사용할 때 몇 가지 알아야 할 것들이 있습니다. [Targets][9] 문서 페이지 에서 자세한 내용 확인해 보시기 바랍니다.

### 백그라운드 쓰레드

백그라운드 쓰레드에서의 이미지 로딩도 [``submit(int, int)``][8] 를 사용하면 아주 간단 합니다.

```java
FutureTarget<Bitmap> futureTarget =
  Glide.with(context)
    .asBitmap()
    .load(url)
    .submit(width, height);

Bitmap bitmap = futureTarget.get();

// Do something with the Bitmap and then when you're done with it:
Glide.with(context).clear(futureTarget);
```

``Bitmap`` 이나``Drawable`` 이 백그라운드 쓰레드 자체에 필요하지 않다면 백그라운드 쓰레드에서도 포어그라운드(foreground) 쓰레드에서 하던 것과 마찮가지로 비동기 로드를 할 수 있습니다.

```java
Glide.with(context)
  .asBitmap()
  .load(url)
  .into(new Target<Bitmap>() {
    ...
  });
```

[1]: {{ site.baseurl }}/javadocs/400/com/bumptech/glide/Glide.html#with-android.app.Fragment-
[2]: {{ site.baseurl }}/javadocs/400/com/bumptech/glide/request/RequestOptions.html#placeholder-int-
[3]: {{ site.baseurl }}/javadocs/400/com/bumptech/glide/request/RequestOptions.html#fallback-int-
[4]: {{ site.baseurl }}/javadocs/400/com/bumptech/glide/RequestManager.html#clear-com.bumptech.glide.request.target.Target-
[5]: {{ site.baseurl }}/javadocs/400/com/bumptech/glide/request/target/Target.html
[6]: {{ site.baseurl }}/javadocs/400/com/bumptech/glide/module/AppGlideModule.html
[7]: {{ site.baseurl }}/doc/generatedapi.html
[8]: {{ site.baseurl }}/javadocs/431/com/bumptech/glide/RequestBuilder.html#submit-int-int-
[9]: {{ site.baseurl }}/doc/targets.html
