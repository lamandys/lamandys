---
title: 日常adb命令大全
date: 2018-10-25 14:24:17
tags: ADB
---
### 有什么方法可以去统计页面的启动时间呢？
adb logcat -s ActivityManager | grep "Displayed"

<!--more-->
### 获取app是否在前台.
1. 首先获取当前的app的进程pid，使用命令获取到应用的pid进程ID。
```
adb shell ps | grep packageName
```
2. 运行命令查看返回的ID。
```
adb shell cat proc/pid/oom_adj
```
3. 返回的ID数值越小，优先级越高，越不容易被杀死，常见的为0，1，3。

### 列出当前连接的设备列表
```
adb devices
```

### 多个设备连接，想对其中的某一设备进行操作
```
adb -s 设备号码 
```

### 启动adb服务 和 停止adb服务
```
adb server-strat
adb kill-server
```

### 安装APP
```
adb install

// 如果apk已安装，则重新安装apk并保留数据和缓存文件
adb install -r
```

### 卸载APP
```
adb uninstall

// 卸载 app 但保留数据和缓存文件
adb uninstall -k
```

### 向设备拷入文件
```
adb push 
```

### 从设备拷出文件
```
adb pull 
```

### 获取管理员权限
```
adb root
```

### 查看手机型号信息
```
adb shell getprop | grep product
```

### 移动文件
```
adb shell mv filePath toNewPath
```

### 复制文件
```
adb shell cp file filePath
```

### 创建目录
```
adb shell mkdir path
```




































