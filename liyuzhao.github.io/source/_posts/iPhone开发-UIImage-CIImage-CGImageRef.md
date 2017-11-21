---
title: 'iPhone开发-UIImage,CIImage,CGImageRef'
date: 2017-11-15 20:47:08
tags: [ios]
categories: ios
---

UIImage 有很多有用的东西，前段时间接触了coreImage的API，发现有一个CIImage的东西，同样还有一个CGImage的东西，这三者总是可以切换起来，多少让人觉得这个东西很能耐。
IOS编程揭秘 书中记录着如下一段话。

```
UIImage类的Core Graphics
版本是CGImage（CGImageRef）这两个类之间很容易进行转换，因为一个UIImage类有一个CGImage的属性“
```

<!-- more -->

### 1.创建过程
UIImage 常规创建过程
```
UIImage *image =[ UIImage imageNamed:@"xx.png"];
```
CGImage的创建过程
```
CGImageRef imageRef = CGImageCreateWithImageInRect(image.CGImage,CGRectMake(0,0,size.width,size.height));

```
或者
```
UIImage *image =[ UIImage imageNamed:@"xx.png"];
CGImageRef imageRef = [image CGImage];
```

CIImage的创建过程

```
NSString *path = [[NSBundle mainBundle] pathForResource:@"bbg" ofType:@"jpg"];
NSURL *myURL = [NSURL fileURLWithPath:path];

CIImage *ciImage = [CIImage imageWithContentsOfURL:myURL];
```

### 2.相互转换

UIImage， CGImageRef， CIImage 三者之间可以通过一些联系进行转换

#### 2.1 UIImage 转换CGImageRef
UIImage 类当中包含了CGImage的属性，所以很方便地就能转换，方法如下
```
UIImage *image =[ UIImage imageNamed:@"xx.png"];
CGImageRef imageRef = [image CGImage];
```

#### 2.2 CGImageRef 转换UIImage
UIImage里面包含了一个方法imageWithCGImage，如果知道了CGImage，则这样子也可以创建得到UIIamge类，在上面我们可以看到关系 UIImage 通过属性得到CGImageRef，同样地两者也可以关联起来。

UIImage—>CGImageRef
CGImageRef –> UIImage

```
UIImage *uiImage =[UIImage imageWithCGImage:cgImage];
```
#### 2.3 CIImage 转换CGImageRef
CIContext 当中有一个方法createCGImage，可以创建得到CGImageRef，换句话可知道CIImage 可以通过其他方式转换CGImageRef
```
CIContext *context = [CIContext contextWithOptions:nil];                
 CIImage *ciImage = [CIImage imageWithContentsOfURL:myURL];                
 filter = [filterWithName:@"CISepiaTone"];            
 [filter setValue:ciImage forKey:kCIInputImageKey];
 [filter setValue:@0.8f forKey:kCIInputIntensityKey];
 CIImage *outputImg = [filter outputImage];   
 CGImageRef cgImage = [context createCGImage:outputImg fromRect:[outputImg extent]];
```

最主要的一句
```
CGImageRef cgImage = [context createCGImage:outputImg fromRect:[outputImg extent]];

```

#### 2.4 UIImage 转换CIImage
```
CIImage  *ciImage = [UIImage imageNamed:@"test.png"].CIImage
UIImage *image = [[UIImage alloc] initWithCIImage:ciImage];
```

但是采用这种方式转换，CIImage的值会是nil，
相反 采用CIImage 的initWithCGImage初始化的方式 则有值，很奇怪
```
UIImage *image = [UIImage imageNamed:@"test.png"];
CIImage *ciImage = [[CIImage alloc]initWithCGImage:image.CGImage];
```
由此可见，三者都可以实现转换了，通过直接或者间接把他们联系起来。
UIImage –> CGImageRef –> CIImage
UIImage <– CGImageRef <– CIImage
