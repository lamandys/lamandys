---
title: Android中使用Java8特性之Lambda
date: 2017-05-03 00:27:36
tags: android
---
##### 前言
早在几年前，Java8就已经发布了正式版本，可是到现在为止，在Android的开发中，大部分人还是一直使用的是Java6和Java7的属性和方法，这使得有时候在开发过程中难免会出现一些问题。
<!--more-->
##### 什么是 lambda 表达式?
简单的说，它可以简化代码，是个匿名方法，定义得非常简单。

** 不懂？ ** 那就对了！我的意思是能用代码怼得明白的事情就别说话。

##### 匿名内部类
如在android开发中的点击事件：
```
mButton.setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View v) {
          System.out.println("click Me");
        }
    };)
```
非常熟悉的代码是吧，在手里啪啪啪的就出来了的有没有！！可是，你有没有想过，其实这段代码最有用的仅仅只是一句** System.out.println("click Me") **而已，其余的都是模板性的代码没任何意义。

那么如何优雅的写出好的代码呢？看看lambda的实现方式先。
```
mButton.setOnClickListener(v -> System.out.println("click Me"))
```
OK ! 是不是很惊讶？！6行代码突然简洁到1行代码，我突然间有种接受不了的感觉了。要不再举个栗子？
```
new Thread(new Runnable() {
                @Override
                public void run() {
                    System.out.println("runnable opt");
                }
            }).start();
```
```
new Thread(() -> System.out.println("runnable opt"))
```
有没有看出什么来呢？竟然没有？

##### Lambda 表达式基本语法
假如我现在有个数组
```
Integer[] numbers = new Integer[]{3, 1, 2, 5, 4};
```
现在我想要对该数组的数据进行排序，我们可以使用Arrays.sort方法，它的参数是Comparator接口，类似于下面的代码：
```
Arrays.sort(numbers,new Comparator<Integer>() {
                @Override
                public int compare(Integer lhs, Integer rhs) {
                    return lhs.compareTo(rhs);
                }
            });
```
用 lambda 表达式来实现：
```
Arrays.sort(numbers, (lhs,rhs) -> lhs.compareTo(rhs));
```
##### 小总结
1. 如果Lambda表达式有参数，可写完整的：
```
(int a,int b,int c,int d) -> { return a + b + c + d; }
```
2. 左边的类型可以省略
```
(a,b,c,d) ->  { return a + b + c + d; }
```
3. return可以省略
```
(a,b,c,d) ->  { a + b + c + d; }
```
4. 语句块中只有单行的可以省略大括号
```
(a,b,c,d) ->  a + b + c + d; 
```
5. 如果Lambda表达式中没有参数，不能省略（）
```
() -> System.out.println("Hello");
```
##### 如何使用

![项目使用的jdk要为jdk 8或更高版本](http://upload-images.jianshu.io/upload_images/4463150-cc26c9dd2bce6a6e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

修改build.gradle文件
```
defaultConfig {
  ...
    jackOptions {
      enabled=true
    }
  }

  compileOptions {
    sourceCompatibility JavaVersion.VERSION_1_8
    targetCompatibility JavaVersion.VERSION_1_8
  }
```

部分插入的位置：

![](http://upload-images.jianshu.io/upload_images/4463150-2bffb8f5925fcc96.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

然后在项目中就可以愉快使用lambda表达式了！！