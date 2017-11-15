---
title: Linux命令wc的详细用法
date: 2016-08-29 16:59:48
tags: [linux]
categories: linux
---

wc命令用来打印文件的文本行数、单词数、字节数等（print the number of newlines, words, and bytes in files）。在Windows的Word中有个“字数统计”的工具，可以帮我们把选中范围的字数、字符数统计出来。Linux下的wc命令可以实现这个 功能。使用vi打开文件的时候，底下的信息也会显示行数和字节数。

<!-- more -->

## 常用参数

格式：wc -l <file>
打印指定文件的文本行数。（l=小写L）
以下参数可组合使用。
参数：-c, --bytes
打印字节数（print the byte counts）

参数：-m, --chars
打印字符数（print the character counts）

参数：-l, --lines
打印行数（print the newline counts）

参数：-L, --max-line-length
打印最长行的长度（print the length of the longest line）

参数：-w, --words
打印单词数（print the word counts）

## 使用示例
### 示例一
[root@jfht ~]# wc /etc/passwd
  46   66 2027 /etc/passwd
行数 单词数 字节数 文件名
[root@jfht ~]#
[root@jfht ~]# wc -l /etc/passwd
46 /etc/passwd
[root@jfht ~]# wc -cmlwL /etc/passwd
  46   66 2027 2027   74 /etc/passwd
[root@jfht ~]# wc -cmlLw /etc/passwd
  46   66 2027 2027   74 /etc/passwd
[root@jfht ~]# wc -wcmlL /etc/passwd
  46   66 2027 2027   74 /etc/passwd
[root@jfht ~]#
问题来了：从上面的命令行执行结果来看，wc的输出数据的顺序与的几个参数的顺序好像没有关系？！
### 示例二 用wc命令怎么做到只打印统计数字不打印文件名
使用管道线。这在编写shell脚本时特别有用。
[root@jfht ~]# wc -l /etc/passwd
46 /etc/passwd
[root@jfht ~]# cat /etc/passwd | wc -l
46
[root@jfht ~]#
### 示例三 中文编码的问题
执行环境是中文编码的。
[root@jfht ~]# echo $LANG
zh_CN.GB18030
中文编码文件ehr_object.gv，UTF8编码的文件ehr_object_utf8.gv。
[root@jfht ~]# file ehr_object.gv ehr_object_utf8.gv
ehr_object.gv:      ISO-8859 text
ehr_object_utf8.gv: UTF-8 Unicode text
[root@jfht ~]#
[root@jfht ~]# wc ehr_object.gv ehr_object_utf8.gv
  11  105  830 ehr_object.gv
wc: ehr_object_utf8.gv:4: 无效或不完整的多字节字符或宽字符
  11  105  866 ehr_object_utf8.gv
  22  210 1696 总计
[root@jfht ~]#
### 示例四 中文单词数的计算
[root@jfht ~]# cat test.txt
你好Word
Linux

[root@jfht ~]# wc test.txt
 3  2 16 test.txt
行数 单词数 字节数 文件名
[root@jfht ~]#
