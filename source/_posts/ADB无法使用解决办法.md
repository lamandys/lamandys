---
title: ADB无法使用解决办法.md
date: 2017-02-06 00:19:25
tags: android
---

在我们使用Android studio连接手机调试的时候，总是会出现找不到手机的问题，很可能的原因是ADB被占用，下面教大家来解决这个问题。
<!--more-->
命令行输入adb devices会出现提示：
```
error: could not install *smartsocket* listener: cannot bind to 127.0.0.1:5037: 通常每个套接字地址(协议/网络地址/端口)只允许使用一次。 (10048)
could not read ok from ADB Server
```

###### 解决办法

输入命令：
```
netstat -ano | findstr "5037"
```
会出现：
```
 TCP    127.0.0.1:5037         0.0.0.0:0              LISTENING       2068
```
以win10为例，打开任务管理器，点击“详细信息“，找出"PID"选项卡中与上面最后数字相同的一列，然后结束相关进程，即可。

重新插拔数据线，显示正常。