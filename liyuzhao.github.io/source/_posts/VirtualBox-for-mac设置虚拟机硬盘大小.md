---
title: VirtualBox for mac设置虚拟机硬盘大小
date: 2020-09-15 18:39:38
tags: [vitualBox]
categories: mac
---

由于VirtualBox在设置好磁盘大小以后就无法通过GUI图形界面进行调整虚拟机硬盘的容量，只能通过命令行调整。我的系统是Mac OS X，Linux和Mac应该差不多，具体没确认过。这种通过命令调整的方法适用于VirtualBox4.0以上。

<!-- more -->

首先要确认虚拟机磁盘存储的位置。打开VirtualBox，点击存储，鼠标指向虚拟磁盘，会提示虚拟磁盘的位置，如下图。

![](/assets/blogImg/20200915-vsbox.png)

 现在打开终端，输入sudo su，取得管理员权限，依据刚刚获得的路径，使用VBoxManage命令调整硬盘大小，具体如下：VBoxManage modifyhd /Users/laoshu/VirtualBox\ VMs/windows7/windows7.vdi --resize 61440。resize的参数是MB，我需要60GB，所以就是61440MB。调整以后重启虚拟机，即可硬盘容量即为60GB。
