---
title: Integer比较的误区
date: 2018-03-13 14:57:21
tags: [integer, java]
categories: [java]
---

## Integer比较时不准确的原因

<!-- more -->

首先理解的是：Java的装箱和拆箱
 
##### 什么时候会进行装箱
>装箱用到的方法：Integer.valueOf()

>Integer i = 100;

##### 什么是会进行拆箱
>如果其中有一个操作数是表达式(即包含算术运算)则比较的是数值(即会触发自动拆箱的过程)，例如：a + b

##### Integer.valueOf()中的cache问题

```
public static Integer valueOf(int i){
	if(i >= - 128 && i <= IntegerCache.high)
		return IntegerCache.cache[i + 128];
	else
		return new Integer(i);
}


```
> 如果数值在[-128, 127]之间，便返回指向IntegerCache.cache中已经存在的对象的引用

##### == 的比较问题
> 当“==”运算符的两个操作数都是包装类型的引用，则是比较指向的是否是同一个对象，而如果其中有一个操作数是表达式(即包含算术运算)则比较的是数值(即会触发自动拆箱的过程)。

例如：

```
	Integer a = 1;
	Integer b = 2;
	Integer c = 3;
	Integer d = 3;
	Integer e = 321;
	Integer f = 321;
	Long g = 3L;
	Long h = 2L;
	
	System.out.println(c == d); ----true  在[-128, 127]区间，对象相同
	System.out.println(e == f); ---- false 不在[-128, 127]区间，对象不同
	System.out.println(c == (a + b)); ---- true 包含运算符，拆箱比较值
	System.out.println(c.equals(a + b)); --- true 先拆箱(a + b),再装箱
	System.out.println(g == (a + b)); --- true 区间缓存
	System.out.println(g.equals(a + b)); --- false  对象类型不同
	System.out.println(g.equals(a + h)); --- true a+h 向上转换成long类型，再装箱 

```