---
title: AndroidStudio打包Jar
date: 2018-03-13 14:49:21
tags: [android, jar]
categories: [android]
---

## Android Studio 打包Jar

<!-- more -->
##### 需要在module的gradle中配置如下task
```
//Copy类型
task makeJar(type: Copy) {
    //删除存在的
    delete 'build/libs/myjar.jar'
    //设置拷贝的文件
    from('build/intermediates/bundles/release/')
    //打进jar包后的文件目录
    into('build/libs/')
    //将classes.jar放入build/libs/目录下
    //include ,exclude参数来设置过滤
    //（我们只关心classes.jar这个文件）
    include('classes.jar')
    //重命名
    rename('classes.jar', 'myjar.jar')
}

makeJar.dependsOn(build)

```

##### 在终端执行

```
gradlew markJar

```

##### 打包后的位置：module的/build/libs/下
具体位置自己可以修改

##### 可能的报错

>Fix the issues identified by lint, or add the following to your build script to proceed with errors
 
 
```
android {
	lintOptions {
		abortOnError false
	}
}

```