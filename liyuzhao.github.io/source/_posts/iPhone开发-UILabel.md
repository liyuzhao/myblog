---
title: iPhone开发-UILabel
date: 2017-11-15 20:38:52
tags: [ios]
categories: ios
---

UILable 的用法涉及内容有如下

1. 创建与显示
2. 文本内容和字体设置
3. 背景颜色指定
4. 计算高度。
5. 行数指定和计算

UIlabel可以显示指定的行数，设置numberOfLines =0 则为不限制行数，因为UIlabel不是Icontrol的方面，没有addTarget和block等方法处理相应的行为机制，但支持扩展手势触控等方法处理点击，不过对于html 超链接响应情况，在之前看过是需要采用第三方处理机制处理。

在日常使用过程，对于文本方法计算高度的用法很常用，ios提供相应计算方法。如属于NSString的类别（NSStringDrawing.h）该类为此提供相应处理解决方案，提供计算高度和文本大小的方法。
注意到 过去ios6的版本 提供sizeWithFont方法 在ios 7.0 已经不生效，需要改成其他方法处理。boundingRectWithSize 后续推荐的方式。

```
- (CGSize)sizeWithAttributes:(NSDictionary *)attrs NS_AVAILABLE_IOS(7_0);

```

```
- (CGRect)boundingRectWithSize:(CGSize)size options:(NSStringDrawingOptions)options attributes:(NSDictionary *)attributes context:(NSStringDrawingContext *)context NS_AVAILABLE_IOS(7_0);

```

```
    self.label = [[UILabel alloc]initWithFrame:CGRectMake(85, 0, self.view.frame.size.width-85, 70)];
    [self.panel addSubview:self.label];
    self.label.text = @"这个人很懒，什么都没留下";
    self.label.numberOfLines = 3;
    self.label.lineBreakMode = NSLineBreakByCharWrapping;
    self.label.font =[UIFont systemFontOfSize:14];
   // self.label.userInteractionEnabled = YES; 需要的时候才打开
    [self.view addSubview:self.panel];
```

下面计算一下高度，当采用默认字体的时候，字体号为17，其高度约为20, 字体为14的时候，高度约为16~17之间


```
字体号：17 ，文本高：20
字体号：16 ，文本高：19
字体号：15 ，文本高：17~18 之间
字体号：14， 文本高：16~17 之间
```

这些字体是默认字体，要是采用其他字体或者设置粗体 ，估计值会受到一些浮动影响。

```
[self getTextHeight:17 width:self.view.frame.size.width-85];   
-(void) getTextHeight:(CGFloat) fontSize width:(CGFloat) textWidth
{

    NSDictionary *dic =@{NSFontAttributeName:[UIFont systemFontOfSize:fontSize]};
    CGSize curSize = CGSizeMake(textWidth, MAXFLOAT);
    CGRect rect =   [self.label.text boundingRectWithSize:curSize
                                                  options:NSStringDrawingUsesLineFragmentOrigin|NSStringDrawingUsesFontLeading
                                               attributes:dic context:nil];

    NSString *str = NSStringFromCGRect(rect);
    NSLog(@"%@",str);

}
```
