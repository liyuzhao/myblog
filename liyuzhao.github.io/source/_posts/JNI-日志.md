---
title: JNI-日志
date: 2019-01-07 16:56:32
tags: [android, ndk, jni, java]
categories: android
---

#### 框架

Android 日志框架把日志消息分成4个日志缓冲区：

- Main: 主要应用程序的日志信息
- Event: 系统事件
- Radio: Radio相关的日志信息
- System: 调试时产生的低级系统调试信息

这 4 个缓冲区以伪设备的形式保存在/dev/log 系统目录中。因为移动平台上的 I/O 操作代价很大，所以日志信息要保存到内存中，而不能保存在永久性存储器(例如磁盘)中。为了有效控制对存储日志信息的内存空间的利用， logger 模块把日志信息放在固定大小的缓冲区。 Main、radio 和 system 日志以自由文本的格式保存在 64KB的日志缓存区中。事件日志信息带有额外的二进制形式信息，因此保存在256KB的日志缓存区中。

<!-- more -->

#### 原生日志API

开发者不希望直接与Logger内核模块进行交互。 Android 运行库系统提供了一组 API 调用以便于 Java 代码和原生代码向 logger 内核模块发送日志信息。通过 android/log.h 头文件来展示原生代码的日志 API。为了使用日志函数，原生代码需要先包含该头文件

```
#include <android.h>
```
除了要包含合适的头文件，还需要动态修改 Android.mk 文件从而将原生模块与日志库进行链接，可以通过构建系统变量 `LOCAL_LDLIBS` 完成该操作， 该构建系统变量必须被放在共享库构建片段的include语句之前；否则它将不起作用。

```
动态链接原生模块与日志库
LOCAL_MODULE := hello-jni
...
LOCAL_LDLIBS += -llog
...
include $(BUILD_SHARED_LIBRARY)
```
##### 1.日志消息
通过日志API发送给logger 模块的每个日志条目都具有以下字段：
* Priority: 取值分别为：verbose、debug、info、warning、error 和 fatal,表示日志信息的重要程度。支持的日志优先级在android/log.h头文件中声明，

```
//支持的日志优先级
typedef enum android_LogPriority {
...
ANDROID_LOG_VERBOSE,
ANDROID_LOG_DEBUG,
ANDROID_LOG_INFO,
ANDROID_LOG_WARN,
ANDROID_LOG_ERROR,
ANDROID_LOG_FATAL,
...
} android_LogPriority;

```
* Tag: 标识产生日志信息的组件，Logcat 和 DDMS 工具可以基于这个标签值过滤日志信息。标签值应该尽可能小。
* Message: 用于存放实际日志信息。在每一个日志消息后自动追加一个换行符，因为循环的日志缓存区很小，因此强烈建议应用程序的日志信息大小尽量保持合适。

##### 2.日志函数
`android/log.h`头文件也声明了一系列函数，这些函数主要用于原生代码生成日志消息。

* `_android_log_write`:可用于生成一个简单的字符串作为日志信息。它包括日志优先级、日志标签和日志消息。

```
//生成简单的日志消息
_android_log_write(ANDROID_LOG_WARN, "hello-jni", "warning log.");
```

* `_android_log_print`:可用于生成一个格式化字符串为日志消息。它包括日志优先级、日志标签、字符串格式和格式中指定个数的其他参数， 

```
//生成格式化的日志消息
_android_log_print(ANDROID_LOG_ERROR, "hello-jni", "Failed with errno %d", errno);
```

* `_android_log_vprint`: 除了参数传递方式外，其他功能与`_android_log_print` 函数完全相同，`_android_log_vprint` 函数用va_list 传递附加参数， 而 `_android_log_print` 函数中以连续参数的方式改为传递参数。如果想要调用日志函数时给当前函数的参数个数动态变化时，该函数的优势就会体现出来。

```
// 传递的参数个数变化时生成日志消息
void log_verbose(const char* format, ...){
	va_list args;
	
	va_start(args, format);
	_android_log_vprint(ANDROID_LOG_VERBOSE, "hello-jni", format, args);
	va_end(args);
}
...
void example()
{
	log_verbose("Errno is now %d", errno);
}
```

* `_android_log_assert`: 用于记录断言失败。与其他的日志函数比较，它不包括日志优先级，但总是将所有的日志记录为fatal, 如果附加了调试器，它也SIGTRAP 当前进程以通过 debugger 进一步检查。

```
// 生成断言失败日志
if(0!=errno){
	_android_log_assert("0!=errno", "hello-jni", "There is an error.");
}

```

#### 受控制的日志

在jni目录下，添加文件`my_log.h`

```
#pragma once
/**
 * NDK的基本日志框架
 */

#include <android/log.h>

#define MY_LOG_LEVEL_VERBOSE 1
#define MY_LOG_LEVEL_DEBUG 2
#define MY_LOG_LEVEL_INFO 3
#define MY_LOG_LEVEL_WARNING 4
#define MY_LOG_LEVEL_ERROR 5
#define MY_LOG_LEVEL_FATAL 6
#define MY_LOG_LEVEL_SILENT 7

#ifndef MY_LOG_TAG
#	define MY_LOG_TAG  __FILE__
#endif

#ifndef MY_LOG_LEVEL
#	define MY_LOG_LEVEL	MY_LOG_LEVEL_VERBOSE
#endif

#define MY_LOG_NOOP (void) 0

#define MY_LOG_PRINT(level, fmt, ...) \
	_android_log_print(level, MY_LOG_TAG, "(%s:%u) %s: " fmt, \
			__FILE__, __LINE__, __PRETTY_FUNCTION__, ##__VA_ARGS__)

#if MY_LOG_LEVEL_VERBOSE >= MY_LOG_LEVEL
#	define MY_LOG_VERBOSE(fmt,...) \
	MY_LOG_PRINT(ANDROID_LOG_VERBOSE, fmt, ##__VA_ARGS__)
#else
#	define MY_LOG_VERBOSE(...) MY_LOG_NOOP
#endif


#if	MY_LOG_LEVEL_DEBUG >= MY_LOG_LEVEL
#	define MY_LOG_DEBUG(fmt, ...) \
	MY_LOG_PRINT(ANDROID_LOG_DEBUG, fmt, ##__VA_ARGS__)
#else
#	define MY_LOG_DEBUG(...) MY_LOG_NOOP
#endif

#if MY_LOG_LEVEL_INFO >= MY_LOG_LEVEL
#	define MY_LOG_INFO(fmt, ...) \
	MY_LOG_PRINT(ANDROID_LOG_INFO, fmt, ##__VA_ARGS__)
#else
#	define MY_LOG_INFO(...) MY_LOG_NOOP
#endif

#if MY_LOG_LEVEL_WARNING >= MY_LOG_LEVEL
#	define MY_LOG_WARNING(fmt, ...) \
	MY_LOG_PRINT(ANDROID_LOG_WARN, fmt, ##__VA_ARGS__)
#else
#	define MY_LOG_WARING(...) MY_LOG_NOOP
#endif

#if MY_LOG_LEVEL_ERROR >= MY_LOG_LEVEL
#	define MY_LOG_ERROR(fmt, ...) \
	MY_LOG_PRINT(ANDROID_LOG_ERROR, fmt, ##__VA_ARGS__)
#else
#	define MY_LOG_ERROR(...) MY_LOG_NOOP
#endif

#if MY_LOG_LEVEL_FATAL >= MY_LOG_LEVEL
#	define MY_LOG_FATAL(fmt, ...) \
	MY_LOG_PRINT(ANDROID_LOG_FATAL, fmt, ##__VA_ARGS__)
#else
#	define MY_LOG_FATAL(...) MY_LOG_NOOP
#endif

#if MY_LOG_LEVEL_FATAL >= MY_LOG_LEVEL
#	define MY_LOG_ASSERT(expression, fmt, ...) \
	if(!(expression)) \
	{ \
		__android_log_assert(#expression, MY_LOG_TAG, \
				fmt, ##__VA_ARGS__); \
	}
#else
#	define MY_LOG_ASSERT(...) MY_LOG_NOOP
#endif


```

现在可以在原生函数中添加日志声明语句。
>在原生函数中添加日志声明语句

```
jstring Java_com.example_hellojni_HelloJni_stringFromJNI(JNIEnv* env, jobject thiz)
{
	MY_LOG_VERBOSE("The stringFromJNI is called.");
	MY_LOG_DEBUG("env=%p thiz=%p", env, thiz);
	MY_LOG_ASSERT(0 != env, "JNIENV cannot be NULL.");
	MY_LOG_INFO("Returning a new string.");
	
	return (*env)->NewStringUTF(env, "Hello from JNI!");
}

```

更新 `Android.mk`

* 1.日志标签

> 通过 MY_LOG_TAG 构建变量定义日志标签

```
LOCAL_MODULE := hello-jni
...
# 定义日志标签
MY_LOG_TAG := \"hello-jni\"

```

* 2.日志等级

>定义默认的日志等级

```
LOCAL_MODULE := hello-jni
...
# 定义日志标签
MY_LOG_TAG := \"hello-jni\"

# 定义基于构建类型的默认日志等级
ifeq ($(APP_OPTIM), release)
	MY_LOG_LEVEL := MY_LOG_LEVEL_ERROR
else
	MY_LOG_LEVEL := MY_LOG_LEVEL_VERBOSE
endif

```

* 3.应用日志配置

在定义`MY_LOG_TAG` 和 `MY_LOG_LEVEL` 构建系统变量时，可以将日志系统配置应用在模块中。

```
// 将日志系统配置应用在模块中

LOCAL_MODULE := hello-jni
...
# 定义日志标签
MY_LOG_TAG := hello-jni
# 定义基于构建类型的默认日志等级
ifeq ($(APP_OPTIM), release)
	MY_LOG_LEVEL := MY_LOG_LEVEL_ERROR
else
	MY_LOG_LEVEL := MY_LOG_LEVEL_VERBOSE
endif

# 追加编译标记
LOCAL_CFLAGS += -DMY_LOG_TAG=$(MY_LOG_TAG)
LOCAL_CFLAGS += -DMY_LOG_LEVEL=$(MY_LOG_LEVEL)

# 动态链接日志库
LOCAL_LDLIBS += -llog

```


##### 控制台日志

默认情况下，控制台文件描述符--STDOUT 和 STDERR 在Android 平台上是不可见的。要想将这些日志消息重定向到 Android 系统日志中， 需要打开一个命令提示符或一个终端窗口，并执行如下所示的ADB命令。

```
// 将控制台日志重定向到 Android 系统日志中
adb shell stop
adb shell setprop log.redirect-stdio true
adb shell start

```
在重新启动应用程序时，用Logcat视图可以看到控制台日志信息。
