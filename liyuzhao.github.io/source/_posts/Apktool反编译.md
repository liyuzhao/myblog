---
title: Apktool反编译
date: 2018-04-08 13:41:44
tags: [apktool]
categories: android
---

Android的反编译，一般需要用到apktool，这里记录下当用到apktool时，基本的用法

<!-- more -->

Apktool反编译和回编为apk，主要是apktool.jar出力，在mac或linux上一般会有个apktool，来执行apktool.jar。如果没有的话，把如下代码保存为apktool即可

```
#!/bin/bash
#
# Copyright (C) 2007 The Android Open Source Project
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

# This script is a wrapper for smali.jar, so you can simply call "smali",
# instead of java -jar smali.jar. It is heavily based on the "dx" script
# from the Android SDK

# Set up prog to be the path of this script, including following symlinks,
# and set up progdir to be the fully-qualified pathname of its directory.
prog="$0"
while [ -h "${prog}" ]; do
    newProg=`/bin/ls -ld "${prog}"`

    newProg=`expr "${newProg}" : ".* -> \(.*\)$"`
    if expr "x${newProg}" : 'x/' >/dev/null; then
        prog="${newProg}"
    else
        progdir=`dirname "${prog}"`
        prog="${progdir}/${newProg}"
    fi
done
oldwd=`pwd`
progdir=`dirname "${prog}"`
cd "${progdir}"
progdir=`pwd`
prog="${progdir}"/`basename "${prog}"`
cd "${oldwd}"

jarfile=apktool.jar
libdir="$progdir"
if [ ! -r "$libdir/$jarfile" ]
then
    echo `basename "$prog"`": can't find $jarfile"
    exit 1
fi

javaOpts=""

# If you want DX to have more memory when executing, uncomment the following
# line and adjust the value accordingly. Use "java -X" for a list of options
# you can pass here.
# 
javaOpts="-Xmx512M"

# Alternatively, this will extract any parameter "-Jxxx" from the command line
# and pass them to Java (instead of to dx). This makes it possible for you to
# add a command-line parameter such as "-JXmx256M" in your ant scripts, for
# example.
while expr "x$1" : 'x-J' >/dev/null; do
    opt=`expr "$1" : '-J\(.*\)'`
    javaOpts="${javaOpts} -${opt}"
    shift
done

if [ "$OSTYPE" = "cygwin" ] ; then
	jarpath=`cygpath -w  "$libdir/$jarfile"`
else
	jarpath="$libdir/$jarfile"
fi

# add current location to path for aapt
PATH=$PATH:`pwd`;
export PATH;
exec java $javaOpts -Djava.awt.headless=true -jar "$jarpath" "$@"
```

#### 反编译apk

常用命令行为：

```
$ apktool d app-release.apk
I: Using Apktool 2.3.2 on app-release.apk
I: Loading resource table...
I: Decoding AndroidManifest.xml with resources...
I: Loading resource table from file: $HOME/Library/apktool/framework/1.apk
I: Regular manifest package...
I: Decoding file-resources...
I: Decoding values */* XMLs...
I: Baksmaling classes.dex...
I: Copying assets and libs...
I: Copying unknown files...
I: Copying original files...
$

```

它的其他语法还有：

```
$ apktool d foo.jar
// decodes foo.jar to foo.jar.out folder

$ apktool decode foo.jar
// decodes foo.jar to foo.jar.out folder

$ apktool d bar.apk
// decodes bar.apk to bar folder

$ apktool decode bar.apk
// decodes bar.apk to bar folder

$ apktool d bar.apk -o baz
// decodes bar.apk to baz folder
```

#### 回编译为apk

常用语法为：

```
apktool b app-release/ -o app-release_unsigned.apk

I: Using Apktool 2.3.2
I: Checking whether sources has changed...
I: Checking whether resources has changed...
I: Building resources...
I: Building apk file...
I: Copying unknown files/dir...
I: Built apk...

```
它的其他语法还有：

```
$ apktool b foo.jar.out
// builds foo.jar.out folder into foo.jar.out/dist/foo.jar file

$ apktool build foo.jar.out
// builds foo.jar.out folder into foo.jar.out/dist/foo.jar file

$ apktool b bar
// builds bar folder into bar/dist/bar.apk file

$ apktool b .
// builds current directory into ./dist

$ apktool b bar -o new_bar.apk
// builds bar folder into new_bar.apk

$ apktool b bar.apk
// WRONG: brut.androlib.AndrolibException: brut.directory.PathNotExist: apktool.yml
// Must use folder, not apk/jar file

```

#### Frameworks

当回编译为apk时，可能会有找不到资源的问题，例如：

```
/app-release/res/layout-v26/abc_screen_toolbar.xml:5: error: No resource identifier found for attribute 'keyboardNavigationCluster' in package 'android'
```
当出现如上问题时，应该为framework-res.apk过期了，换句话说，是这个apk有点老了。

`framework-res.apk` 可以从最新版本的手机或模拟器中拿到。具体位置在：
`/system/framework/framework-res.apk` 可以通过adb命令取出：

```
adb pull /system/framework/framework-res.apk
```
在得到framework-res.apk后，即可安装到电脑中。

一般为（其中: **if** 为 install-framework的缩写）：

```
sudo apktool if framework-res.apk

```

安装后，即不再报上面找不到资源的问题了。


#### 签名

千万不要忘记最后一步，签名。
如果不签名的话，是安装不到手机的，会报未签名错误，自己签名后即可啦。

如下命令为签名：相关参数自己修改

```
jarsigner -verbose -keystore /opt/workspace/testkeystore -signedjar app-release_signed.apk app-release_unsigned.apk testkeystore

```

