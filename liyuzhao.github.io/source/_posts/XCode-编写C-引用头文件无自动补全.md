---
title: XCode 编写C++ 引用头文件无自动补全
date: 2020-10-13 12:19:30
tags: [xcode, c++]
categories: xcode
---

### 如题:XCode 编写C++ 引用头文件无自动补全

<!-- more -->

在Mac写C++代码，最好的工具，无疑是XCode，在xcode中写c++代码时，调用系统方法或引用系统头文件时，有代码提示或自动补全能够使开发更快，更不容易出错。

在Xcode 11写代码时，突然发现引入系统的头文件都没有提示。这个引起了我的好奇心，经过一顿操作和搜索，终于找到解决方案。

```
打开Xcode —> File —> Workspace Settings或者Project Settings 把界面上的build system的默认选项由"New Build System(Default)" 改为 "Legacy Build System"

```

