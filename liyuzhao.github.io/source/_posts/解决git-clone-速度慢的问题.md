---
title: 解决git clone 速度慢的问题
date: 2020-09-15 19:10:48
tags: [github, git]
categories: git
---

最近从github clone一些项目的时候速度极慢，完全受不了，从网上look了很多办法，都以失败告终，直到看到了一篇文章...

[跳转](https://blog.csdn.net/weixin_42886104/article/details/106454331)

<!-- more -->

解决方法：

```
使用国内镜像，目前已知Github国内镜像网站有github.com.cnpmjs.org和hub.fastgit.org/。速度根据各地情况而定，

在clone某个项目的时候将github.com替换为github.com.cnpmjs.org即可。

```

示例：

```
//这是我们要clone的
git clone https://github.com/Hackergeek/architecture-samples
 
//使用镜像
git clone https://github.com.cnpmjs.org/Hackergeek/architecture-samples
 
//或者
 
//使用镜像
git clone https://hub.fastgit.org/Hackergeek/architecture-samples

```