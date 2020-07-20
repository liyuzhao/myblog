---
title: Linux搜索命令ag
date: 2019-09-24 10:25:34
tags: [linux, ag]
categories: linux
---

ag的the\_silver\_searcher介绍，搜索代码神器

<!-- more -->

the\_silver\_searcher是什么？ ag又是什么？

作者主页关于the\_silver\_searcher的说明：[http://geoff.greer.fm/ag/](http://geoff.greer.fm/ag/)

The Silver Searcher is a tool for searching code. It started off as a clone of Ack, but their feature sets have since diverged slightly. In typical usage, Ag is 5-10x faster than Ack. See the GitHub page for more info.

源代码：[https://github.com/ggreer/the\_silver\_searcher](https://github.com/ggreer/the_silver_searcher)


Mac 下安装：

``` 
brew install the_silver_searcher
```

Mac 下卸载：

```
brew uninstall the_silver_searcher
```

使用方法

```
ag 'hx' /www/t086.com

```

常用参数
-i 忽略大小写
-l 只列出文件名
-g 文件名匹配
--php 只搜索php文件
--ignore-dir 忽略目录

如：

```
ag --ignore-dir sitedata --php hx /www/9enjoy.com
```

更多

-h 看帮助吧