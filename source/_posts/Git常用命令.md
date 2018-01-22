---
title: Git常用命令
date: 2017-10-29 00:30:35
tags: android
---
##### 说明
这里记录的所有命令，都是平时比较常用的。当然，也许还有一些也是比较常用的命令，这里并没有列出来。
然后，这里只是稍微的提及到相关的知识，具体的使用不会的寄几解决啦。
<!--more-->
##### 一、git配置
1、设置用户名和邮箱
```
git config --global user.name "xxx"
git config --global user.email xxx@xx.xx
```
##### 二、基本操作
1、初始化,进入工作目录，执行
```
git init
```
2、开始跟踪所有文件
```
git add .
```
3、添加单独文件，如test.md文件
```
git add test.md
```
4、执行一次提交
```
git commit -m "这是提交的说明"
```
5、检查当前文件状态
```
git status
```
6、查看差异
```
git diff
```
7、克隆网上的资源到本地
```
git clone xxx
```
##### 三、git stash

1、保存当前的工作进度。会分别对暂存区和工作区的状态进行保存。
```
git stash
```
2、显示所有已经保存的列表
```
git stash list
```
3、恢复所有暂存列表中的第一项，也是最新的那项，恢复后并删除此项
```
git stash pop 
```
4、恢复列表中指定的某一项，并删除
```
git stash pop [<stash>]
```
5、恢复所有暂存列表中的第一项，也是最新的那项，但不删除
```
git stash apply
```
6、恢复列表中指定的某一项，但不删除
```
git stash apply [<stash>]
```
7、删除最新的进度，也是列表中的第一个
```
git stash drop
```
8、删除指定的某一项
```
git stash drop [<stash>]
```
9、清除列表的所有记录
```
git stash clear
```
##### 三、git merge
1、默认的fast-forward方式，把另一分支的历史copy过来，指针指向最新
```
git merge
```
2、--no-ff 方式，把指针指向合并分支的头，但不把分支的信息copy过来，一旦分支删除，则丢失信息
```
git merge --no-ff
```
3、--squash 方式，把另一分支信息压缩成一个，合并
```
git merge --squash
```

##### 四、远程仓库
1、显示当前的远程仓库地址
```
git remote -v
```
2、添加远程仓库地址
```
git remote add origin https://xxx.xxx/xxxx
```
3、提交到远程仓库，master是分支
```
git push -u origin master
```

##### 五、打标签
1、显示标签，只能看到标签名字，看不到标注信息
```
git tag
```
2、显示标签的详细信息，V1.0是某个标签
```
git show V1.0
```
3、打标签
```
git tag -a V1.0 -m "这是1.0版本标签的说明"
```
##### 六、分支
1、在当前分支上新建另一分支test
```
git branch test
```
2、切换到test分支
```
git checkout test
```
3、新建分支test并切换到新分支，相当于上面两条的合体
```
git checkout -b test
```
4、删除分支test
```
git branch -d test
```
5、列出所有的分支
```
git branch
```
##### 七、其他常用
1、改变最近一次提交
```
git commit --amend
```
2、合并多次的commit到一个，比如3个，具体的使用自行解决，稍微提及
```
git rebase -i HEAD~3
```