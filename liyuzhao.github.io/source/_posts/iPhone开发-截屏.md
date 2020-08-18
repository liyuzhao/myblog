---
title: iPhone开发-截屏
date: 2017-11-15 20:23:09
tags: [ios]
categories: ios
---
iPhone 截屏并本地存储

使用UIGraphicsBeginImageContext 相关绘图API 获取到图像信息，使用UIImageWriteToSavedPhotosAlbum 方法就可以保存到相关的库里面去

```
UIWindow *window =[UIApplication sharedApplication].keyWindow;
UIGraphicsBeginImageContext(window.frame.size);
[window.layer renderInContext:UIGraphicsGetCurrentContext()];
UIImage *image = UIGraphicsGetImageFromCurrentImageContext();
UIGraphicsEndImageContext();
UIImageWriteToSavedPhotosAlbum(image, self, @selector(image:didFinishSavingWithError:contextInfo:), nil);

-(void) image:(UIImage *) image didFinishSavingWithError:(NSError *) error contextInfo:(void *) contextInfo
{
    if (error != NULL)
    {
        NSLog(@"保存失败");
    }
    else
    {
        UIAlertView *alertView = [[UIAlertView alloc]initWithTitle:@"提示" message:@"保存成功！" delegate:nil cancelButtonTitle:@"确定" otherButtonTitles:nil, nil];
        [alertView show];
    }

}

```
