---
title: glide的简单用法
date: 2016-08-29 20:15:27
tags: [glide]
categories: android
---

首先需要在app的build.gradle中加入
```
compile 'com.github.bumptech.glide:glide:+'
```
注："+"表示始终请求最新版

<!-- more -->

### 基本用法
```
Glide.with(context)
        .load(url)//图片地址
        .placeholder(R.drawable.default_image)//加载中显示的图片
        .error(R.drawable.error_image)//加载出错显示的图片
        .crossFade()//淡入效果
        .into(imageView);

```
### 缓存多尺寸

因为Glide默认只缓存一种尺寸大小的图片，即当前要加载的ImageView大小的图片，当你要在另一个不同大小的ImageView中加载同一张图片时Glide就会再次请求加载新的尺寸的图片，但是Glide给我们提供了一个设置可以缓存全尺寸的图片，这样在不同大小的ImageView中加载同一张图片就只会产生一次请求。

```
Glide.with(context)
        .load(url)
        .diskCacheStrategy(DiskCacheStrategy.ALL)
        .into(imageView);
```

### 加载特定大小的图片

```
Glide.with(context)
        .load(url)
        .override(300, 200);
        .into(imageView);

```

### Center Cropping

```
Glide.with(context)
        .load(url)
        .centerCrop();
        .into(imageView);
```

### Transforming
```
Glide.with(context)
        .load(url)
        .transform(new CircleTransform(context))
        .into(imageView);
```

### 特性：加载Gif、加载本地图片、asbitmap
Glide可以加载Gif动态图，使用方法和加载普通图片一样，同时因为Glide和Activity/Fragment的生命周期是一致的，因此gif的动画也会自动的随着Activity/Fragment的状态暂停、重放。Glide 的缓存在gif这里也是一样，调整大小然后缓存。

```
Glide.with(context)
        .load(url)//图片地址
        .asGif()//asGif加载Gif动态图，asBitmap可以将Gif解码成bitmap
        .placeholder(R.drawable.default_image)//加载中显示的图片
        .error(R.drawable.error_image)//加载出错显示的图片
        .crossFade()//淡入效果
        .into(imageView);

```
