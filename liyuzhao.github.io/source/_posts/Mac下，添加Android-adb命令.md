---
title: Mac下，添加Android adb命令
date: 2016-03-09 23:58:48
tags: [mac]
categories: mac
---

1 在用户目录下 ~/.bash_profile 如果存在打开，不存在则创建

``` 
vim ~/.bash_profile
```
2 加入我们需要的添加的环境变量，例如：Android需要添加platform-tools 和 tools

<!--more-->
``` 
export PATH=${PATH}:/Users/user/android-sdks/platform-tools:/Users/user/android-sdks/tools 

或者
export PATH=/Users/user/android-sdks/platform-tools:$PATH 
export PATH=/Users/user/android-sdks/tools:$PATH 

其中 :$PATH 不能少，表示加入系统的环境变量
```

3 进行验证 

> 重新打开一个终端：输入adb,即可验证。



