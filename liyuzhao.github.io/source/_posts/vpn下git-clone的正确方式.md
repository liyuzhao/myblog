---
title: vpn下git clone的正确方式
date: 2019-07-12 17:13:43
tags: [linux, git]
categories: git
---

在Mac系统中，使用了Shadowsocks开了代理，使用浏览器打开Google是正常的，但用git clone googlesource.com里面的代码，缺总是超时，怎样解决git也使用代理呢？

修改git配置即可，速度直接飙升10倍

```

git config --global http.proxy 'socks5://127.0.0.1:1080'

git config --global https.proxy 'socks5://127.0.0.1:1080'


```