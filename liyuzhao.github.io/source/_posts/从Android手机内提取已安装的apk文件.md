---
title: 从Android手机内提取已安装的apk文件
date: 2018-04-09 14:44:50
tags: [apk, backup]
categories: android
---

假如你在其他手机上看到一个应用，又不知道从哪去下载，而那个手机的安装包已经被删除掉，那怎样安装一个和那个手机上一模一样的应用呢？办法就是从那个手机中备份一份，然后传给你。也许你见过一些其他软件有备份apk的功能，也是这个原理。

<!-- more -->

#### 找到具体的应用的包名

连接到手机，用adb命令执行如下命令：

```
	$ adb shell pm list package
```
输出结果为：

```
package:com.mediatek.gba
package:com.mediatek.ims
package:com.flyme.roamingpay
package:com.github.shadowsocks
package:org.simalliance.openmobileapi.uicc2terminal
package:com.android.providers.telephony
package:com.meizu.documentsui
package:com.goodix.fingerprint
package:com.android.providers.calendar
package:com.zgczw.chezhiwang
package:cc.mianfeinovel
package:com.android.providers.media
......
```

#### 找到需要备份的安装包的位置

如果要找到应用包名为：cc.mianfeinovel

可通过如下命令，找到具体安装包的位置：

```
$ adb shell pm path cc.mianfeinovel
```

输出结果为：

```
package:/data/app/cc.mianfeinovel-2/base.apk
```

#### 把安装包取出

可通过adb的pull命令：

```
$ adb pull /data/app/cc.mianfeinovel-2/base.apk
```
结果为：

```
/data/app/cc.mianfeinovel-2/base.apk: 1 file pulled. 5.7 MB/s (11417386 bytes in 1.913s)

```

此操作手机无需root,也就是说每个手机都可以这样操作。