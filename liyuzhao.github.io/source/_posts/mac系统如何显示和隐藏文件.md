---
title: mac系统如何显示和隐藏文件
date: 2016-03-09 23:49:21
tags: [mac]
categories: mac
---

### 显示Mac隐藏文件的命令：
>defaults write com.apple.finder AppleShowAllFiles -bool true

### 隐藏Mac隐藏文件的命令：
>defaults write com.apple.finder AppleShowAllFiles -bool false

<!-- more -->
## 或者 : ##

### 显示Mac隐藏文件的命令：
>defaults write com.apple.finder AppleShowAllFiles YES

### 隐藏Mac隐藏文件的命令：
>defaults write com.apple.finder AppleShowAllFiles NO 

输完单击Enter键，退出终端，重新启动Finder就可以了 
#### 重启Finder：
>鼠标单击窗口左上角的苹果标志-->强制退出-->Finder-->重新启动


