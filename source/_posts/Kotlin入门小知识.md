---
title: Kotlin入门小知识
date: 2017-03-25 17:21:53
tags: kotlin
---
##### 前言
前不久，kotlin的1.1版本正式发布啦。在当今的互联网时代，新技术犹如雨后春笋般层出不穷，我们为什么不花点时间来探究一下新的技术呢?
<!--more-->
##### kotlin是神码东西?
- Kotlin是JetBrains创造的基于JVM的语言，JetBrains是IntelliJ的缔造团队。而Android Studio又是基于IntelliJ而来的。
- kotlin具有concise（代码简洁）safe（安全）versatile（多平台支持）interoperable（完全兼容java）的特点。
- Kotlin 是一种面向对象语言。不过它支持高阶函数以及 lambda 表达式和顶层函数。

##### 为什么要用kotlin

妈妈说它好用,就试试咯.

##### 特点表现在哪里呢
1. 简洁
在java中定义一个bean业务类,代码如下:

```
public class Person {

    /**
     * 邮箱
     */
    private String email;
    /**
     * 年龄
     */
    private int age;
    /**
     * 昵称
     */
    private String nick;

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getNick() {
        return nick;
    }

    public void setNick(String nick) {
        this.nick = nick;
    }
}
```
使用Kotlin，我们只需要通过数据类：
```
data class Person(var email : String, var age : Integer, var Nick : String)
```
这个数据类，它会自动生成所有属性和它们的访问器，以及一些有用的方法，比如，toString()

2. 安全
当我们使用Java开发的时候，如果代码写得不好或者说在各种情景中都会去判断属性是否为空,不然的话有可能会报NullPointerException，我们就需要在使用它之前不停地去判断它是否为null。Kotlin，是空安全的，因为我们需要通过一个安全调用操作符（写做?）来明确地指定一个对象是否能为空。
我们可以像这样去写：
```
// 这里不能通过编译. name 不能是null
var notNullName: String = null
```
```
// 这里的 name 是可以为 null的
var nullName: String? = null
```

##### 如何使用
说了那么多的性质特征,好像并不怎么爽,因为并没有亲自动手上马撸几发.

1. 安装android studio 插件
打开android studio , File -> Setting -> Plugins -> 搜索Kotlin -> 安装重启

2. Gradle配置
在app的gradle下加入如下代码
```
apply plugin: 'kotlin-android'
apply plugin: 'kotlin-android-extensions'
```
在project的gradle下加入如下代码
```
classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:1.1.0"
```
3. 转换kotlin代码
传统的java代码如何转换成kotlin代码呢，其实很简单，打开要转换的文件，然后选择Android studio的菜单栏中的code  -> Convert Java File to Kotlin File ，OK了

4. 开始使用
在layout布局文件中新建一个layout，里面添加一个TextView标签
```
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout 
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="match_parent">
        <TextView
            android:id="@+id/centerTextView"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:gravity="center"
            tools:text="hello kotlin"/>
</RelativeLayout>
```
代码中如果要为这个TextView设置值的话，用java实现：
```
TextView  tvView = (TextView) findViewById("R.id.centerTextView");
tvView.setText("我是用java设置的值");
```
用kotlin实现：
```
centerTextView.text = "我是用kotlin设置的值"
```
是不是很爽，爽就对了。

###### 再来简单的说说kotlin

1. 类
首先来看看java中的类结构
```
// 普通的接口
public interface TestAction {
        void doSomething();
}
// 普通的Base
public class DemoBaseActivity extends Activity{}
// test
public class TestActivity extends DemoSuperActivity implements TestAction {

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
    }

    @Override
    public void doSomething() {}
}
```
kotlin:
```
// 接口
interface TestAction {
    fun doSomething()
}
// 
open class DemoSuperActivity : Activity()
// 
class TestActivity : DemoSuperActivity(), TestAction {

    override fun doSomething() {
        TODO("not implemented") //To change body of created functions use File | Settings | File Templates.
    }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
    }
}
```
恩，如你所看，java中的继承用extends关键字，接口用implements实现，而在kotlin直接用：就能代替其。
一般创建的类用class关键字，但是不能被继承，所以我们只能继承那些明确声明open或者abstract的类。
2. 方法（函数）
方法用关键字 fun 开头标识
```
fun add(x: Int, y: Int) : Int {
    return x + y
}
```
然而如果返回的结果可以使用一个表达式计算出来，你可以不使用括号而是使用等号：
```
fun add(x: Int,y: Int) : Int = x + y
```
如果没有返回值可省略,如：
```
fun print(x: Int, y: Int) {
    print(x+y)
}
```
好了，就写这么多吧，了解了基础就可以无限飞起来了啦。