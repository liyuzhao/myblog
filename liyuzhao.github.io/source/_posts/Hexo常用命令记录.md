---
title: Hexo常用命令记录
date: 2016-05-18 18:24:43
tags: [hexo]
categories: hexo
---

# Hexo常用命令记录

由于博客是用的hexo, 方便以后忘记来此查询

```
  hexo new "postName" #新建文章
  hexo new page "pageName" #新建页面
  hexo new draft "draftName" #新建草稿
  hexo publish draft "draftName" #草稿变成正文
  hexo generate #生成静态页面至public目录
  hexo server #开启预览(可以访问localhost:4000)
  hexo deploy #部署到github, gitcafe等

```

简写命令

```

  hexo n #新生成
  hexo g #生成静态页面至public目录
  hexo s #开启预览(可以访问localhost:4000)
  hexo d #部署

  hexo d -g #重新生成并发布

```
[Hexo官方文档](http://hexo.io/zh-cn/)
