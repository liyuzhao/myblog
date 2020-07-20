---
title: C++调试技巧
date: 2019-03-21 10:20:44
tags: [c++, debug, gdb]
categories: c++
---

### 1、常用调试技巧

1) **代码检查**，重新阅读程序，排除比较明显的错误。编译时带上-Wall参数，生成所有的警告信息。

```
gcc -Wall -pedantic -ansi 表示以ansi/iso生成所有的警告西信息。
```

<!-- more -->

2) **取样法：** 在程序中增加一些代码，收集更多与程序运行时的行为相关的信息。使用条件编译，可以清楚的辨别哪些是调试代码，有利于调试后的代码整理。

例如：

```
#ifdef DEBUG
std::cout << x :
#endif
```

程序编译时可以选择性的加上-DDEBUG。如果加上这个标志，就定义了DEBUG这个符号，从而在程序中包含调试用的额外代码，没有加上该标志，这些调试代码将删除。

3) **程序的受控执行**。用调试器来控制代码的运行，随时查看这些变量的状态

为了能够调试程序，需要在编译和链接时为每个源文件加上编译选项参数。这些选项的作用是让编译器在程序中添加额外的调试信息。这些信息包括符号和源代码行号，调试器将利用这些信息向用户显示程序已经执行到的源代码的位置。-g标志是对程序调试性编译时常用的一个选项。调试信息的加入使可执行程序的长度成倍的增长、容量增加，程序运行时的内存数量还是和原来一样，程序调试结束后，最好还是将调试信息从程序的发行版中删除。


### 2、使用gdb进行程序调试

#### 常用功能命令：

```
g++ -g -o test test.cpp // 编译时加上-g参数
```

1. 启动gdb: `gdb test`
2. help
3. 具备带有历史记录的命令行编辑功能，方向键选择之前执行过的命令，直接回车键再次执行最近执行过的那条命令。单步调试非常有用。
4. quit: 退出
5. run:执行这个程序，程序运行失败时gdb会报告失败的原因和位置。
6. backtrace(bt):栈跟踪，失败时停止的位置，帮助我们找到程序到达错误地点的路径。
7. print：run 后检查变量，注意变量的生命期。
8. 打印围绕当前位置前后的一段代码，继续使用list可以显示更多的代码。
9. 设置断点，停止程序的运行，查看变量。help breakpoint，break lineNumber，cont，end，display，disable breakpoint number，clear，commands breakpointNumber.
10. 设置断点后经常使用单步调试命令next(n)，查看程序运行的细节。

### 3、valgrind 内存调试

动态内存分配很容易出现程序漏洞，必须清楚自己分配的每一块内存，而且要确定没有使用已经释放的内存块，非常重要。内存调试的工具有很多，这里使用的是valgrind工具。在centos 7中直接使用 yum install valgrind 安装。

```
#include <iostream>

int main()
{
    int *ptr = new int [3];
    ptr[3]=1;

    delete [] ptr;
    std::cout << ptr[1];
    return 0;
}
```

上面简单的代码编译运行不会发生错误，但是实际上发生了很严重的内存问题。ptr[3]访问越界，std::cout <<ptr[i]，读已经释放过的内存。

**通过valgrind工具可以检查出来：**

```
[xgwang@localhost Desktop]$ g++ -g -o test2 test2.cpp

[xgwang@localhost Desktop]$ valgrind ./test2

==21739== Memcheck, a memory error detector
==21739== Copyright (C) 2002-2013, and GNU GPL'd, by Julian Seward et al.
==21739== Using Valgrind-3.10.0 and LibVEX; rerun with -h for copyright info
==21739== Command: ./test2
==21739== 
==21739== Invalid write of size 4
==21739== at 0x40081E: main (test2.cpp:8)
==21739== Address 0x5a1504c is 0 bytes after a block of size 12 alloc'd
==21739== at 0x4C2A7AA: operator new[](unsigned long) (in /usr/lib64/valgrind/vgpreload_memcheck-amd64-linux.so)
==21739== by 0x400811: main (test2.cpp:7)
==21739== 
==21739== Invalid read of size 4
==21739== at 0x40083F: main (test2.cpp:11)
==21739== Address 0x5a15044 is 4 bytes inside a block of size 12 free'd
==21739== at 0x4C2B5E1: operator delete[](void*) (in /usr/lib64/valgrind/vgpreload_memcheck-amd64-linux.so)
==21739== by 0x400836: main (test2.cpp:10)
==21739== 
0==21739== 
==21739== HEAP SUMMARY:
==21739== in use at exit: 0 bytes in 0 blocks
==21739== total heap usage: 1 allocs, 1 frees, 12 bytes allocated
==21739== 
==21739== All heap blocks were freed -- no leaks are possible
==21739== 
==21739== For counts of detected and suppressed errors, rerun with: -v
==21739== ERROR SUMMARY: 2 errors from 2 contexts (suppressed: 2 from 2)

```



>转载自：知乎
>
>[代码人生](https://www.zhihu.com/people/dai-ma-ren-sheng-5)