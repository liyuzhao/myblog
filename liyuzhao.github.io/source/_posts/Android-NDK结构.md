---
title: Android-NDK结构
date: 2018-12-28 11:15:20
tags: [android, ndk]
categories: android
---

### Android NDK 的结构

- ndk-build: 该shell脚本是Android NDK构建系统的起始点。
- ndk-gdb: 该shell脚本允许用GNU调试器调试原生组件。
- ndk-stack: 该shell脚本可以帮助分析原生组件崩溃时的堆栈追踪
- build: 该目录包含了Android NDK构建系统的所有模块。
- platforms: 该目录包含了支持不同Android目标版本的头文件和库文件。
- samples: 该目录包含了一些示例应用程序，这些程序可以体现Android NDK的性能。
- sources: 该目录包含了可供开发人员导入到现有的Android NDK项目的一些共享模块。
- toolchains: 该目录包含目录Android NDK支持的不用目标机体系结构的交叉编译器。Android NDK目前支持ARM、X86 和MIPS机体系结构。Android NDK 构建系统根据选定的体系结构使用不同的交叉编译器。

<!-- more -->

使用NDK可以做什么：

- 建立一个共享库
- 建立多种共享库
- 建立静态库
- 利用共享库共享通用模块
- 在多种NDK项目见共享模块
- 使用预建库
- 使用独立的可执行文件
- 其他构件系统变量和宏
- 定义新变量和条件操作


### Android.mk

```
LOCAL_PATH := $(call my-dir)

include $(CLEAR_VARS)

LOCAL_MODULE := hello-jni
LOCAL_SRC_FILES := hello-jni.c

include $(BUILD_SHARED_LIBRARY)

```

> LOCAL_PATH := $(call my-dir)

Android 构建系统利用LOCAL_PATH来定位源文件。因为将该变量设置为硬编码值并不合适，所以 Android 构建系统提供了一个名为 my-dir 的宏功能。通过该变量设置为 my-dir 宏功能的返回值， 可以将其放在当前目录下。

Android 构建系统将CLEAR_VARS变量设置为clear-vars.mk片段的位置。包含Makefile片段可以清除除了LOCAL_PATH以外的LOCAL_<name>变量，例如 LOCAL_MODULE 与 LOCAL_SRC_FILES 等。

> Include $(CLEAR_VARS)

这样做是因为 Android 构建系统在单次执行中解析多个构建文件和模块定义，而LOCAL_<name>是全局变量。清除他们可以避免冲突，每一个原生组件被称为一个模块。

LOCAL_MODULE 变量用来给这些模块设定一个唯一的名称。下面的代码将该模块的名称设为 hello-jni:

>LOCAL_MODLE := hello-jni

因为模块名称也被用于给构建过程所生成的文件命名，所以构建系统给该文件添加了适当的前缀和后缀。本例中， hello-jni 模块会生成一个共享库文件且构建系统会将它命名为`libhello-jni.so`。

用LOCAL_SRC_FILES变量定义用来建立和组装这个模块的源文件列表。

> LOCAL_SRC_FILES := hello-jni.c

这里，hello-jni 模块只由一个源文件生成，而 LOCAL_SRC_FILES 变量可以包含用空格分开的多个源文件名。

 ---
 构建多个共享库模块的 Androd.mk 构建文件
 
 ```
 LOCAL_PATH := $(call my-dir)
 
 #
 # 模块 1
 #
 include $(CLEAR_VARS)
 
 LOCAL_MODULE := module1
 LOCAL_SRC_FILES := module1.c
 include $(BUILD_SHARED_LIBRARY)
 
 #
 # 模块2
 #
 include $(CLEAR_VARS)
 
 LOCAL_MODULE := module2
 LOCAL_SRC_FILES := module2.c
 
 include $(BUILD_SHARED_LIBRARY)
 
 ```
 ---
 构建静态库的 Android.mk 构建文件
 
 ```
 LOCAL_PATH := $(call my-dir)
 
 #
 # 第三方 AVI 库
 #
 include $(CLEAR_VARS)
 
 LOCAL_MODULE := avilib
 LOCAL_SRC_FILES := avilib.c platform_posix.c
 
 include $(BUILD_STATIC_LIBRARY)
 
 #
 # 原生模块
 #
 include $(CLEAR_VARS)
 
 LOCAL_MODULE := module
 LOCAL_SRC_FILES := module.c
 
 LOCAL_STATIC_LIBRARIES := avilib
 
 include $(BUILD_SHARED_LIBRARY)
 
 ```
 在将第三方代码模块生成静态库之后，共享库就可以通过将它的模块名添加到`LOCAL_STATIC_LIBRARIES` 变量中来使用该模块。
 
 ---
 
##### 用共享库共享通用模块
 
 静态库可以保证源代码模块化；但是，在静态库与共享库相连时，它就变成了共享库的一部分。在多个共享库的情况下，多个共享库与同一个静态库连接时，需要将通用模块的多个副本与不同共享库重复相连，这样就增加了应用程序的大小。在这种情况下，不用构建静态库，而是将通用模块作为共享库建立起来，而动态连接依赖模块以便消除重复的副本。
 
 Android.mk 文件中共享库之间的代码共享
 
 ```
 LOCAL_PATH := $(call my-dir)
 #
 # 第三方 AVI 库
 #
 include $(CLEAR_VARS)
 
 LOCAL_MODULE := avilib
 LOCAL_SRC_FILES := avilib.c platform_posix.c
 
 include $(BUILD_SHARED_LIBRARY)
 
 #
 # 原生模块1
 #
 include $(CLEAR_VARS)
 
 LOCAL_MODULES := module1
 LOCAL_SRC_FILES := module1.c
 
 LOCAL_SHARED_LIBRARIES := avilib
 
 include $(BUILD_SHARED_LIBRARY)
 
 #
 # 原生模块2
 #
 include $(CLEAR_VARS)
 
 LOCAL_MODULE := module2
 LOCAL_SRC_FILES := module2.c
 
 LOCAL_SHARED_LIBRARIES := avilib
 
 include $(BUILD_SHARED_LIBRARY)
 
 
 ```
 
 ---
 
##### 不同NDK项目间共享模块
 
 **注意：** 在 Android NDK 构建系统中，共享模块路径不能包含空格。
 
 - 作为共享模块，avilib 需要自己的 Android.mk 文件

 共享 avilib 模块的 Android.mk 文件
 
 ```
 LOCAL_PATH := $(call my-dir)
 
 #
 # 第三方 AVI 库
 #
 include $(CLEAR_VARS)
 
 LOCAL_MODULE := avilib
 LOCAL_SRC_FILES := avilib.c platform_posix.c
 
 include $(BUILD_SHARED_LIBRARY)
 
 ```
 
 使用共享模块的 NDK项目
 
 ```
 #
 # 原生模块
 #
 include $(CLEAR_VARS)
 
 LOCAL_MODULE := module
 LOCAL_SRC_FILES := module.c
 LOCAL_SHARED_LIBRARIES := avilib
 
 include $(BUILD_SHARED_LIBRARY)
 
 $(call import-module, transcode/avilib)
 ```
 - import-module 函数库需要先定位共享模块，然后再将它导入到 NDK 项目中。默认情况下，import-module 函数库只搜索<Android NDK>/sources 目录。为了搜索 c:\android\shared-modules 目录，定义一个名为 `NDK_MODULE_PATH` 的新环境变量并将它设置成共享库的根目录，例如：c:\android\shared-modules。


---

预构建共享模块的 `Android.mk` 文件

```
LOCAL_PATH := $(call my-dir)

#
# 第三方预构建 AVI 库
#
include $(CLEAR_VARS)

LOCAL_MODULE := avilib
LOCAL_SRC_FILES := libavilib.so

include $(PREBUILT_SHARED_LIBRARY)

```

`LOCAL_SRC_FILES` 变量指向的不是源文件，而是实际 Prebuilt 库相对于 `LOCAL_PATH` 的位置。

**注意:** Prebuilt 库定义中不包含任何关于该库所构建的实际机器体系结构的信息。开发人员需要确保 Prebuilt 库是为与 NDK 项目相同的机器的体系结构而构建的。

`PREBUILT_SHARED_LIBRARY` 变量指向 prebuilt-shared-library.mk MakeFile 片段。它什么都没有构建，但是它将Prebuilt 库复制到 NDK 项目的libs目录下。通过使用 `PREBUILT_STATIC_LIBRARY` 变量，静态库可以像共享库一样被用作 Prebuilt 库，NDK 项目可以像普通共享库一样使用 Prebuilt库了。

```
	...
	LOCAL_SHARED_LIBRARIES := avilib
	...
```

 ---
 独立可执行模块的 Android.mk 文件
 
 ```
 #
 # 独立的可执行的原生模块
 #
 include $(CLEAR_VARS)
 LOCAL_MODULE := module
 LOCAL_SRC_FILES := module.c
 
 LOCAL_STATIC_LIBRARIES := avilib
 
 include $(BUILD_EXECUTABLE)
 
 ```
 
 `BUILD_EXECUTABLE` 变量指向 build-executable.mk Makefile 片段，该片段包含了在 Android 平台上生成独立可执行文件的必要步骤。独立可执行文件以与模块相同的名称被放在libs/<machine architecture>目录下。尽管放在该目录下，但在打包阶段它并没有被包含在APK文件中。
 
 ---
 
#### 其他构建系统变量

构建系统定义的变量有：

- `TARGET_ARCH`: 目标 CPU 体系结构的名称，例如 arm
- `TARGET_PLATFORM`: 目标 Android 平台的名称，例如: android-8
- `TARGET_ARCH_ABI`: 目标CPU体系结构和ABI的名称，例如：armeabi-v7a
- `TARGET_ABI`: 目标平台和 ABI 的串联，例如：android-7-armeabi-v7a

可被定义为模块说明部分的变量有：

- `LOCAL_MODULE_FILENAME`: 可选变量，用来重新定义生成的输出文件名称。默认情况下，构建系统使用`LOCAL_MODULE` 的值作为生成的输出文件名称，但变量 `LOCAL_MODULE_FILENAME` 可以覆盖 `LOCAL_MODULE` 的值。

- `LOCAL_CPP_EXTENSION`: C++源文件的默认扩展名是 .cpp。这个变量可以用来为C++源代码指定一个或多个文件扩展名。

```
...
LOCAL_CPP_EXTENSION := .cpp .cxx
...

```

- `LOCAL_CPP_FEATURES`: 可选变量，用来指明模块所依赖的具体 C++ 特性，如 RTTI 、 exceptions 等。

```
...
LOCAL_CPP_FEATURES := rtti
...

```

- `LOCAL_C_INCLUDES`: 可选目录列表，NDK 安装目录的相对路径，用来搜索头文件

```
...
LOCAL_C_INCLUDES := sources/shared-module
LOCAL_C_INCLUDES := $(LOCAL_PATH)/include
...
```

- `LOCAL_CFLAGS`: 一组可选的编译器标志，在编译C和C++源文件的时候会被传送给编译器。

```
...
LOCAL_CFLAGS := -DNDEBUG -DPORT=1234
...
```

- `LOCAL_CPP_FLAGS`: 一组可选的编译标志，在只编译C++源文件时被传送给编译器。
- `LOCAL_WHOLE_STATIC_LIBRARIES`:`LOCAL_STATIC_LIBRARIES` 的变体，用来指明应该被包含在生成的共享库中的所有静态库内容。

**小贴士**: 当几个静态库之间有循环依赖时，`LOCAL_WHOLE_STATIC_LIBRARIES` 很有用。

- `LOCAL_LDLIBS`:链接标志的可选列表，当对目标文件进行链接以生成输出文件时该标志将被传送给链接器。它主要用于传送要进行动态链接的系统库列表。
例如：要与 Android NDK 日志库链接，使用以下代码：
```
LOCAL_LDFLAGS := -llog
```
-`LOCAL_ALLOW_UNDEFINED_SYMBOLS`: 可选参数，它禁止在生成的文件中进行缺失符号检查。若没有定义，链接器会在符号缺失时生成错误信息。

- `LOCAL_ARM_MODE`: 可选参数，ARM机器体系结构特有变量，用于指定要生成的ARM 二进制类型。默认情况下，构建系统在拇指模式下用16位指令生成，但该变量可以被设置为 arm 来指定使用 32 位指令。

```
LOCAL_ARM_MODE := arm
```
该变量改变了整个模块的构建系统行为；可以用 .arm扩展名指定只在 arm 模式下构建特定文件。

```
LOCAL_SRC_FILES :=file1.c file2.c.arm
```

- `LOCAL_ARM_NEON`: 可选参数，ARM机器体系结构特有变量，用来指定在源文件中应该使用的 ARM 高级单指令多数据流(Single Instruction Multiple Data,SIMD)(a.k.a. NEON)内联函数。

```
LOCAL_ARM_NEON := true
```
该变量改变了整个模块的构建系统行为；可以用.neon 扩展名指定只构建带有NEON内联函数的特定文件。

```
LOCAL_SRC_FILES :=file1.c file2.c.neon
```
- `LOCAL_DISABLE_NO_EXECUTE`:可选变量，用来禁用 NX Bit 安全特性。 NX Bit代表 Never Execute(永不执行)，它是在CPU中使用的一项技术，用来隔离代码区和存储区。这样可以防止恶意软件通过将它的代码插入应用程序的存储区来控制应用程序。

```
LOCAL_DISABLE_NO_EXECUTE :=true
```

- `LOCAL_EXPORT_CFLAGS`: 该变量记录一组编译器标志，这些编译器标志会被添加到通过变量`LOCAL_STATIC_LIBRARIES`或`LOCAL_SHARED_LIBRARIES`使用本模块的其他模块的LOCAL_CFLAGS 定义中。

```
LOCAL_MODULE := avilib
...
LOCAL_EXPORT_CFLAGS := -DENABLE_AUDIO
...
LOCAL_MODULE := module1
LOCAL_CFLAGS := -DDEBUG
...
LOCAL_SHARED_LIBRARIES := avilib
```
编译器在构建 module1 时会以-DENABLE_AUDIO -DDEBUG 标志执行。

- `LOCAL_EXPORT_CPPFLAGS`： 和`LOCAL_EXPORT_CFLAGS`一样，但是它是C++特定代码编译器标志。
- `LOCAL_EXPORT_LDFLAGS`: 和 `LOCAL_EXPORT_CFLAGS`一样，但用作链接器标志。
- `LOCAL_EXPORT_C_INCLUDES`:该变量允许记录路径集，这些路径会被添加到通过变量`LOCAL_STATIC_LIBRARIES` 或 `LOCAL_SHARED_LIBRARIES` 使用该模块的 `LOCAL_C_INCLUDES` 定义中。
- `LOCAL_SHORT_COMMANDS`: 对于有大量资源或独立的静态/共享库的模块，该变量应该被设置为 true. 诸如Windows 之类的操作系统只允许命令行最多输入8191个字符；该变量通过分解构建命令使其长度小于8191个字符。在较小的模块中不推荐使用该方法，因为使用它会让构建过程变慢。
- `LOCAL_FILTER_ASM`:该变量定义了用于过滤来自`LOCAL_SRC_FILES`变量的装配文件的应用程序。

---

包含条件操作的构建文件 Android.mk

```
...
ifeq ($(TARGET_ARCH), arm)
	LOCAL_SRC_FILES += armonly.c
else
	LOCAL_SRC_FILES += generic.c
endif
...

```

### Application.mk

以下是Application.mk 构建文件指出的变量：

- `APP_MODULES`: 默认情况下，Android NDK 构建系统构建 Android.mk 文件声明的所有模块。该变量可以覆盖上述行为并提供一个空格分开的、需要被构建的模块列表。
- `APP_OPTIM`: 该变量可以被设置为 release 或 debug 以改变生成的二进制文件的优化级别。默认情况下使用的是 release 模式，并且此时生成的二进制文件被高度优化。该变量可以被设置为debug模式以生成更容易调试的未优化二进制文件。
- `APP_CLAGS`: 该变量列出了一些编译器标志，在编译任何模块的 C 和 C++源文件时这些标志都会被传给编译器。
- `APP_CPPFLAGS`: 该变量列出了一些编译器标志，在编译任何模块的 C++ 源文件时这些标志都会被传给编译器。
- `APP_BUILD_SCRIPT`:默认情况下， Android NDK 构建系统在项目的 jni 子目录下查找 Android.mk 构建文件。可以用该变量改变上述行为，并使用不同的生成文件。
- `APP_ABI`:默认情况下，Android NDK构建系统为armeabi  ABI 生成二进制文件。可以用该变量改变上述行为，并为其他 ABI 生成二进制文件，例如：

```
APP_ABI :=mips
另外， 可以设置多个 ABI 
APP_ABI := armeabi mips

为所有支持的ABI生成二进制文件
APP_ABI := all
```

- `APP_STL`: 默认情况下， Android NDK 构建系统使用最小 STL 运行库，也被称为system库。可以用该变量选择不同的STL实现。

```
APP_STL :=stlport_shared
```
- `APP_GNUSTL_FORCE_CPP_FEATURES`: 与 `LOCAL_CPP_EXTENSIONS` 变量相似，该变量表明所有模块都依赖于具体的C++特性， 如RTTI 、exceptions 等。
- `APP_SHORT_COMMANDS`: 与 `LOCAL_SHORT_COMMANDS` 变量相似，该变量使得构建系统在有大量源文件的情况下可以在项目中使用更短的命令。

	