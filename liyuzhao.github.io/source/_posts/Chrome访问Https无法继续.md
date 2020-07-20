---
title: Chrome访问Https无法继续
date: 2020-01-08 15:45:26
tags: [mac, chrome]
categories: chrome
---

今天更新了Chrome版本，发现请求自签名证书的https网站，无法不安全继续访问。

<!-- more -->

Chrome当请求https网站时，如检测不到证书，则会报不安全访问。
在旧版本中，可以点击“高级”按钮，选择不安全访问，即可继续访问。

----

但在新版本的Chrome访问https网站时，点击“高级”按钮，并没有显示不安全继续访问，那这种情况下，怎样不影响继续访问呢？

在Chrome的新Tab中，输入如下：

```
chrome://net-internals/#hsts
```

在打开的界面中，选择`Domain Security Policy` 选项， 在最下面的Delete Domain security policies中的Domain文本框中，输入对应的IP/域名，点击“Delete”按钮后，刷新访问的页面，在“高级”选项中，可以看到继续按照不安全访问的按钮。




