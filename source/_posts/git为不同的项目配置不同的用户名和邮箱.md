---
title: git为不同的项目配置不同的用户名和邮箱
date: 2018-02-02 18:37:12
tags: android
---
还记得我们刚开始用git的时候，教程里的两行代码么：
```
git config --global user.name  xxx
git config --global user.email  xxx@xxx.xxx
```
<!--more-->
当时也不知道这样设置是为了什么，有什么作用，只知道把自己常用的邮箱填了上去。

这行命令实际上是设置了当前电脑用户的全局git配置，即所有项目如果不再设置其他的配置，那么默认采用这个配置。

实际上这个命令更改的是，当前用户的目录，window系统的是user目录下的.gitconfig文件，Mac系统下的就是～/.gitconfig这个文件。

以Mac为例，window也是一样的。

命令行进入到当前的目录，用命令cat可以查看该文件的详细内容：
```
1. cd ~
2. ls -all
3. cat .gitconfig
```
![config.png](http://upload-images.jianshu.io/upload_images/4463150-0ff9124dc6eeb159.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到我们之前配置的内容就在这个文件中保存了。

回到正题，我为什么要这样去搞呢？
由于我配置的用户名不是真实的，这样上传到GitHub上面的话就随心啦；但是对于公司内部的项目，在提交的时候如果你随便用的名字别人不知道是谁，所以得用真实的一个名字。

两者似乎存在冲突，解决办法也是很简单，就是在项目的.git目录下增加自己的配置。

我这里用命令行是因为.开头的文件都是隐藏的，而我又不想去显示它，所以只能这样咯：
```
1. cd 项目的目录
2. cd .git
3. vim config
4. 填写你的配置：
[user]
        name = lamandys
        email = lsxt10@qq.com
5. 保存退出
```

或者不这么操作的话，就是如果不使用vim命令的话，可以直接用命令：
```
git config user.name  xxx  // 主要把那个global去掉就好啦
```

这样的话就可以修改我们在提交时的用户名了。
