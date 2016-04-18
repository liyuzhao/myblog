---
title: 使用Android Studio的lint清除无用的资源文件
date: 2016-03-22 22:15:20
tags: [android, lint]
categories: android
---

## 使用Android Studio的lint清除无用的资源文件
<!-- more -->
> studio很容易查找无用的资源

在项目中，点击菜单栏的Analyze->Run Inspection by Name...
![lint-1.png](http://7xpj58.com1.z0.glb.clouddn.com/20160322-1.png)

选择后出现弹窗如下：
![lint-2.png](http://7xpj58.com1.z0.glb.clouddn.com/20160322-2.png)

在弹窗中输入 unused resources 选中点击回车后：
![lint-3.png](http://7xpj58.com1.z0.glb.clouddn.com/20160322-3.png)

这里可以选择，整个项目或者某个模块，也可以自定义选择某个目录
到此，Android Studio 就会自动分析，然后可以根据结果清除无用的资源文件。

**注意**：在某些情况下，也有可能检测错误，我就在build.gradle中有引用图片，然而这里无法查询到，但大部分查出是对的。


> Note:
> 1.一般是删除无用的Java文件
> 2.然后删除无用Java文件中引用的xml文件
> 3.删除xml或Java代码中用到的图片等资源文件
> 4.可以通过指定图片Alt+F7(Find Usages)查询是否引用




