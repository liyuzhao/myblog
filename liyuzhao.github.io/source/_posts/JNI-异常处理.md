---
title: JNI-异常处理
date: 2019-01-04 12:22:10
tags: [java, jni, android]
categories: android
---

### 异常处理

#### 捕获异常

JNIEnv 接口提供了一组与异常相关的函数集，在运行过程中可以使用Java类查看这些函数。
<!-- more -->

```
抛出异常的 Java 例子
public class JavaClass {
	/**
	 * 抛出方法.
	 */
	 private void throwingMethod() throws NullPointerException {
	 	throw new NullPointerException("Null pointer");
	 }
	 /**
	  * 访问方法(原生方法).
	  */
	 private native void accessMethods();
}
```
调用 throwingMethod 方法时， accessMethods 原生方法需要显示地做异常处理。 JNI提供了 ExceptionOccurred 函数查询虚拟机中是否有挂起的异常。在使用完之后，异常处理程序需要用 ExceptionClear 函数显式地清除异常。

```
原生代码中的异常处理
jthrowable ex;
...
(*env)->CallVoidMethod(env, instance, throwingMethodId);
ex = (*env)->ExceptionOccurred(env);
if (0 != ex) {
	(*env)->ExceptionClear(env);
	/* Exception handler. */
}
```

#### 抛出异常

JNI 也允许原生代码抛出异常。因为异常是Java类，应该先用FindClass函数找到异常类，用ThrowNew 函数可以初始化且抛出新的异常。

```
原生代码中抛出异常
jclass clazz;
...
clazz = (*env)->FindClass(env, "java/lang/NullPointerException");
if (0 != clazz) {
	(*env)->ThrowNew(env, clazz, "Exception message.");
}
```
因为原生函数的代码执行不受虚拟机的控制，因此抛出异常并不会停止原生函数的执行并把控制权转交给异常处理程序。到抛出异常时，原生函数应该释放所有已分配的原生资源，例如内存及合适的返回值等。通过 JNIEnv 接口获得的引用是局部引用且一旦返回原生函数，它们自动地被虚拟机释放。

