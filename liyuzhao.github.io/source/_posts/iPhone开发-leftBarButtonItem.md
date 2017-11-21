---
title: iPhone开发-leftBarButtonItem
date: 2017-11-15 20:27:50
tags: [ios]
categories: ios
---

### 1.设置leftBarButtonItem的文字
在设置 leftBarButtonItem的时候，又会少了一个左箭头。

一般情况下，自定义一个新的标题会这样操作，设置完成后，问题来了，发现左箭头不见了。
```
self.navigationItem.leftBarButtonItem =  [[UIBarButtonItem alloc]initWithTitle:@"返回" style:UIBarButtonItemStylePlain target:self  action:@selector(onSelect)];
```

一般情况下，自定义 UIBarButtonItem 使用设置图片有如下，但发现文本不能设置显示了。
```
[[UIBarButtonItem alloc]initWithImage:image style:UIBarButtonItemStylePlain target:self
                                   action:@selector(onSelect)];
```

还有一种方式，既有文本又有左箭头的需求，只能使用下面这种方式，自定义一个按钮视图。
```
[[UIBarButtonItem alloc]initWithCustomView:backButton];
```


问题前提： 使用pushViewController的时候，该界面返回情况会默认是上一级的标题， 更改文字，又会对本身产生影响。原本意图很简单，就是想设置自己的文字，又想有左箭头。
```
[self.navigationController pushViewController:cityController animated:YES];
```

<!-- more -->
### 2.实现过程
实现这个过程改用了一种做法是 用按钮的方式去完成这件事。这里涉及几个问题。设置了图片的时候，按钮点下去还是会出现**蓝色**。

在写的时候，会发现Button 的 setImage 和 setBackGroundImage 是有区别的。
**setBackGroundImage**： 会对原图拉伸产生影响，跟随性。也就是说，按钮宽度改变，底图也发生改变

**setImage** ： 会保持原图性质，不随改变而改变。
在测试过程也是一样。

为了解决点击后保存一致的效果， 改用暴力的方式，对点击下去的情况设置一张透明度的图 。同时对文本设置透明度。这样有点罗嗦和累赘。暂时还没发现更好的方式。有一些人说是设置backBarButtonItem 但是没有效果。我也遇到这个问题。**backBarButtonItem 和 leftBarButtonItem 还是有区别**。

为了解决按钮图片和文本点击后透明度变化，采取的办法是对两个状态设置不同的UIImage信息。其中这里使用到复制位图的操作，UIGraphicsBeginImageContext 使用这个方式。拷贝一个UIImage副本出来，对其透明度alpha 设置。
这个设计的目标是为 了解决点击后还是变蓝色的问题。

```
//按钮
[backButton setImage:image forState:UIControlStateNormal];
[backButton setImage:neImage forState:UIControlStateHighlighted];
//文本颜色
[backButton setTitleColor:[UIColor whiteColor] forState:UIControlStateNormal];
[backButton setTitleColor:[UIColor colorWithWhite:1.0 alpha:0.7] forState:UIControlStateHighlighted];
```

一切完成后，在刚运行的过程当中，按钮背景图片和文本粘贴在比较紧，可以使用 setImageEdgeInsets的方式让其他拉开一点距离。这样子可以实现一个自定义LeftBarButtonItem。

```
UIButton *backButton = [[UIButton alloc ]initWithFrame:CGRectMake(0, 0, 60, 30)];
[backButton addTarget:self action:@selector(onSelectCity:) forControlEvents:UIControlEventTouchUpInside];
UIImage  *image =[[UIImage imageNamed:@"navbar_btn@2x.png"] imageWithRenderingMode:UIImageRenderingModeAlwaysOriginal];
[backButton setImage:image forState:UIControlStateNormal];

UIGraphicsBeginImageContext(image.size);
[image drawInRect:CGRectMake(0,0,image.size.width,image.size.height) blendMode:kCGBlendModeNormal alpha:0.7];
UIImage *neImage = UIGraphicsGetImageFromCurrentImageContext();
UIGraphicsEndImageContext();
[backButton setImage:neImage forState:UIControlStateHighlighted];
[backButton setTitleColor:[UIColor whiteColor] forState:UIControlStateNormal];
backButton.titleLabel.font  =[UIFont systemFontOfSize:14] ;
[backButton setTitleColor:[UIColor colorWithWhite:1.0 alpha:0.7] forState:UIControlStateHighlighted];
[backButton setTitle:@"全部" forState:UIControlStateNormal];
[backButton setImageEdgeInsets:UIEdgeInsetsMake(0, -15, 0, 0)];
UIBarButtonItem *backItem = [[UIBarButtonItem alloc]initWithCustomView:backButton];
self.navigationItem.leftBarButtonItem = backItem;
```

### 3.遇到的问题

问题1： UITabbar选择图片的问题，选中后出现蓝色的问题，而不是自定义的

问题2：按钮点击后，蓝色按钮蓝色的问题

问题3：颜色设置缺少一个0会出现奇特现象

问题4：UIImage的缩放操作


在学习实验过程，遇到问题 UIColor colorWithRed的方法，后缀 255.0 去掉了0 后变成255 会发现很莫名的颜色，这个小数点影响了一些结果。非法奇怪。

```
UIColor *color = [UIColor colorWithRed:254/255.0 green:85/255.0 blue:90/255.0 alpha:1.0];

```

其次是UITabBarItem 使用了两张图片做状态，一种是默认的情况，一种是选中的效果。会发现变成蓝色。不同不使用UIImage的imageWithRenderingMode的方式去改回原图。这样子这个问题就解决了。

```
UIImage  *image =[[UIImage imageNamed:@"navbar_btn@2x.png"] imageWithRenderingMode:UIImageRenderingModeAlwaysOriginal];

```

而NavigationBar的颜色，也是一个坑的，旧的API只能在旧版本使用，新的版本需要在7.0以上才能使用，这样要兼容6.0 还要挖个坑。有时候 会对背景整个栏目设置，所以在这个情况下只能使用兼容方式。
