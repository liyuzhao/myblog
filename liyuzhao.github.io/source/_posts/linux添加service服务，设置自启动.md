---
title: linux添加service服务，设置自启动
date: 2020-11-19 10:35:52
tags: [linux]
categories: linux
---

linux添加service服务，设置自启动

<!-- more -->

例如：注册一个oa-server的服务
创建文件： 在`/etc/init.d/oa-server`

```
#!/bin/sh
# chkconfig: 2345 80 90
# description: oa-server register server

case "$1" in
		start)
			sh /usr/local/idea/start.sh
			;;
		stop)
			ps -ef | grep oa-server | grep -v grep | awk '{print $2}' | xargs kill

esac
```

- 注意: `#chkconfig, #description 不要少，设置自启动需要`.





服务启动: service oa-server start

服务关闭: service oa-server shutdown

设置自启: chkconfig oa-server on

关闭自启: chkconfig oa-server off
