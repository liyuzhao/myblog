---
title: C++常用的程序库
date: 2019-03-22 10:20:54
tags: [c++]
categories: cpp
---

- 合适的程序库，可以带来方便。
- 代码有深度且可读性高。

<!-- more -->

#### 强烈推荐的库
printf 不好用，ostream 也不好用，format 好用 [fmtlib/fmt](https://github.com/fmtlib/fmt)

超高效级的 json 解析 [Tencent/rapidjson](https://github.com/Tencent/rapidjson)


#### 其他库

3D模型解析 [assimp/assimp](https://github.com/assimp/assimp)

物理模拟 [bulletphysics/bullet3](https://github.com/bulletphysics/bullet3)

渲染中间层 [bkaradzic/bgfx](https://github.com/bkaradzic/bgfx)

3D灵感宣泄场所 [cinder/Cinder](https://github.com/cinder/Cinder) 

命令行帮助既是命令行解析 [https://github.com/docopt/docopt.cpp](https://github.com/docopt/docopt.cpp)



3D 数学运算 [g-truc/glm](https://link.zhihu.com/?target=https%3A//github.com/g-truc/glm)

拿来就能用的 UI [ocornut/imgui](https://github.com/ocornut/imgui)

json 结构用在 C++ 里面就像在 JS 里面一样自然 [nlohmann/json](https://github.com/nlohmann/json)

用过都知道它的好的性能分析工具 [jonasmr/microprofile](https://github.com/jonasmr/microprofile)

又快又不折腾的 xml 解析 [zeux/pugixml](https://github.com/zeux/pugixml)

专注寻路 [recastnavigation/recastnavigation](https://github.com/recastnavigation/recastnavigation) 

什么都能放到头文件中 [nothings/stb](https://github.com/nothings/stb)

一键生成 C/C++ 对各种其他语言的接口 [swig/swig](https://github.com/swig/swig)

最快的哈希算法 [Cyan4973/xxHash](https://github.com/Cyan4973/xxHash)

包含大量计算几何算法的 [Geometric Tools](https://www.geometrictools.com/)

包含最经典渲染算法的 [mmp/pbrt-v3](https://github.com/mmp/pbrt-v3)

又小又快又方便的单元测试库 [onqtam/doctest](https://github.com/onqtam/doctest)

高精度浮点数运算库 [LibBF Library](https://bellard.org/libbf/)

#### 线程

- [C++ Threads](http://threads.sourceforge.net/)：这个库的目标是给程序员提供易于使用的类，这些类被继承以提供在Linux环境中很难看到的大量的线程方面的功能。
- [ZThreads](http://zthread.sourceforge.net/)：跨平台的C++线程和同步库。


#### 字符串

- [C++ Str Library](http://www.utilitycode.com/str/)：操作字符串和字符的库，支持Windows和支持gcc的多种平台。
- [Common Text Transformation Library](http://cttl.sourceforge.net/)：解析和修改STL字符串的库。
- [GRETA](http://research.microsoft.com/projects/greta/)：由微软研究院的研究人员开发的处理正则表达式的库，在小型匹配的情况下有非常优秀的表现。


#### C语言开源项目：

- [Webbench](http://home.tiscali.cz/~cz210552/webbench.html)：在Linux下使用的非常简单的网站压测工具，使用C语言编写, 代码超级简洁，源码加起来几乎不到600行。
- [Tinyhttpd](http://sourceforge.net/projects/tinyhttpd/)：超轻量型Http Server，C语言开发，附带简单的Client，可通过阅读这段代码理解一个 Http Server 的本质。
- [cJSON](http://sourceforge.net/projects/cjson/)：C语言中的一个JSON编解码器，非常轻量级，速度非常理想。结构简单易懂，可以作为一个非常好的C语言项目进行学习。
- [CMockery](http://code.google.com/p/cmockery/downloads/list)：Google发布的用于C单元测试的一个轻量级的框架。它很小巧，对其他开源包没有依赖，对被测试代码侵入性小。
- [Libev](http://software.schmorp.de/pkg/libev.html)：基于Reactor模式，效率较高，并且代码精简，是学习事件驱动编程的很好的资源。
- [Memcached](http://memcached.org/)：Memcached 是一个高性能的分布式内存对象缓存系统，用于动态Web应用以减轻数据库负载。
- [Lua](http://www.lua.org/)：在任何支持ANSI C编译器的平台上都可以轻松编译通过。
- [SQLite](http://www.sqlite.org/)：SQLite是一个开源的嵌入式关系数据库，实现自包容、零配置、支持事务的SQL数据库引擎。
- [NETBSD](http://www.netbsd.org/)：NetBSD是一个免费的，具有高度移植性的 UNIX-like 操作系统，是现行可移植平台最多的操作系统。



- 应用开发框架Qt，优雅的信号与槽，强大的界面类库，跨平台。
- CEF（Chromium Embedded Framework），使用网页做富客户端的绝佳选择，基于Chromium，可以方便嵌入到你的应用中。类似的还有 Electron 。
- WebRTC，非常赞的框架，做音视频通信绕不开的。
- TinyXml，小巧的C++ XML库，几个源文件，直接加入到项目中就可以用
- Protobuf，Google的，网络通信，非常赞，方便序列化和结构化，流量又小
- FreeImage，强大好用的图形库
- Libevent，轻量级的基于事件驱动的高性能的开源网络库
- ffmpeg，多媒体开发类库的无冕之王

---
- 一个非常容易上手的 C++ gui 库 [nana](http://nanapro.org/en-us/)
- http客户端curl
- http服务器 crow
- gzip压缩zlib
- json序列化nlohmann/json
- 二进制序列化protobuf
- 嵌入式数据库sqlite
- 日志库glog
- 参数解析库gflags
- 消息队列zmq
- rpc库brpc
- tcp网络库evpp
- 3d仿真osg
- 图形图像opencv
- stl,boost,qt就不用说了。