---
layout: page
title: "Placeholders"
category: doc
date: 2015-05-19 07:14:23
order: 4
disqus: 1
---

원문보기：[링크](http://bumptech.github.io/glide/doc/placeholders.html)

* TOC
{:toc}

### 타입
Glide 는 각기 다른 상황에서 사용되는 3종류의 다른 Placeholder 를 제공 합니다.

* [placeholder][1]
* [error][2]
* [fallback][3]

#### Placeholder

Placeholder 는 요청이 진행 중일 때 나타나는 Drawable 입니다. 요청이 성공적으로  이뤄지면 Placeholder 는 요청된 리소스로 교체 되게 됩니다. 만약에 요청된 리소스가 메모리에서 부터 온다면, Placeholder 가 나타날 일은 없으나,  `error Drawable` 이 별도로 없는 상태에서 요청이 실패할 경우 Placeholder 는 계속 보여지게 될 것 입니다.비슷한 상황으로, url/model 이 ``null`` 일 때 `error Drawable` 이나 `fallback` 이 없을 경우 Placeholder 가 계속 보여질 것 입니다.

[generated API][4] 에서는 아래와 같이 사용되거나,

```java
GlideApp.with(fragment)
  .load(url)
  .placeholder(R.drawable.placeholder)
  .into(view);
```

또는 다음과 같이 사용 될 수 있습니다.

```java
GlideApp.with(fragment)
  .load(url)
  .placeholder(new ColorDrawable(Color.BLACK))
  .into(view);
```

#### Error

`error Drawable` 은 요청이 최종적으로 실패할 경우 나타나게 됩니다. `error Drawable` 은 url/model 이 ``null`` 이거나 `fallback Drawable` 이 별도로 설정 되어 있지 않은 경우 나타기도 합니다.

 [generated API][4] 에서는 아래와 같이 사용되거나,

```java
GlideApp.with(fragment)
  .load(url)
  .error(R.drawable.error)
  .into(view);
```

또는 다음과 같이 사용 될 수 있습니다.

```java
GlideApp.with(fragment)
  .load(url)
  .error(new ColorDrawable(Color.RED))
  .into(view);
```

#### Fallback
`fallback Drawable` 은 요청한 url/model 이 ``null`` 일 경우 나타납니다. `fallback Drawable` 의 주요 목적은 사용자로 하여금 ``null`` 이 예상되는지 여부를 표시할 수 있도록 하는 것 입니다. 예를 들어, 프로필 사진이 ``null`` url 인 경우 이를 아직 프로필 사진이 설정되지 않는 경우로 보고 기본 이미지를 사용해야 될 수 있습니다. 그런데 ``null`` 이 meta-data 를 잘못 설정된 상태이거나 해당 url 을 검색할 수 없는 상황일 수도 있습니다. 기본적으로 Glide 는 ``null`` url/model 을 error 로 취급합니다. 따라서 ``null`` 에 대한 예측 상황이 따로 있는 경우 `fallback Drawable` 을 설정해 주어야 합니다.

 [generated API][4] 에서는 아래와 같이 사용되거나,

```java
GlideApp.with(fragment)
  .load(url)
  .fallback(R.drawable.fallback)
  .into(view);
```

또는 다음과 같이 사용 될 수 있습니다.

```java
GlideApp.with(fragment)
  .load(url)
  .fallback(new ColorDrawable(Color.GREY))
  .into(view);
```

### FAQ

##### placeholder 비동기적으로 로드 되나요？
아닙니다. placeholders 는 Main Thread 상에서 Android Resources 를 가져옵니다. Placeholder 는 대개 작거나 시스템 캐시화 되기 쉬운 것으로 설정하는 것이 좋습니다.

##### Transformation 이 placeholder 에도 적용 되나요?
아닙니다. Transformation 은 요청된 리소스에만 적용되며, placeholder 에는 적용되지 않습니다. 런타임에 Transform 해야 되는 리소스를 어플리케이션 내에 가지고 있는 것은 비효율적 입니다. 거의 모든 경우, 꼭 필요로 하는 크기와 형태의 리소르만 포함하는 것이 더 효율적 입니다. 만약 원형의 이미지를 로딩해야 한다면, 원형의 placeholder 를 가져야 할 것 입니다. 또는, Transform 과 같은 방식으로 placeholder 를 자르는 커스텀 뷰를 고려할 수도 있습니다.

##### 여러개의 View 에서 같은 Drawable 을 placeholder 로 사용해도 되나요?
보통은 그렇지만 항상 그렇지는 않습니다. `BitmapDrawable`과 같은 무상태(`non-stateful`) 적 Drawable 은 다수의 View 에서 동시에 사용되도 괜찮지만, 상태를 가지는(`stateful`) Drawable 의 경우 다수의 View 에서 동시에 보여지도록 하는 것은, 다수의 View 가 상태를 변형 (`mutate`) 시킬수 있기에 안전하지 않습니다. 상태를 가지는(`stateful`) Drawable 을 사용할 때는 리소스 id 를 넘기거나 `newDrawable()` 를 사용하여 요청별 새로운 카피를 만드는 것이 좋습니다.

[1]: {{ site.baseurl }}/javadocs/400/com/bumptech/glide/request/RequestOptions.html#placeholder-int-
[2]: {{ site.baseurl }}/javadocs/400/com/bumptech/glide/request/RequestOptions.html#error-int-
[3]: {{ site.baseurl }}/javadocs/400/com/bumptech/glide/request/RequestOptions.html#fallback-int-
[4]: generatedapi.html
