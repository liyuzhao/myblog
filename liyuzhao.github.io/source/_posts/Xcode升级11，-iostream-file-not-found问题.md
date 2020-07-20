---
title: Xcode升级11，'iostream' file not found问题
date: 2020-07-20 19:09:45
tags: [xcode, c++]
categories: xcode
---

升级xcode11后，代码报错：'iostream' file not found

解决方法：

Build Settings -> Search paths -> System Header Search Paths

在Debug和Release添加

```
/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/include/c++/v1
```

添加后，重新编译，发现已正常。