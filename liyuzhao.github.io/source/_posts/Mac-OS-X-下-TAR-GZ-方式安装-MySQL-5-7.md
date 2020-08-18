---
title: Mac OS X 下 TAR.GZ 方式安装 MySQL 5.7
date: 2016-03-20 12:15:20
tags: [mysql, 数据库, mac]
categories: mac
---

# Mac OS X 下 TAR.GZ 方式安装 MySQL 5.7

### 与 MySQL 5.6 相比, 5.7 版本在安装时有两处不同:
> 初始化方式改变，从`scripts/mysql_install_db --user=mysql` 初始化方式变成了`bin/mysqld --initialize --user=mysql`方式；
> 初始密码生成改变, 5.6 的版本在 tar gz 方式初始化完成后默认 root 密码为空, 5.7 版本在初始化完成后会生成一个临时的 root 密码。

<!-- more -->
```
系统环境: OS X El Capitan 10.11.1
登录用户: wid (有 sudo 权限)
MySQL 版本: 5.7.9 (mysql-5.7.9-osx10.10-x86_64.tar.gz)
MySQL下载: http://dev.mysql.com/downloads/mysql/
```
### 和 MySQL 5.6 tar gz 安装方式一样, 解压并移动到指定安装目录中并执行初始化:

```
# 解压
cd /Users/<YourName>/Downloads
tar zxvf mysql-5.7.9-osx10.10-x86_64.tar.gz

# 移动解压后的二进制包到安装目录
sudo mv mysql-5.7.9-osx10.10-x86_64 /usr/local/mysql

# 更改 mysql 安装目录所属用户与用户组
cd /usr/local
sudo chown -R root:wheel mysql

# 切换到 mysql 安装目录并执行初始化命令并记录生成的临时 root 密码
cd /usr/local/mysql
sudo bin/mysqld --initialize --user=mysql
```
 **注意**:需要记录在初始化完成后命令行窗口中显示的临时 root 密码, 如图:
 ![mysql-pwd-pic](http://7xpj58.com1.z0.glb.clouddn.com/20160320mysql.png)

### 测试启动、重启与停止:

```
cd /usr/local/mysql

# 启动
sudo support-files/mysql.server start

# 重启
sudo support-files/mysql.server restart

# 停止
sudo support-files/mysql.server stop

# 检查 MySQL 运行状态
sudo support-files/mysql.server status
```
### 修改 MySQL root 初始密码

```
# 需要 MySQL 服务在运行状态执行
cd /usr/local/mysql/bin
./mysqladmin -u root -p password 新密码
输入生成的临时密码↵
```


