---
title: .gitignore文件不起作用
date: 2017-01-03 15:38:24
tags: ["git"]
categories: "git"
---

当用git时，经常会添加一些忽略的文件到.gitignore文件中，这里面类似于git的黑名单，文件中指定的文件不会加入到git仓库中，
但当发现添加某个文件名后，通过git status查看，并不能忽略此文件。

<!-- more -->

遇到此问题原因是：git仓库中某些文件被stage标记，如果不想用此文件即使添加.gitignore后，也是不会被忽略的。

** .gitignore ** 文件只是ignore没有被staged(cached)文件， 对于已经被staged文件，加入ignore文件时一定要先从staged移除。

从网络搜索到此解释：

```
If you already have a file checked in, and you want to ignore it, Git will not ignore the file if you add a rule later.
In those cases, you must untrack the file first, by running the following command in your terminal:

$ git rm --cached

```

通过上面这句话明白：把我们想要忽略的文件，从他们的staged中移除即可。
```
git rm --cached file/path/to/be/ignored
git add .
git commit -m "fix untracked files"
```

** 引用 **：
[ignore-not-working](http://stackoverflow.com/questions/11451535/gitignore-not-working)
