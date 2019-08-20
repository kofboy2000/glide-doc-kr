---
layout: page
title: "Transformations"
category: doc
date: 2015-05-21 20:04:53
order: 6
disqus: 1
---

원문보기：[링크](http://bumptech.github.io/glide/doc/transformations.html)

* TOC
{:toc}

### Transformations 란

Glide 의 [Transformations][1] 는 리소스를 가져와 변형 한 다음, 변형 된 리소스를 리턴 합니다. 일반적으로 transformations 는 이미지를 자르거나 비트맵에 필터를 적용하는데 사용되나, 움직이는 GIF 나 사용자 정의 커스텀 리소스 타입을 변환하는데 사용 할 수도 있습니다.

### 기본 제공 타입

Glide 에는 다음과 같이 몇가지 기본 transformations 을 포함하고 있습니다. ：

* [CenterCrop][4]
* [FitCenter][2]
* [CircleCrop][6]

### 적용

[RequestOptions][9] 를 사용해서 transformations 을 적용 할 수 있습니다. ：

#### 디폴트 Transformations

```java
Glide.with(fragment)
  .load(url)
  .fitCenter()
  .into(imageView);
```

혹은 `RequestOptions` 을 사용하는 경우,

```java
RequestOptions options = new RequestOptions();
options.centerCrop();

Glide.with(fragment)
    .load(url)
    .apply(options)
    .into(imageView);
```

RequestOption 사용에 대한 자세한 내용은 [Options][3] 페이지를 참조하시기 바랍니다.

```java
import static com.bumptech.glide.request.RequestOptions.fitCenterTransform;

Glide.with(fragment)
    .load(url)
    .apply(fitCenterTransform())
    .into(imageView);
```

만약 [Generated API][16] 를 사용한다면 transfomrations 는 inline 되어 있기 때문에 더 사용하기 쉽습니다.

```java
GlideApp.with(fragment)
  .load(url)
  .fitCenter()
  .into(imageView);
```

 `RequestOption` 에 대해 더 알고 싶으시다면 [Options][3] 페이지를 참고 하세요.

#### 다중 Transformations

기본적으로 [``transform()``][17] 을 하나의 로드에 여러개를 하나씩 적용하게 된다면 (``fitCenter()``, ``centerCrop()``, ``bitmapTransform()`` etc) 이전의 적용된 것을 덮어쓰게 됩니다.

따라서 하나의 로드에 여러개의 Transformation 을 사용하기 보다는 [``MultiTransformation``][18] 나 숏컷 메소드인 [``.transforms()``][19] 메소를 사용하는 것이 좋습니다.

[generated API][16] 에서 사용할 경우 :

```java
Glide.with(fragment)
  .load(url)
  .transform(new MultiTransformation(new FitCenter(), new YourCustomTransformation())
  .into(imageView);
```

[generated API][16] 의 숏컷 메소드를 사용 하는 경우 ：

```java
GlideApp.with(fragment)
  .load(url)
  .transforms(new FitCenter(), new YourCustomTransformation())
  .into(imageView);
```

[``MultiTransformation``][18] 의 생성자에 전달하는 Transformations 의 순서대로 각각의 Transformations 이 순차적으로 적용 됩니다.

### 사용자 정의 Transformations

비록 Glide 에서 다양한 기본 [``Transformation``][1] 를 제공하고즌 있지만 경우에 따라 추가 기능을 위해 사용자 정의의 [``Transformation``][2] 가 필요한 경우도 있을 것 입니다.

#### BitmapTransformation

``Bitmap`` 만 변환하고 싶다면 [``BitmapTransformation``][20] 클래스를 확장하는 것을 추천 드립니다. ``BitmapTransformation`` 은 수정된 Bitmap 을 반환하고 난 후 원래의 Bitmap 을 재활용 하는 등 몇가지 기본적인 사항을 처리 합니다.

간단하게 구현된 모습은 아래와 같습니다. ：

```java
public class FillSpace extends BitmapTransformation {
    private static final String ID = "com.bumptech.glide.transformations.FillSpace";
    private static final String ID_BYTES = ID.getBytes(STRING_CHARSET_NAME);

    @Override
    public Bitmap transform(BitmapPool pool, Bitmap toTransform, int outWidth, int outHeight) {
        if (toTransform.getWidth() == outWidth && toTransform.getHeight() == outHeight) {
            return toTransform;
        }

        return Bitmap.createScaledBitmap(toTransform, outWidth, outHeight, /*filter=*/ true);
    }

    @Override
    public void equals(Object o) {
      return o instanceof FillSpace;
    }

    @Override
    public int hashCode() {
      return ID.hashCode();
    }

    @Override
    public void updateDiskCacheKey(MessageDigest messageDigest)
        throws UnsupportedEncodingException {
      messageDigest.update(ID_BYTES);
    }
}
```

여러분이 작성할 ``Transformation`` 이 위의 예제보다 분명 더 복잡할 테지만, 기본적인 부분과 메소드 오버라이드는 꼭 포함해야 합니다.

#### 필수 메소드

특히, ``BitmapTransformation`` 포함 어떠한 ``Transformation` 서브 클래스들은 디스크나 메모리 캐싱이 올바르게 동작하기 위해서는 3가지 필수  메소드를 *필히** 구현하여야 합니다.：

1. ``equals()``
2. ``hashCode()``
3. ``updateDiskCacheKey``

만약 [``Transformation``][1] 이 별도의 아규먼트(arguments) 를 가지지 않는다면, ``static`` ``final`` ``String`` 의 전체 패키지명의 ID 를 ``hashCode()`` 에 사용하고, ``updateDiskCacheKey()`` 에는 ``MessageDigest`` 를 사용하는 것이 좋습니다. 만약 [``Transformation``][1] 이 ``Bitmap`` 변환에 영향을 미치는 아규먼트(argments) 를 가지는 상황에서도 위 3가지 메소드는 포함하고 있어야 합니다.

예를 들의 Glide 의 [``RoundedCorners``][21] 는 라운드 코너의 반경(radius) 을 정하는 ``int`` 값을 가지는데, 이에 해당하는 ``equals()``, ``hashCode()``, ``updateDiskCacheKey`` 의 구현은 아래와 같습니다. ：

```java
  @Override
  public boolean equals(Object o) {
    if (o instanceof RoundedCorners) {
      RoundedCorners other = (RoundedCorners) o;
      return roundingRadius == other.roundingRadius;
    }
    return false;
  }

  @Override
  public int hashCode() {
    return Util.hashCode(ID.hashCode(),
        Util.hashCode(roundingRadius));
  }

  @Override
  public void updateDiskCacheKey(MessageDigest messageDigest) {
    messageDigest.update(ID_BYTES);

    byte[] radiusData = ByteBuffer.allocate(4).putInt(roundingRadius).array();
    messageDigest.update(radiusData);
  }
```

본래의 ``String`` id 와 함께 ``roundingRadius`` 가 3개의 모든 메소드에 포함된 것을 확인할 수 있습니다. ``updateDiskCacheKey`` 를 보면 어떻게 ``ByteBuffer`` 를 사용해 기본 아규먼트를 ``updateDiskCacheKey``를 구현에 사용 하는지 보여주고 있습니다.

#### equals() / hashCode() 을 잊지 마세요!

equals() / hashCode() 에 대해서 한번 더 언급할 가치가 있어 보입니다. ``equals()`` 와 ``hashCode()`` 는 메모리 캐싱을 위해 꼭 구현하셔야 합니다. 안타깝게도 ``BitmapTransformation`` 와 ``Transformation`` 는 위 두 함수가 오버라이드가 되어 있지 않더라도 컴파일이 될 것 입니다. 컴파일이 된다는 말이 올바르게 동작할 것을 보장한다는 말이 아닙니다. Glide 추후 버전에서는 기본 ``equals()`` 나 ``hashCode`` 만 있을 경우 컴파일 타임 에러를 발생시키는 옵션을 추가할 예정 입니다.

### Glide 특수 동작

#### Transformations 재사용
``Transformations`` 는 비상태기반(stateless) 이기에, ``Transformation``을 다수의 로드에서 사용할 경우 인스턴스를 재사용 하는 것이 안전 합니다. 대개 하나의 ``Transformation`` 을 만들고 이를 다수의 로드에 패스하는 것이 좋은 사용법이라 할 수 있습니다.

#### ImageView 자동 변환
Glide 를 사용해서 [ImageView][7] 에 로드 할 경우 Glide 는 자동적으로 [FitCenter][2] 나 [CenterCrop][4] 을 view 의 [ScaleType][8] 에 맞추어 적용해 줍니다. 만약 `scaleType` 이 ``CENTER_CROP`` 이라면, Glide 는 자동적으로 ``CenterCrop`` 을 적용 할 것이며, `scaleType` 이 ``FIT_CENTER`` 나 ``CENTER_INSIDE`` 라면 ``FitCenter`` 를 적용 합니다.

[RequestOptions][9] 을 사용할 경우 ``Transformation`` 셋(set)으로 기본 변환을 오버라이드 할 수 있습니다. 추가적으로 [``dontTransform()``][10] 사용하면 ``Transformation`` 이 자동 적용 되지 않도록 할 수 있습니다.

#### 커스텀 리소스
Glide 4.0 은 디코딩 하고자 하는 리소스의 super 타입을 지정할 수 있기에 정확하게 어떤 타입의 transfromation 을 적용해야할지 모르는 경우가 있을 수 있습니다. 예를 들어, [``asDrawable()``][11] (혹은 ``asDrawable()`` 이 기본이기에  ``with()``) 를 Drawable 요청에 사용할 경우 [``BitmapDrawable``][12] 이나 [``GifDrawable``][13] 서브 클래스를 얻게 될 수 있습니다.

``RequestOptions`` 에 추가되는 어떠한 ``Transformation`` 도 사용 가능하다록 하기 위해, Glide 는 [``transform()``][14] 에 제공하는 리소스를 키로 사용하는 Map 에 ``Transformation`` 을 추가합니다. 리소스가 성공적으로 디코드 되면, Glide 는 이 Map 을 탐색하여 해당 리소스에 맞는 ``Transformation`` 을 반환하게 됩니다.

Glide 는 ``Bitmap`` ``Transformation``을 ``BitmapDrawable`` , ``GifDrawable`` , ``Bitmap`` 리소스에 사용 할 수 있습니다. 따라서 일반적으로 ``Bitmap`` ``Transformation`` 만 사용하시면 됩니다. 하지만 별도 리소스 타입을 추가할 경우 [``RequestOptions``][15] 의 서브 클래스를 만들고 항상 사용자 지정 타입을 위한 ``Transformation`` 을 ``Bitmap`` ``Transformations`` 과 함께 사용해야 합니다.

[1]: {{ site.baseurl }}/javadocs/400/com/bumptech/glide/load/Transformation.html
[2]: {{ site.baseurl }}/javadocs/400/com/bumptech/glide/load/resource/bitmap/FitCenter.html
[3]: options.html
[4]: {{ site.baseurl }}/javadocs/400/com/bumptech/glide/load/resource/bitmap/CenterCrop.html
[6]: {{ site.baseurl }}/javadocs/400/com/bumptech/glide/load/resource/bitmap/CircleCrop.html
[7]: http://developer.android.com/reference/android/widget/ImageView.html
[8]: http://developer.android.com/reference/android/widget/ImageView.ScaleType.html
[9]: {{ site.baseurl }}/javadocs/400/com/bumptech/glide/request/RequestOptions.html
[10]: {{ site.baseurl }}/javadocs/400/com/bumptech/glide/request/RequestOptions.html#dontTransform--
[11]: {{ site.baseurl }}/javadocs/400/com/bumptech/glide/RequestManager.html#asDrawable--
[12]: http://developer.android.com/reference/android/graphics/drawable/BitmapDrawable.html
[13]: {{ site.baseurl }}/javadocs/400/com/bumptech/glide/load/resource/gif/GifDrawable.html
[14]: {{ site.baseurl }}/javadocs/400/com/bumptech/glide/request/RequestOptions.html#transform-java.lang.Class-com.bumptech.glide.load.Transformation-
[15]: {{ site.baseurl }}/javadocs/400/com/bumptech/glide/request/RequestOptions.html
[16]: {{ site.baseurl }}/doc/generatedapi.html
[17]: {{ site.baseurl }}/javadocs/400/com/bumptech/glide/request/RequestOptions.html#transform-java.lang.Class-com.bumptech.glide.load.Transformation-
[18]: {{ site.baseurl }}/javadocs/400/com/bumptech/glide/load/MultiTransformation.html
[19]: {{ site.baseurl }}/javadocs/410/com/bumptech/glide/request/RequestOptions.html#transforms-com.bumptech.glide.load.Transformation...-
[20]: {{ site.baseurl }}/javadocs/440/com/bumptech/glide/load/resource/bitmap/BitmapTransformation.html
[21]: {{ site.baseurl }}/javadocs/440/com/bumptech/glide/load/resource/bitmap/RoundedCorners.html
