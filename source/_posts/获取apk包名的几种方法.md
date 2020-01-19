---
title: 获取apk包名的几种方法
date: 2018-02-01 00:19:25
tags: [Android,ADB]
---

##### 通过adb shell进入到手机data/data目录下，查看包名（需要root）
1. adb shell 
2. cd /data/data/
3. ls
<!--more-->
![image.png](http://upload-images.jianshu.io/upload_images/4463150-17be85d609dbb053.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### 通过adb shell的pm命令，查看包名（不需要root）
1. adb shell pm list package
![image.png](http://upload-images.jianshu.io/upload_images/4463150-4de6b0f4c6405bd6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2. adb shell pm list package -f   将获取手机内所有apk对应的包名和路径
![image.png](http://upload-images.jianshu.io/upload_images/4463150-da08803056681aa3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### 通过使用adb shell的dumpsys命令，查看包名（不需要root）
1. adb shell dumpsys activity
2. 搜索cmp=
此方法查看的是当前打开应用的activity的包名
![image.png](http://upload-images.jianshu.io/upload_images/4463150-0a443ebc1d2f36f1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### 通过使用grep，查看包名（不需要root）
1. adb shell
2. logcat | grep START
![image.png](http://upload-images.jianshu.io/upload_images/4463150-59453b0aafbee6c6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### 通过使用自带的Hierayviewer工具，查看包名（需要root和开启view service）
![image.png](http://upload-images.jianshu.io/upload_images/4463150-a4113d365ceb4c59.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### 通过反编译得到文件直接查看
略.....

好了，这次真的是水了一篇，等我有ji qing 时再写......

