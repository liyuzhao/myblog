---
title: JNI-数据类型
date: 2019-01-03 17:36:46
tags: [jni, java, android]
categories: android
---

### JNI的数据类型

Java 中有两种数据类型：

- 基本数据类型：布尔型、字节型、字符型、短整型、整型、长整型、浮点型和双精度类型。
- 引用类型：字符串类型、数组类及其他类。

<!-- more -->

Java 基本数据类型

|Java 类型| JNI类型| C/C++类型| 大小 |
|:--:|:--:|:--:|:--:|
|boolean| jboolean|unsigned char| 无符号8位|
|byte|jbyte|char| 有符号8位|
|char|jchar|unsigned short| 无符号16位|
|short|jshort|short|有符号16位|
|int|jint|int|有符号32位|
|long|jlong|long long|有符号64位|
|float|jfloat|float|32位|
|double|jdouble|double|64位|

Java 引用类型映射

|Java 类型| 原生类型|
|:--:|:--:|
|java.lang.Class|jclass|
|java.lang.Throwable|jthrowable|
|java.lang.String|jstring|
|Other objects|jobjects|
|java.lang.Object]|jobjectArray|
|boolean[]|jbooleanArray|
|char[]|jcharArray|
|short[]|jshortArray|
|byte[]|jbyteArray|
|char[]|jcharArray|
|short[]|jshortArray|
|int[]|jintArray|
|long[]|jlongArray|
|float[]|jfloatArray|
|double[]|jdoubleArray|
|Other arrays|jarray|

#### 对引用数据类型的操作

##### 字符串操作

JNI把 Java 字符串当成应用类型处理。这些引用类型并不像原生 C 字符串一样可以直接使用， JNI 提供了 Java 字符串与C字符串之间相互转化的必要函数。因为Java字符串对象是不可变的，因此JNI不提供任何修改现有的Java字符串内容的函数。

JNI支持Unicode编码格式和UTF-8编码格式的字符串，还提供两组函数通过JNIEnv接口指针处理这些字符串编码。

* 1.创建字符串
	
	可以在原生代码中用 NewString 函数构建Unicode编码格式的字符串实例，用NewStringUTF函数构建UTF-8编码格式的字符串实例。这些函数以一个C字符串为参数，并返回一个Java字符串引用类型jstring值。

	```
jstring javaString;
javaString = (*env)->NewStringUTF(env, "Hello world!");
```
	在内存溢出的情况下，这些函数返回NULL以通过原生代码虚拟机中抛出异常，这样原生代码就会停止运行。

* 2.把 Java 字符串转换成 C 字符串

	为了在原生代码中使用 Java 字符串，需要先将 Java 字符串转换成 C 字符串。用 GetStringChars 函数可以将Unicode格式的Java字符串转换成 C 字符串， 用GetStringUTFChars 函数可以将UTF-8格式的Java字符串转换成C字符串。 这些函数的第三个参数均为可选参数，该可选参数名是isCopy, 它让调用者确定返回的C字符串地址指向副本还是指向堆中的固定对象。
	
	```
				将Java字符串转换成C字符串
		
		const jbyte* str;
		jboolean isCopy;
		
		str = (*env)->GetStringUTFChars(env, javaString, &isCopy);
		if(0 != str){
			printf("Java string: %s", str);
			
			if(JNI_TRUE == isCopy){
				printf("C string is a copy of the Java string.")
			} else {
				printf("C string points to actual string.");
			}
		}
	
	```
* 3.释放字符串

	通过JNI GetStringChars 函数和 GetStringUTFChars 函数获得的C字符串在原生代码中使用完之后需要正确地释放，否则将会引起内存泄露。 JNI提供了 ReleaseStringChars 函数释放 Unicode编码格式的字符串，而用ReleaseStringUTFChars函数释放UTF-8编码格式的字符串。
	
	```
	释放 JNI 函数返回的 C 字符串
	(*env)->ReleaseStringUTFChars(env, javaString, str);
	```
	
##### 数组操作

JNI 把 Java 数组当成引用类型来处理，JNI提供必要的函数访问和处理Java数组。

* 1.创建数组

	用 New&lt;Type&gt;Array 函数在原生代码中创建数组实例，其中&lt;Type&gt;可以是 Int, Char 和 Boolean 等，例如 NewIntArray。使用这些函数时应该以参数的形式给出数组的大小。
	
	```
		在原生代码中创建数组
		
		jintArray javaArray;
		javaArray = (*env)->NewIntArray(env, 10);
		if(0 != javaArray){
			/* 现在可以使用数组了。 */
		}
		
	```
	与 NewString 函数一样，在内存溢出的情况下，New&lt;Type&gt;Array 函数将返回 NULL 以通知原生代码虚拟机中有异常抛出，这样原生代码就会停止运行。
* 2.访问数组元素

	JNI 提供两种访问 Java 数组元素的方法，可以将数组的代码复制成C数组或者让JNI提供直接指向数组元素的指针。
	
* 3.对副本的操作

	Get&lt;Type&gt;ArrayRegion 函数将给定的基本Java数组复制到给定的C数组中。
	
	```
		将Java数组区复制到C数组中
		jint nativeArray[10];
		(*env)->GetIntArrayRegion(env, javaArray, 0, 10, nativeArray);
	```
	原生代码可以像使用普通的C数组一样使用和修改数组元素。当原生代码想将所做的修改提交给Java数组时，可以使用 Set&lt;Type&gt;ArrayRegion 函数将C数组复制回Java数组中。
	
	```
		从C数组向Java数组提交所作的修改
		
		(*env)->SetIntArrayRegion(env, javaArray, 0, 10, nativeArray);
	```
	当数组很大时，为了对数组进行操作而复制数组会引起性能问题。在这种情况下，如果可能的话，原生代码只获取或设置数组元素区域而不是获取整个数组。另外，JNI 提供了不同的函数集以获得数组元素而非其副本的直接指针。
	
* 4.对直接指针的操作
	
	可能的话，原生代码可以用Get&lt;Type&gt;ArrayElements函数获取指向数组元素的直接指针。函数带有三个参数，第三个参数是可选参数，该可选参数名是isCopy, 让调用者确定返回的C字符串地址指向副本还是指向堆中的固定对象。
	
	```
		获得指向Java数组元素的直接指针
		
		jint* nativeDirectArray;
		jboolean isCopy;
		
		nativeDirectArray = (*env)->GetIntArrayElements(env, javaArray, &isCopy);
	```
	因为可以像普通的C数组一样访问和处理数组元素，因此JNI没提供访问和处理数组元素的方法，JNI 要求原生代码用完这些指针立即释放，否则会出现内存溢出。原生代码可以使用JNI提供的 Release&lt;Type&gt;ArrayElements函数释放 Get&lt;Type&gt;ArrayElements函数返回C数组。
	
	```
			释放指向Java数组元素的直接指针
			(*env)->ReleaseIntArrayElements(env, javaArray, nativeDirectArray, 0);
	```
	该函数带有四个函数，第四个函数是释放模式，下面列出了支持的释放模式列表。
	
|释放模式|动作|
|:--:|:--:|
|0| 将内容复制回来并是否原生数组|
|JNI_COMMIT| 将内容复制回来但不释放原生数组，一般用于周期性地更新一个Java数组|
|JNI_ABORT| 释放原生数组但不用将内容复制回来|
	
	
##### NIO操作
原生I/O（NIO）在缓冲管理区、大规模网络和文件I/O及字符集支持方面的性能有所改进。JNI提供了在原生代码中使用NIO的函数。与数组操作相比，NIO缓冲区的数据传送性能较好，更适合在原生代码和Java应用程序之间传送大量数据。

* 1.创建直接字节缓冲区

	原生代码可以创建Java应用程序使用的直接字节缓冲区，该过程是以一个原生C字节数组为基础，下面列出了NewDirectByteBuffer的使用。
	
	```
		基于给定的C字节数组创建字节缓冲区
		
		unsigned char* buffer = (unsigned char*) malloc(1024);
		...
		jobject directBuffer;
		directBuffer = (*env)->NewDirectByteBuffer(env, buffer, 1024);
		
	```
	**注意**
	
	原生方法中的内存分配超出了虚拟机的管理范围，且不能用虚拟机的垃圾回收器回收原生方法中的内存。原生函数应该通过释放未使用的内存分配以避免内存泄露来正确管理内存。
	
* 2.直接字节缓冲区获取

	Java应用程序中也可以创建直接字节缓冲区，在原生代码中调用 GetDirectBufferAddress 函数可以获得原生字节数组的内存地址。
	
	```
		通过Java字节缓冲区获取原生字节数组
		
		unsigned char* buffer;
		buffer = (unsigned char*)(*env)->GetDirectBufferAddress(env, directBuffer);
		
	```
	
##### 访问域

Java 有两类域：实例域和静态域。类的每个实例都有自己的实例域副本，而一个类的所有实例共享同一个静态域。

JNI提供了访问两类域的函数，下面代码显示了带有静态域和实例域的Java类

```
	带有静态域和实例域的 Java 类
	
	public class JavaClass {
		/** 实例域 */
		private String instanceField = "Instance Field";
		/** 静态域 */
		private static String staticField = "Static Field";
		...
	}
```

* 1.获取域ID

	JNI提供了用域ID访问两类域的方法，可以通过给定实例的class对象获取域ID,用GetObjectClass 函数获得 class 对象，
	
	```
		用对象引用获取类
		jclass clazz;
		clazz = (*env)->GetObjectClass(env, instance);
		
	```
	有两个获得域ID的函数分别适用于不同类型域，GetFieldId 函数用于获取实例域，如下：
	
	```
		获取实例域的域ID
		
		jfieldID instanceFieldId;
		instanceFieldId = (*env)->GetFieldID(env, clazz, "instanceField", "Ljava/lang/String;")
		
	```
	
	GetStaticFieldId用于获取静态域ID， 这两个函数均返回 jfieldID 类型的域ID.
	
	```
		获得静态域的域ID
		
		jfieldID staticFieldId;
		
		staticFieldId = (*env)->GetStaticFieldID(env, clazz, "staticField", "Ljava/lang/String;");
		
	```
	两个函数的最后一个参数是Java中表示域类型的域描述符。在上述示例代码中，"Ljava/lang/String" 表明域类型是String。
	
	>为了提高应用程序的性能，可以缓存域ID。一般总是缓存使用最频繁的域ID。
	
* 2.获取域

	在获得域ID之后，可以用Get&lt;Type&gt;Field 函数获得实际的实例域，
	
	```
		获得实例域
		
		jstring instanceField;
		instanceField = (*env)->GetObjectField(env, instance, instanceFieldId);
	
	```
	用GetStatic&lt;Type&gt;Field函数获得静态域
	
	```
		获得静态域
		jstring staticField;
		staticField = (*env)->GetStaticObjectField(env, clazz, staticFieldId);
	
	```
	在内存溢出的情况下，这些函数均返回NULL，此时原生代码不会继续执行。
	
	>获得单个域值需要调用两到三个JNI函数，原生代码回到Java中获取每个单独的域值，这给应用程序增加了额外的负担，进而导致性能下降。强烈建议将所有需要的参数传递给原生方法调用，而不是让原生代码回到Java中。
	
##### 调用方法

与域一样，Java中有两类方法：实例方法和静态方法。JNI 提供访问两类方法的函数， 如下代码给出了含有一个静态方法和一个实例方法的Java类。

```
	带有静态方法和实例方法的Java类
	
	public class JavaClass {
		/**
		 * 实例方法
		 */	
		private String instanceMethod(){
			return "instance Method";
		}
		
		/**
		 * 静态方法
		 */
		private static String staticMethod(){
			return "Static Method";
		}
		...
	}
```

* 1.获取方法ID

	JNI 提供了用方法ID访问两类方法的途径，可以用给定的实例的 class 对象获得方法ID。 用GetMethodID 函数获得实例方法的方法ID,
	
	```
		获得实例方法的方法ID
		jmethodID instanceMethodId;
		instanceMethodId = (*env)->GetMethodID(env, clazz, "instanceMethod", "()Ljava/lang/String;");
	```
	用GetStaticMethodID 函数获得静态域的方法ID， 两个函数均返回 jmethodID 类型的方法ID.
	
	```	
		获得静态方法的方法ID
		
		jmethodID staticMethodId;
		staticMethodId = (*env)->GetStaticMethodID(env, clazz, "staticMethod", "()Ljava/lang/String;");
	
	```
	与字段ID获取方法一样，两个函数的最后一个参数均表示方法描述符，在Java中它表示方法签名。
	
	>为了提升应用程序性能，可以缓存方法ID。一般总是缓存使用最频繁的方法ID。
	
* 2.调用方法

	可以以方法ID为参数通过Call&lt;Type&gt;Method 类函数调用实际的实例方法， 如下：
	
	```
		调用实例方法
		jstring instanceMethodResult;
		instanceMethodResult = (*env)->CallStringMethod(env, instance, instanceMethodId);
	```
	用CallStatic&lt;Type&gt;Field类函数调用静态方法，如下：
	
	```
		调用静态方法
		jstring staticMethodResult;
		staticMethodResult = (*env)->CallStaticStringMethod(env, clazz, staticMethodId);
	
	```
	在内存溢出的情况下，这些函数均返回NULL，此时原生代码不会继续执行。
	
	>Java和原生代码之间的转换是代价较大的操作，强烈建议规划Java代码和原生代码的任务时考虑这种代价，最小化这种转换可以大大提高应用程序的性能。
	
* 3.域和方法的描述符

下表为 Java类型签名映射
	
|Java 类型| 签名 |
|:--:|:--:|
|Boolean | Z|
|Byte|B|
|Char|C|
|Short|S|
|Int|I|
|Long|J|
|fully-qualified-class|Lfully-qualified-class;|
|type[]|[type|
|method type|(arg-type)ret-type|
	
>用类型签名映射手工生成域和方法描述符并让它们与Java代码同步是一件非常繁琐的任务。通常都是借助Java的类文件反汇编程序：javap
	
	