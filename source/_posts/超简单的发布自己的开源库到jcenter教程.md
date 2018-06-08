---
title: 超简单的发布自己的开源库到jcenter教程
date: 2018-03-07 23:44:16
tags: android maven
---
平时我们在开发的时候，必不可少的都有使用过很多的第三方库，比如某分享的sdk依赖：
<!--more-->
```
compile 'com.mmc.share:ShareSDK-Wechat:3.0.0'
compile 'com.mmc.share:ShareSDK-Wechat-Core:3.0.0'
compile 'com.mmc.share:ShareSDK-Wechat-Moments:3.0.0'
compile 'com.mmc.share:ShareSDK-QQ:3.0.0'
compile 'com.mmc.share:ShareSDK-QZone:3.0.0'
compile 'com.mmc.share:ShareSDK-SinaWeibo:3.0.0'
```
那么，他们是怎么做到的呢？怎样才能把自己的代码分享给别人用？

进入正题，下面一步步教你怎么像别人一样，把自己做的东西做出一个库，并可以分享给别人用。

我们日常经常使用的maven仓库有jcenter和mavenCentral，高版本的android studio默认使用jcenter作为maven中央仓库，所以接下来讲的都是关于jcenter的操作。

1. 去[bintray官网](https://bintray.com/)注册一个账号,但是这里需要注意的是，不要点击start your free trial 的按钮去注册，而是点击右边的sign up here ,原因是绿色的那个注册的是企业的账号，完了一个月内步没有添加库到jcenter的功能。
![image.png](http://upload-images.jianshu.io/upload_images/4463150-09c20eee7d731be8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2. 嗯，这里没有什么好讲的，直接填写相关信息后点鸡下面的按钮就行。
![image.png](http://upload-images.jianshu.io/upload_images/4463150-ca885207d32d58b5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3. 注册完激活后登录，接下来新建一个maven仓库
![image.png](http://upload-images.jianshu.io/upload_images/4463150-4525bba8ae6a2486.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

 4. 继续填信息
* name是你新建的仓库的名称
* type从下拉列表中选择maven
* 再往下的是选择一个开源的协议，这里选择Apache-2.0，随意都行，但得对应代码中的协议，后面有解释
* 填完就点击创建
![image.png](http://upload-images.jianshu.io/upload_images/4463150-b26b18c140cc0a51.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如无意外：如果不成功的话回去重新创建一次即可。
![image.png](http://upload-images.jianshu.io/upload_images/4463150-7327fe6ba09cfc7d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

5. 继续新建一个项目，刚才创建的是一个仓库，仓库下会有很多不同的项目，下面来创建一个我们测试的项目，点击add new package
![image.png](http://upload-images.jianshu.io/upload_images/4463150-18bccf335c91fb6c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

6. 继续填写项目的详细信息
* name是你这个项目的名字，如：image-util
* description是项目的介绍，没有*号标志的可以不填
* licenses默认选择的是你创建仓库时选择的协议，这里是Apache-2.0
* tags是这个项目的一些标签，可以多个
* Maturity不管，默认就行
* website，这里如果你的项目在GitHub上有代码，填git的网页地址，可不填
* issues，同上
* version contro这个是你源代码所在的仓库地址，比如git地址，如无随便填个网址即可。
* 填完点击创建即可。
![image.png](http://upload-images.jianshu.io/upload_images/4463150-0ced15338a064ff6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

7. 创建成功，点击这里进入该项目的详细页面。
![image.png](http://upload-images.jianshu.io/upload_images/4463150-546f87e9e0f6e4fd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![现在啥也没有.png](http://upload-images.jianshu.io/upload_images/4463150-21469e2601e12188.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

8. 准备好你要上传的代码，打开Android studio
根目录的build.gradle文件中加入代码：
```
dependencies {
        classpath 'com.android.tools.build:gradle:2.3.3'//原来有的，下面的是新增的
        classpath 'com.jfrog.bintray.gradle:gradle-bintray-plugin:1.7.3'
        classpath 'com.github.dcendents:android-maven-gradle-plugin:1.4.1'
    }
```

为了兼容格式，加入下面的代码：
```
allprojects {
    repositories {
        jcenter()
    }

    //注释中有汉字，需要这里处理
    tasks.withType(Javadoc) {
        options {
            encoding "UTF-8"
        }
    }
}
```

9. 首先要发布的代码作为一个单独的library，这个忽略不说了，再在该module中新建一个mavenpush.gradle的文件

这里要明白一个定义，比如上面的依赖中
```
compile 'com.mmc.share:ShareSDK-Wechat:3.0.0'
```
其中引号里面的内容以冒号分割开分为3个部分，第一部分com.mmc.share是groupID，第二部分ShareSDK-Wechat是开源库的名称，第三部分是该库的版本

mavenpush.gradle的内容如下：
```
apply plugin: 'com.github.dcendents.android-maven'
apply plugin: 'com.jfrog.bintray'

group = 'com.lamandys' // 构建依赖的组
version = '1.0.0'  // 上传版本号

def siteUrl = 'https://github.com/bintray/bintray-examples'
def gitUrl = 'https://github.com/bintray/bintray-examples.git' // git 地址

Properties properties = new Properties()
properties.load(project.rootProject.file('local.properties').newDataInputStream())

install {
    repositories.mavenInstaller {
        pom {
            project {
                packaging 'aar' //发布的格式，有jar和aar的形式
                name 'image util library code and resource' // 说明
                url siteUrl 

                licenses {
                    license {
                        name 'The Apache Software License, Version 2.0'
                        url 'http://www.apache.org/licenses/LICENSE-2.0.txt'
                    }
                }

                developers {
                    developer {
                        id 'misslp' // 注册bintray时填写的昵称
                        name 'lan lp' // 注册账号的姓和名
                        email 'xzr23516@tqoai.com'
                    }

                }

                scm { //不用改
                    connection gitUrl
                    developerConnection gitUrl
                    url siteUrl
                }
            }
        }
    }
}

bintray {
    user = "misslp" // 注册bintray时填写的昵称
    key = "aacd18f97563f2edae9bd40ee83780e15013e" //每个账号的唯一key
    configurations = ['archives']

    pkg {
        userOrg = 'misslp' // 个人的账号跟user一样即可，企业的账号填组织的名字
        repo = 'maven' // 创建仓库的名字
        name = 'image-util' // 开源库的名字，和group组成整个依赖关系
        desc = '这个是图片处理的工具类集合库'// 说明

        // 不用改
        websiteUrl = siteUrl
        issueTrackerUrl = siteUrl
        vcsUrl = gitUrl

        licenses = ['Apache-2.0']// 选择的开源库是什么
        labels = ['aar', 'image','android'] // 该库标签

        publicDownloadNumbers = true //允许公开下载的数量
        publish = true // 是否公开
    }
}

task sourcesJar(type: Jar) {
    from android.sourceSets.main.java.srcDirs
    classifier = 'sources'
}

task javadoc(type: Javadoc) {
    source = android.sourceSets.main.java.srcDirs
    classpath += project.files(android.getBootClasspath().join(File.pathSeparator))
}

task javadocJar(type: Jar, dependsOn: javadoc) {
    classifier = 'javadoc'
    from javadoc.destinationDir
}

artifacts {
    // 这里上传两个，完整的结构，不然到时add to jcenter审核不通过
    archives javadocJar
    archives sourcesJar
}
```

10. 命令行执行，gradlew install bintrayUpload，或者直接图形界面执行：
![image.png](http://upload-images.jianshu.io/upload_images/4463150-5833a983cc3774bf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![出现这个即可.png](http://upload-images.jianshu.io/upload_images/4463150-4e6dda285a9429a1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

11. 再到刚才的页面刷新，出现下面类似的即表示上传成功：
![image.png](http://upload-images.jianshu.io/upload_images/4463150-a4638cda5e23cd0a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

12. 此时的最后一步，点击右边的add to jcenter按钮，之后填写描述点击发送即可提交申请。
![image.png](http://upload-images.jianshu.io/upload_images/4463150-872dc659f2b2abb9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

13. 申请通过后，直接在Android中引入即可像以往一样使用了。
![image.png](http://upload-images.jianshu.io/upload_images/4463150-8cf946c04ff7a168.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

14. 如果在提交申请后，想要马上去使用这个库怎么办？
![image.png](http://upload-images.jianshu.io/upload_images/4463150-04498651426b8587.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

复制上面的地址，打开Android studio项目根目录的build.gradle文件，加入以下代码即可使用了。
```
allprojects {
    repositories {
        jcenter()
        maven { url 'https://dl.bintray.com/misslp/maven'}
    }
}
```
emmm，就是这么简单，第一次操作的话需要耐心点，熟悉后其实很简单的啦～