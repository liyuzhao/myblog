---
title: wireshark过滤器规则
date: 2018-12-10 12:31:22
tags: [linux, wireshark]
categories: linux
---

<!-- more -->
#### 一、IP过滤：包括来源IP或者目标IP等于某个IP
比如：

```
ip.src addr==192.168.0.208  or ip.src addr eq 192.168.0.208 显示来源IP

ip.dst addr==192.168.0.208  or ip.dst addr eq 192.168.0.208 显示目标IP
```

#### 二、端口过滤：
比如：

```
 tcp.port eq 80 // 不管端口是来源的还是目标的都显示
 tcp.port == 80
 tcp.port eq 2722
 tcp.port eq 80 or udp.port eq 80
 tcp.dstport == 80 // 只显tcp协议的目标端口80
 tcp.srcport == 80 // 只显tcp协议的来源端口80

过滤端口范围
tcp.port >= 1 and tcp.port <= 80

```

#### 三、协议过滤：tcp

```
udp
arp
icmp
http
smtp
ftp
dns
msnms
ip
ssl
等等
排除ssl包，如!ssl 或者  not ssl
```

#### 四、包长度过滤：
比如：

```
udp.length == 26 这个长度是指udp本身固定长度8加上udp下面那块数据包之和
tcp.len >= 7  指的是ip数据包(tcp下面那块数据),不包括tcp本身
ip.len == 94 除了以太网头固定长度14,其它都算是ip.len,即从ip本身到最后
frame.len == 119 整个数据包长度,从eth开始到最后
```
#### 五、http模式过滤：

```
例子:
http.request.method == “GET”
http.request.method == “POST”
http.request.uri == “/img/logo-edu.gif”
http contains “GET”
http contains “HTTP/1.”

// GET包
http.request.method == “GET” && http contains “Host: ”
http.request.method == “GET” && http contains “User-Agent: ”
// POST包
http.request.method == “POST” && http contains “Host: ”
http.request.method == “POST” && http contains “User-Agent: ”
// 响应包
http contains “HTTP/1.1 200 OK” && http contains “Content-Type: ”
http contains “HTTP/1.0 200 OK” && http contains “Content-Type: ”
一定包含如下
Content-Type:
```

#### 六、连接符 and / or

#### 七、表达式：
```
!(arp.src==192.168.1.1) and !(arp.dst.proto_ipv4==192.168.1.243)
```