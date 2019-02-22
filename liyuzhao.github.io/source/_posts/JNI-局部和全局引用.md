---
title: JNI-局部和全局引用
date: 2019-01-04 12:44:53
tags: [java, jni, android]
categories: android
---

### 局部引用和全局引用

JNI支持三种引用：局部引用、全局引用和弱全局引用。

#### 局部引用

大多数JNI函数返回局部应用。局部引用不能在后续的调用中被缓存及重用，主要因为它们的使用期限仅限于原生方法，一旦原生函数返回，局部引用即被释放。例如：FindClass 函数返回一个局部引用，当原生方法返回时，它被自动释放，也可以用DeleteLocalRef 函数显式的是否原生代码。

```
删除一个局部引用
jclass clazz;
clazz = (*env)->FindClass(env, "java/lang/String");
...
(*env)->DeleteLocalRef(env, clazz);
```
根据JNI的规范，虚拟机应该允许原生代码创建最少16个局部引用。在单个方法调用时进行多个内存密集型操作的最佳实践是删除未用的局部引用。如果不可能，原生代码可以在使用之前用 EnsureLocalCapacity 方法请求更多的局部引用槽。

#### 全局引用

全局引用在原生方法的后续调用过程中依然有效，除非它们被原生代码显式释放。

* 1.创建全局引用

可以用 NewGlobalRef 函数将局部引用初始化为全局引用。

```
用给定的局部引用创建全局引用
jclass localClazz;
jclass globalClazz;
...
localClazz = (*env)->FindClass(env, "java/lang/String");
globalClazz = (*env)->NewGlobalRef(env, localClazz);
...
(*env)->DeleteLocalRef(env, localClazz);
```

* 2.删除全局引用

当原生代码不再需要一个全局引用时，可以随时用 DeleteGlobalRef 函数释放它，如下：

```
//删除一个全局应用
(*env)->DeleteGlobalRef(env, globalClazz);
```

#### 弱全局引用

全局引用的另一种类型是弱全局引用。与全局引用一样，弱全局引用在原生方法的后续调用过程中依然有效，与全局引用不同，弱全局引用并不阻止潜在的对象被垃圾收回。

* 1.创建弱全局引用

可以用 NewWeakGlobalRef 函数对弱全局引用进行初始化。

```
	jclass weakGlobalClazz;
	weakGlobalClazz = (*env)->NewWeakGlobalRef(env, localClazz);
```

* 2.弱全局引用的有效性检验

可以用 IsSameObject 函数检验一个弱全局引用是否仍然指向活动的类实例。

```
	检验弱全局变量是否仍然有效
	if(JNI_FALSE == (*env)->IsSameObject(env, weakGlobalClazz, NULL)){
		/** 对象仍然处于活动状态且可以使用 */
	} else {
		/** 对象被垃圾回收器收回，不能使用 */
	}
```

* 3.删除弱全局引用

可以随时用 DeleteWeakGlobalRef 函数是否弱全局引用。

```
删除一个弱全局引用

(*env)->DeleteWeakGlobalRef(env, weakGlobalClazz);

```
全局引用显式释放前一直有效，他们可以被其他原生函数及原生线程使用。
