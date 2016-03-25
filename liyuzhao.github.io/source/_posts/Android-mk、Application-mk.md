---
title: Android.mk、Application.mk
date: 2016-03-14 11:42:27
tags: [ndk]
categories: ndk
---

# Android.mk、Application.mk

### Application.mk
Application.mk 目的是描述在你的应用程序中所需要的模块(即静态库或动态库)，如下是表示编译出所有对应的平台。

`````
APP_ABI := all
`````
### Android.mk
Android.mk是Android提供的一种makefile文件，用来指定诸如编译生成so库名、引用的头文件目录、需要编译的.c/.cpp文件和.a静态库文件等

`````
LOCAL_PATH := $(call my-dir)

include $(CLEAR_VARS)

LOCAL_MODULE := hello-jni
LOCAL_SRC_FILES := hello-jni.c

include $(BUILD_SHARED_LIBRARY)
`````
<!-- more -->

#### LOCAL_PATH := $(call my-dir)
每个Android.mk文件必须以定义LOCAL_PATH为开始。它用于在开发tree中查找源文件。
宏 my-dir 则由Build System提供。返回包含Android.mk的目录路径。

#### include $(CLEAR_VARS)
CLEAR_VARS 变量由Build System提供。并指向一个指定的GNU Makefile，由它负责清理很多LOCAL_xxx.
例如：LOCAL_MODULE, LOCAL_SRC_FILES, LOCAL_STATIC_LIBRARIES等等。但不清理LOCAL_PATH.
这个清理动作是必须的，因为所有的编译控制文件由同一个GNU Make解析和执行，其变量是全局的。所以清理后才能避免相互影响。

#### LOCAL_MODULE := hello-jni
LOCAL_MODULE 模块必须定义，以表示Android.mk中的每一个模块。名字必须唯一且不包含空格。
Build System会自动添加适当的前缀和后缀。例如, foo, 要产生动态库，则生成libfoo.so。但请注意：如果模块名被定为：libfoo。则生成libfoo.so。不再加前缀。

#### LOCAL_SRC_FILES := hello-jni.c
LOCAL_SRC_FILES变量必须包含将要打包入模块的C/C++源码。
不必列出头文件，Build System会自动帮我们找到依赖文件。
缺省的C++源码的扩展名为.cpp。也可以修改，通过LOCAL_CPP_EXTENSION。

#### include $(BUILD_SHARED_LIBRARY)
BUILD_SHARED_LIBRARY: 是Build System提供的一个变量，指向一个GNU Makefile Script。
它负责收集自从上次调用`include $(CLEAR_VARS)`后的所有LOCAL_xxx信息。并决定编译什么。

##### BUILD_STATIC_LIBRARY: 编译为静态库。
##### BUILD_SHARED_LIBRARY: 编译为动态库。
##### BUILD_EXECUTABLE: 编译为Native C可执行程序

### 多文件编译

`````
LOCAL_PATH:= $(call my-dir)

# first lib, which will be built statically
#
include $(CLEAR_VARS)

LOCAL_MODULE    := libtwolib-first
LOCAL_SRC_FILES := first.c

include $(BUILD_STATIC_LIBRARY)

# second lib, which will depend on and include the first one
#
include $(CLEAR_VARS)

LOCAL_MODULE    := libtwolib-second
LOCAL_SRC_FILES := second.c

LOCAL_STATIC_LIBRARIES := libtwolib-first

include $(BUILD_SHARED_LIBRARY)
`````

### 语法详解

[android.mk](http://android.mk/)

> 作者：ben_speed 
> 文章源自：http://www.jianshu.com/p/46089c2276f3

