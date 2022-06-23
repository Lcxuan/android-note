## Glide 图片加载库

Glide是一个快速高效的Android图片加载库，可以自动加载网络、本地文件、app资源中图片，注重于平滑的滚动

开源地址：https://github.com/bumptech/glide

中文文档：[Glide v4 : 快速高效的Android图片加载库 (muyangmin.github.io)](https://muyangmin.github.io/glide-docs-cn/)



### 基本使用

```java
Glide.with(this)
    .load("网络图片/资源")
    .into(imageView);
```



### 占位符

placeholder：正在请求图片的时候展示的图片
error：如果请求失败的失败展示的图片，如果没有设置，还是展示placeholder的图片
fallback：如果请求的url/model为null的时候展示的图片，如果没有设置，还是展示placeholder的图片

```java
RequestOptions requestOptions = new RequestOptions()
    .placeholder(R.drawable.ic_launcher_background)
    .error(R.drawable.ic_launcher_foreground)
    .fallback(R.drawable.ic_launcher_background);

Glide.with(this)
	.load("网络图片/资源")
    .apply(requestOptions)
    .into(image);
```



### 过渡动画

```java
RequestOptions requestOptions = new RequestOptions()
    .placeholder(R.drawable.ic_launcher_background)
    .error(R.drawable.ic_launcher_foreground)
    .fallback(R.drawable.ic_launcher_background);

DrawableCrossFadeFactory factory = new DrawableCrossFadeFactory.Builder().setCrossFadeEnabled(true).build();

Glide.with(this)
    .load("https://th.bing.com/th/id/OIP.KyyWEP5vqh7fuQd3mbZHtgHaFj?pid=ImgDet&rs=1")
    .apply(requestOptions)
    .transition(DrawableTransitionOptions.withCrossFade(factory))	//实现过滤效果
    .into(image);
```



### 变换

可以获取资源并修改它，然后返回被修改后的资源。通常变换操作是用来完成剪裁或对位图应用过滤器，但它也可以用于转换GIF动画，甚至自定义的资源类型。

CircleCrop：圆角

RoundedCorners：四个角度统一指定

GranularRoundedCorners：四个角度单独指定

Rotate：旋转

```java
RequestOptions requestOptions = new RequestOptions()
	.placeholder(R.drawable.ic_launcher_background)
	.error(R.drawable.ic_launcher_foreground)
	.fallback(R.drawable.ic_launcher_background);

DrawableCrossFadeFactory factory = new DrawableCrossFadeFactory.Builder().setCrossFadeEnabled(true).build();

Glide.with(this)
	.load("https://th.bing.com/th/id/OIP.KyyWEP5vqh7fuQd3mbZHtgHaFj?pid=ImgDet&rs=1")
	.apply(requestOptions)
	.transition(DrawableTransitionOptions.withCrossFade(factory))
	.transform(new CircleCrop())	//变换效果
	.into(image);
```



### Generatde API

在Application模块中包含一个AppGlideModule的实现

```java
@GlideModule
public class MyAppModule extends AppGlideModule {
}
```

此时就可以更简单的完成占位符的配置

```java
GlideApp.with(this)
    .load("https://th.bing.com/th/id/OIP.KyyWEP5vqh7fuQd3mbZHtgHaFj?pid=ImgDet&rs=1")
    .placeholder(R.drawable.ic_launcher_background)
    .into(image);
```



### GlideExtension和GlideOption

```java
@GlideExtension
public class MyAppExtension {
    private MyAppExtension(){}

    public static BaseRequestOptions<?> defaultImg(BaseRequestOptions<?> options){
        return options
                .placeholder(R.drawable.ic_launcher_background)
                .error(R.drawable.ic_launcher_background)
                .fallback(R.drawable.ic_launcher_background);
    }
}
```

此时可以使用占位符

```java
GlideApp.with(this)
    .load("https://th.bing.com/th/id/OIP.KyyWEP5vqh7fuQd3mbZHtgHaFj?pid=ImgDet&rs=1")
    .defaultImg()
    .into(image);
```

