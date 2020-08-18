---
title: iPhone开发-常用组件
date: 2017-11-15 17:35:41
tags: [ios]
categories: ios
---

### 1. UIButton

```
 CGRect frame = CGRectMake(0, 100, 80, 40);
 UIButton *button = [UIButton buttonWithType:UIButtonTypeRoundedRect];
 button.frame = frame;
 [button setTitle:@"click" forState: UIControlStateNormal];
 button.backgroundColor = [UIColor greenColor];
 [button addTarget:self action:@selector(buttonClicked:) forControlEvents:UIControlEventTouchUpInside];
 [self.view addSubview:button];

 -(void) buttonClicked:(UIButton *)button
 {
   //添加code
 }
```
### 2. UIAlertView

```
UIAlertView *alert = [[UIAlertView alloc]initWithTitle:@"标题" message:@"你的内存已满" delegate:self cancelButtonTitle:@"取消" otherButtonTitles:nil, nil];
   [alert show];

```


<!-- more -->
### 3. UILabel

```
UILabel *label =[[UILabel alloc]initWithFrame:CGRectMake(0, 10, 200, 34)];
label.textColor =[UIColor grayColor];
label.font =[UIFont systemFontOfSize:18];
label.text =@"创建一个文本";
label.lineBreakMode = NSLineBreakByCharWrapping;
label.numberOfLines = 0;
label.backgroundColor =[UIColor grayColor];
label.textAlignment = NSTextAlignmentCenter;
```

### 4. UITextField

```

UITextField *textField =[[UITextField alloc]initWithFrame:CGRectMake(10, 44, self.view.bounds.size.width-20,30)];
 textField.borderStyle = UITextBorderStyleRoundedRect;
 textField.placeholder = @"请输入用户名";
 textField.clearButtonMode = UITextFieldViewModeAlways;
 textField.delegate = self;
 textField.textColor = [UIColor grayColor];
 textField.keyboardType = UIKeyboardTypeDefault;
 textField.returnKeyType = UIReturnKeyDone;
 textField.clearButtonMode = UITextFieldViewModeWhileEditing;
```

### 5.UISlider

```
UISlider *slider =[[UISlider alloc]initWithFrame:CGRectMake(0, 100, 200, 33)];
  [slider addTarget:self action:@selector(onChangeHandler:) forControlEvents:UIControlEventValueChanged];
  [self.view addSubview:slider];

-(void) onChangeHandler:(UISlider *) slider
{
    float value = slider.value;
}
```

### 6.UISegmentedControl

```
UISegmentedControl *seg= [[UISegmentedControl alloc]initWithItems:@[@"骑士",@"勇士"]];
   [self.view addSubview:seg];
   seg.frame =CGRectMake(20, 200, 200, 33);
   [seg addTarget:self action:@selector(onSelect:) forControlEvents:UIControlEventValueChanged];

-(void) onSelect:(UISegmentedControl *) control
{
   NSInteger index = control.selectedSegmentIndex;
    if (index==0)
    {
        NSLog(@"点击骑士");
    }
    else
    {
        NSLog(@"点击勇士");
    }

}

```

### 7.UIViewImage

```
UIImageView *imageView =[[UIImageView alloc]initWithImage:[UIImage imageNamed:@"head.png" ]];
imageView.layer.cornerRadius = 4.0;
```
### 8.UISwitch

```
UISwitch *uiSwitch=[[UISwitch alloc]initWithFrame:CGRectMake(20, 200, 100, 33)];
   [self.view addSubview:uiSwitch];
   [uiSwitch setOn:YES animated:YES];
   [uiSwitch addTarget:self action:@selector(onChange:) forControlEvents:UIControlEventValueChanged];

-(void) onChange:(UISwitch *) uiswitch
{
  BOOL isOn =  uiswitch.isOn;
  //两种方式输出真假值
  NSLog(@"%@",  [NSNumber numberWithBool:isOn].stringValue);
  NSLog(@"%@",  isOn ? @"YES":@"NO");
}

```

### 9.UIActionSheet

```
UIActionSheet *actionSheet = [[UIActionSheet alloc]initWithTitle:nil delegate:self cancelButtonTitle:@"取消" destructiveButtonTitle:@"微信" otherButtonTitles:@"新浪微博",@"腾讯微博", nil];
actionSheet.actionSheetStyle = UIActionSheetStyleDefault;
[actionSheet showInView:self.view];

-(void) actionSheet:(UIActionSheet *)actionSheet clickedButtonAtIndex:(NSInteger)buttonIndex
{
    if(buttonIndex == 0)
    {
        NSLog(@"微信");
    }
    else if(buttonIndex ==1)
    {
        NSLog(@"新浪微博");
    }
    else if(buttonIndex == 2)
    {
        NSLog(@"腾讯微博");
    }
}

```
### 10.UIWindow

```
UIWindow *window =[[UIWindow alloc]initWithFrame:[UIScreen mainScreen].bounds];
```

### 11.UIBarButtonItem

```
//左边
self.navigationItem.leftBarButtonItem = [[UIBarButtonItem alloc]initWithTitle:@"话题" style:UIBarButtonItemStylePlain target:self action:@selector(showMenu)];
 //右边
self.navigationItem.rightBarButtonItem = [[UIBarButtonItem alloc]initWithTitle:@"话题" style:UIBarButtonItemStylePlain target:self action:@selector(showMenu)];

-(void) showMenu
{
    [[NSNotificationCenter defaultCenter] postNotificationName:@"ShowMenuCmd" object:nil];
}
```

### 12.UIWebView
  UIWebView 的本地加载HTML页面
```
CGRect rect = [UIScreen mainScreen].bounds;
UIWebView *webView = [[UIWebView alloc] initWithFrame:CGRectMake(0, 0, rect .size.width,rect.size.height)];
[self.view addSubview:self.webView];
[webView setUserInteractionEnabled:YES];//是否支持交互
NSString *path =  [[NSBundle mainBundle] pathForResource:@"index" ofType:@"html"];
NSURL *url = [NSURL fileURLWithPath:path];
[webView loadRequest:[NSURLRequest requestWithURL:url]];   
webView.autoresizingMask = UIViewAutoresizingFlexibleHeight | UIViewAutoresizingFlexibleWidth;
```
### 13.UIPickerView
选择器创建与代理设置

```
NSArray *pickerArray =@[@"勇士",@"火箭",@"湖人",@"雷霆"];
UIPickerView *pickerView =[[UIPickerView alloc]init];
[self.view addSubview:self.pickerView];
pickerView.frame =(CGRect){0,self.view.frame.size.height-160,320,180};
pickerView.delegate = self;//设置代理
pickerView.dataSource = self;//设置代理
pickerView.backgroundColor = [UIColor orangeColor];

 - (NSInteger)numberOfComponentsInPickerView:(UIPickerView *)pickerView
{
    return 1;
}

- (NSInteger)pickerView:(UIPickerView *)pickerView numberOfRowsInComponent:(NSInteger)component
{
    return [pickerArray count];
}
-(NSString *) pickerView:(UIPickerView *)pickerView titleForRow:(NSInteger)row forComponent:(NSInteger)component
{
  return [pickerArray objectAtIndex:row];
}

- (void)pickerView:(UIPickerView *)pickerView didSelectRow:(NSInteger)row inComponent:(NSInteger)component
{
  NSLog(@"点击选择了");
}
```

### 14.UIScrollView

```
UIScrollView *scrollView = [[UIScrollView alloc] initWithFrame:CGRectMake(0, 40, 200, 100)];
scrollView.backgroundColor =  [UIColor blueColor];

    CGFloat y=20;
    for (int i=0; i<30;i++)
    {
        UILabel *lab = [[UILabel alloc]init];
        lab.text = @"第一章 约定的开始";
        [lab sizeToFit];
        CGRect f= lab.frame;
        f.origin.y = i*22;
        lab.frame =f;
        [scrollView addSubview:lab];
        y += lab.bounds.size.height +10;
    }
    CGSize sz=scrollView.bounds.size;
    sz.height = y;
    scrollView.contentSize = sz;
```

### 15.UIProgressView
  进度条
```
UIProgressView *progressView =[[UIProgressView alloc]initWithProgressViewStyle:UIProgressViewStyleDefault];
  [self.view addSubview:progressView];
  [NSTimer scheduledTimerWithTimeInterval:1.0 target:self selector:@selector(onTimer:) userInfo:progressView repeats:YES];

-(void) onTimer:(NSTimer *) timer
{
UIProgressView *progress =(UIProgressView *) timer.userInfo;
  progress.progress += 0.1;
  if(progress.progress == 1.0)
  {
      progress.progress =0;
  }
}
```

### 16.UIActivityIndicatorView
活动指示器
```
UIActivityIndicatorView *activityView= [[UIActivityIndicatorView alloc]initWithActivityIndicatorStyle:UIActivityIndicatorViewStyleWhiteLarge];
  [self.view addSubview:activityView];
  [activityView startAnimating];//播放动画
  [activityView stopAnimating];//停止动画
  [activityView isAnimating];//判断是否在播放动画
  self.view.backgroundColor=[UIColor orangeColor];//背景设置颜色方便预览该组件
```

### 17.UINavigationBar
  UINavigationBar 导航栏 需要抱一个导航栏目UINavigationItem

```
UINavigationBar *navbar =[[UINavigationBar alloc]initWithFrame:CGRectMake(0, 0, self.view.frame.size.width, 44)];
 [self.view addSubview:navbar];
 UIBarButtonItem *leftButtonItem = [[UIBarButtonItem alloc]initWithTitle:@"菜单" style:UIBarButtonItemStylePlain target:self action:@selector(onSelectLeft:)];
 UIBarButtonItem *rightButtonItem = [[UIBarButtonItem alloc]initWithTitle:@"设置" style:UIBarButtonItemStylePlain target:self action:@selector(onSelectRight:)];
 UINavigationItem *navigationItem =[[UINavigationItem alloc]initWithTitle:@"我爱IOS"];
 [navbar pushNavigationItem:navigationItem animated:NO];
 [navigationItem setLeftBarButtonItem:leftButtonItem];
 [navigationItem setRightBarButtonItem:rightButtonItem];

-(void) onSelectLeft:(UIBarButtonItem *) button
{
   NSLog(@"点击左边");
}

-(void) onSelectRight:(UIBarButtonItem *) button
{
   NSLog(@"点击右边");
}
```

### 18.UITabBar

```
UITabBar *tabBar =[[UITabBar alloc]initWithFrame:CGRectMake(0, [UIScreen mainScreen].bounds.size.height-44, self.view.frame.size.width, 44)];
[self.view addSubview:tabBar];
UITabBarItem *firstBarItem =[[UITabBarItem alloc]initWithTitle:@"首页" image:nil tag:1];
UITabBarItem *secondBarItem =[[UITabBarItem alloc]initWithTitle:@"我的" image:nil tag:2];
[tabBar setItems:@[firstBarItem,secondBarItem]];
```

### 19.UIApplication

```
NSURL *appStoreUrl = [NSURL URLWithString:@"http://phobos.apple.com/WebObjects/MZStore.woa/wa/viewSoftware?id=291586600&amp;amp;mt=8"];
[[UIApplication sharedApplication] openURL:appStoreUrl];
```

```
  //退出编辑
 [[UIApplication sharedApplication].keyWindow endEditing:YES];

  //设置网络状态
 [[UIApplication sharedApplication] setNetworkActivityIndicatorVisible:YES];//开启
 [[UIApplication sharedApplication] setNetworkActivityIndicatorVisible:NO];//关闭
```

### 20.UIRefreshControl
  刷新组件，继承了UITableController 有refreshControl 属性存在
```
UIRefreshControl *refreshControl = [[UIRefreshControl alloc]init];
  refreshControl.attributedTitle = [[NSAttributedString alloc]initWithString:@"刷新中.."];
  [refreshControl addTarget:self action:@selector(refreshTableView) forControlEvents:UIControlEventValueChanged];
  self.refreshControl = refreshControl;


-(void) refreshTableView
{
  //刷新后请求
}
```

### 21.UIImagePickerController
查看相册
```
UIImagePickerController *pickerController = [[UIImagePickerController alloc]init];
pickerController.sourceType  = UIImagePickerControllerSourceTypePhotoLibrary;
pickerController.delegate = self;
[self presentViewController:pickerController animated:YES completion:nil];

-(void) imagePickerController:(UIImagePickerController *)picker didFinishPickingMediaWithInfo:(NSDictionary *)info
{

  if([UIImagePickerController isSourceTypeAvailable:UIImagePickerControllerSourceTypePhotoLibrary])
  {
     UIImage *image =  [info objectForKey:UIImagePickerControllerOriginalImage];
     self.imageView.image = image;
  }

    [self dismissViewControllerAnimated:YES completion:nil];
}
```

### 22.UICollectionView

```
UICollectionViewFlowLayout* flowLayout = [[UICollectionViewFlowLayout alloc]init];
flowLayout.itemSize = CGSizeMake(120, 120);
[flowLayout setScrollDirection:UICollectionViewScrollDirectionVertical];
UICollectionView *collectionView = [[UICollectionView alloc]initWithFrame:rect collectionViewLayout:flowLayout ];
collectionView.backgroundColor = [UIColor whiteColor];
collectionView.dataSource = self;
collectionView.delegate = self;
[self.view addSubview:self.collectionView];

-(NSInteger) collectionView:(UICollectionView *)collectionView numberOfItemsInSection:(NSInteger)section
{
    return  1;
}

-(UICollectionViewCell *) collectionView:(UICollectionView *)collectionView cellForItemAtIndexPath:(NSIndexPath *)indexPath
{
    UICollectionViewCell *cell = [collectionView dequeueReusableCellWithReuseIdentifier:@"NodeCell" forIndexPath:indexPath];
    return cell;
}


-(void) collectionView:(UICollectionView *)collectionView didSelectItemAtIndexPath:(NSIndexPath *)indexPath
{
    //选中子项
}
```
### 23.MPMoviePlayerController
  视频控制
```
NSURL *url = [NSURL URLWithString:@"http://devimages.apple.com/iphone/samples/bipbop/bipbopall.m3u8"];
MPMoviePlayerController  *player =[[MPMoviePlayerController alloc]initWithContentURL:url];
player.fullscreen = YES;
CGRect winRect = [[UIScreen mainScreen] applicationFrame];
CGRect rect = CGRectMake(0,0,winRect.size.height, winRect.size.width);
player.controlStyle = MPMovieControlStyleDefault;
player.view.frame = rect;
player.view.center = CGPointMake(rect.size.width/2, rect.size.height/2);  
[player.view setTransform:CGAffineTransformMakeRotation(M_PI/2)];
layer.scalingMode = MPMovieScalingModeAspectFill;
[player play];
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(playCompleteFun:) name:MPMoviePlayerPlaybackDidFinishNotification object:nil];
[self.view addSubview:player.view];

-(void) playCompleteFun:(NSNotification *) notification
{
    [[NSNotificationCenter defaultCenter] removeObserver:self name:MPMoviePlayerPlaybackDidFinishNotification object:nil];
}
```
### 常用的一些代码片段记录

#### 视频截图
```
- (UIImage *) captureFromView: (UIView *) aView
{    
    UIGraphicsBeginImageContext(aView.frame.size);
    CGContextRef context = UIGraphicsGetCurrentContext();
    [aView.layer renderInContext:context];
    UIImage *image= UIGraphicsGetImageFromCurrentImageContext();
    UIGraphicsEndImageContext();     
    return image;
}
```

#### 遇到block的情况下转为self

```
__weak typeof(self)weakSelf = self;
__storng typeof(self)strongSelf = self;
```

### 常用的一些Build setting 记录

#### Bitcode 的开启和关闭
![bitcode](http://7xpj58.com1.z0.glb.clouddn.com/20150923200746917)

#### pch文件开启和配置路径位置
需要则在Precompile Prefix Header 开启 默认关闭
##### Prefix Header 则配置相应的路径
例如：**$(SRCROOT)/PrefixHeader.pch**
![PrefixHeader](http://7xpj58.com1.z0.glb.clouddn.com/20150923201059584)

##### Library Search Paths
![Search Paths](http://7xpj58.com1.z0.glb.clouddn.com/20150923201555972)

##### Product Name 产品名字设置
![Product BundleId](http://7xpj58.com1.z0.glb.clouddn.com/20150923201750634)

##### Other Linker Flags -Objc 设置
![Other linker](http://7xpj58.com1.z0.glb.clouddn.com/20150923202008456)
