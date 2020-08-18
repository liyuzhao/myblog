---
title: 'Android,iOS打开手机QQ与指定聊天界面'
date: 2016-04-15 10:45:20
tags: [android, qq, intent]
categories: android
---

 ### Android，iOS打开手机QQ与指定聊天界面
 
 > 在浏览器中可以通过JS代码打开QQ并弹出聊天界面，一般作为客服QQ使用。
 > 在移动端可以使用schema模式来启动手机QQ。
 
 <!-- more -->
 
#### Android:

```
String url="mqqwpa://im/chat?chat_type=wpa&uin=123456";  
startActivity(new Intent(Intent.ACTION_VIEW, Uri.parse(url)));  
```

#### iOS:

```
UIWebView *webView = [[UIWebView alloc] initWithFrame:CGRectZero];  
NSURL *url = [NSURL URLWithString:@"mqq://im/chat?chat_type=wpa&uin=123456&version=1&src_type=web"];  
NSURLRequest *request = [NSURLRequest requestWithURL:url];  
webView.delegate = self;  
[webView loadRequest:request];  
[self.view addSubview:webView];  

```
 
#### 浏览器：

```
<a target="_blank" href="http://wpa.qq.com/msgrd?v=3&uin=123456&site=qq&menu=yes">click here</a>  
``` 
 
