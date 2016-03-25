---
title: Mac 清除 DNS 缓存
date: 2016-03-18 19:28:30
tags: [mac, dns, 缓存]
categories: mac
---

# Mac 清除 DNS 缓存

<!-- more -->
打开 Terminal，根据你的系统版本运行对应的命令：

Mac OSX 10.11
> sudo killall -HUP mDNSResponder

Mac OSX 10.10.4以上
> sudo killall -HUP mDNSResponder

Mac OSX 10.10.0 - 10.10.3
> sudo discoveryutil mdnsflushcache

Mac OSX 10.7 & 10.8 & 10.9
> sudo killall -HUP mDNSResponder

Mac OSX 10.6
> dscacheutil -flushcache


