---
title: Android中通过反射获取资源ID
date: 2017-12-05 17:19:32
tags: [android]
categories: android
---

某些时候，自己把代码打包成工具jar包，但jar包中可能引用到res中的资源，这时候不能将资源一起打包，只能通过反射机制动态获取

```
/**
 * 通过反射获取组件的id号
 */
public static int getCompentID(String packageName, String className,String idName) {
	int id = 0;
	try {
		Class<?> cls = Class.forName(packageName + ".R$" + className);
		id = cls.getField(idName).getInt(cls);
	} catch (Exception e) {
		LogUtil.LogPrint(LogUtil.LOG_ERROR, "缺少" + idName + "文件!");
		e.printStackTrace();
	}
	return id;
}

```