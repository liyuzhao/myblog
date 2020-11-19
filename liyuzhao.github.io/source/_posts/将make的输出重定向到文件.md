---
title: 将make的输出重定向到文件
date: 2020-11-19 11:01:24
tags: [linux]
categories: linux
---

将make的输出重定向到文件

<!-- more -->

-  想要把make输出的全部信息，输出到某个文件中，最常见的办法就是：

```
make xxx > build_output.txt
```
此时默认情况是没有改变2=stderr的输出方式，还是屏幕，所以，如果有错误信息，还是可以在屏幕上看到的。

-  只需要把make输出中的错误(及警告)信息输出到文件中，可以用：

```
make xxx 2> build_output.txt

```
相应地，由于1=stdout没有变，还是屏幕，所以，那些命令执行时候输出的正常信息，还是会输出到屏幕上，你还是可以在屏幕上看到的。

-  只需要把make输出中的正常（非错误，非警告）的信息输出到文件中，可以用：

```
make xxx 1> build_output.txt

```
相应地，由于2=stderr没有变，还是屏幕，所以，那些命令执行时候输出的错误信息，还是会输出到屏幕上，你还是可以在屏幕上看到的。

-  想要把正常输出信息和错误信息输出到分别的文件中，可以用：

```
make xxx 1> build_output_normal.txt 2>build_output_error.txt
```
即联合使用了1和2，正常信息和错误信息，都输出到对应文件中了。

-  所有的信息都输出到同一个文件中：

```
make xxx > build_output_all.txt 2>&1

```

其中的2&gt;&1表示错误信息输出到&1中，而&1，指的是前面的那个文件：build\_output\_all.txt 。
注意：上面所有的1,2等数字，后面紧跟着大于号'&gt;' ，中间不能有空格

