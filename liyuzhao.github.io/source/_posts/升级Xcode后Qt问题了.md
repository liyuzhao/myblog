---
title: 升级Xcode后Qt问题了
date: 2017-01-23 14:21:18
tags: [qt]
categories: qt
---

升级XCode后，Qt新建项目出现了问题：

  `Xcode not set up properly. You may need to confirm the license agreement by running /usr/bin/xcodebuild.`

<!-- more -->
在Google找到了解决方法。
[http://stackoverflow.com/questions/33728905/qt-creator-project-error-xcode-not-set-up-properly-you-may-need-to-confirm-t](http://stackoverflow.com/questions/33728905/qt-creator-project-error-xcode-not-set-up-properly-you-may-need-to-confirm-t)

~> Xcode 8

This problem occurs when command line tools are installed after Xcode is installed. What happens is the `Xcode-select` developer directory gets pointed to ``/Library/Developer/CommandLineTools`.

Step 1:

Point `Xcode-select` to the correct Xcode Developer directory with the command:

```
sudo xcode-select -switch /Applications/Xcode.app/Contents/Developer
```
Step 2:

Confirm the license agreement with the command:
```
xcodebuild -license
```
This will prompt you to read through the license agreement.

Enter `agree` to accept the terms.

#### >= Xcode 8

Step 1:

As Bruce said, this happens when Qt tries to find `xcrun` when it should be looking for `xcodebuild`.

Open the file:
```
Qt_install_folder/5.7/clang_64/mkspecs/features/mac/default_pre.prf
```

Step 2:

Replace:

```
isEmpty($$list($$system("/usr/bin/xcrun -find xcrun 2>/dev/null"))))
```

With:

```
isEmpty($$list($$system("/usr/bin/xcrun -find xcodebuild 2>/dev/null")))
```
