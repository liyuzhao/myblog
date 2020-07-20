---
title: mac下安装depot_tools
date: 2019-07-12 18:24:01
tags: [webrtc, depot_tool]
categories: webrtc
---

Google的Chrome代码，webrtc代码都是需要用到depot_tools的，那怎样安装呢，下面详细介绍，使用环境为Mac系统。

### 1. 获取depot_tools

```
git clone https://chromium.googlesource.com/chromium/tools/depot_tools.git
```

### 2. 获取depot_tools当前目录

```
pwd
```
### 3. 添加环境变量

```
vim ~/.bash_profile 打开最后一行添加，如无此文件，可添加此文件
export PATH="$PATH:/PWD/depot_tools" PWD为刚才第二步获取的路径
```

### 4. 生效环境变量

```
source ~/.bash_profile
```