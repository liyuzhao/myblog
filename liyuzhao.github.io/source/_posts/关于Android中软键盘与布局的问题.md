---
title: 关于Android中软键盘与布局的问题
date: 2017-12-05 17:28:06
tags: [keyboard]
categories: android
---
>当在Android的layout设计里面如果输入框过多，则在输入弹出软键盘的时候，下面的输入框会有一部分被软件盘挡住，从而不能获取焦点输入。

**解决办法：**

方法一、在Activity中的onCreate中setContentView之前写上如下代码

```
getWindow().setSoftInputMode(WindowManager.LayoutParams.SOFT_INPUT_ADJUST_PAN)
```

方法二、在项目的AndroidManifest.xml文件中界面对应<activity>里加上 `android:windowSoftInputMode="stateVisible|adjustResize"`,这样会让屏幕整体上移。如果加上的是`android:windowSoftInputMode="adjustPan"`这样键盘就会覆盖屏幕。

方法三、把顶级的layout替换为ScrollView, 或者在顶级的layout外再嵌套一层ScrollView。这样页面就可以一起滚动了，软键盘会一直处于底部。

**注意：** 方法一和二应该是同一种方法，只不过一个是在代码中实现一个是在xml中实现。











