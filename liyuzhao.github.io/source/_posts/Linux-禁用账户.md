---
title: Linux 禁用账户
date: 2020-09-11 18:12:09
tags: [linux]
categories: linux
---

1、可直接修改/etc/passwd相关项来禁用账户：
/etc/passwd username:x:uid:gid:explain:home:shell

x-用户的密码原来直接存储在第二字段，但是为了安全，最后专门有了/etc/shadow文件，现在默认用x替代

uid-用户的uid,一般情况下root为0，1-499默认为系统账号，有的更大些到1000，500-65535为用户的可登录账号，有的系统从1000开始

<!-- more -->

gid-用户的gid,linux的用户都会有两个ID,一个是用户uid，一个是用户组id，在我们登录的时候，输入用户名和密码，其实会先到/etc/passwd查看是否有你输入的账号或者用户名，有的话将该账号与对应的UID和GID(在/etc/group中)读出来。然后读出主文件夹与shell的设置，然后再去检验密码是否正确，正确的话正常登录。

explain-用户的账号说明解释

home-用户的家目录文件夹

shell-用户使用的shell，如果换成/sbin/nologin/就是默认没有登录环境的

2、可直接修改/etc/shadow相关项来禁用账户：

/etc/shadow username:passwd:lastchg:min:max:warn:inactive:expire:

username–用户名

passwd–密码

lastchg–从1970年1月1日起到上次修改密码所经过的天数

min–密码再过几天可以被变更(0表示随时可以改变)

max–密码再过几天必须被变更(99999表示永不过期)

warn–密码过期前几天提醒用户(默认为一周)

inactive–密码过期几天后帐号被禁用

expire–从1970年1月1日算起，多少天后账号失效


3、禁止所有的用户登录

echo "hehe" >> /etc/nologin

这种方法设置后，只是禁止了从外部ssh登陆本机时有效！但是在本机上，无论是从root用户还是其他普通用户使用su命令切换到锁定用户下都不受影响。

4、使用命令 passwd    (锁定后，做了ssh无密码信任的机器之间登录不受影响)

passwd -l 用户            //锁定账号，-l:lock

passwd -u 用户            //解禁用户，-u:unlock

5、使用命令 usermod

usermod -L 用户            //锁定帐号，-L:lock

usermod -U 用户            //解锁帐号，-U:unlock

usermod -e 1970-01-02 用户            //该账号的密码自1970年1月1日起，过一天后立即过期

usermod 用户 -s /sbin/nologin        //修改用户的shell类型
用usermod -s禁用的用户，禁用的提示信息可加在 /etc/nologin.txt ，若没有此文件可新建

