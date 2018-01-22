---
title: Android性能优化慎用Enum的代替方法
date: 2017-10-25 23:13:23
tags: android
---
###### 1. 为什么要用枚举？
在开发中我们是不是经常都自己定义一些固定不变的静态常量来作为其他方法的参数或者是判断的条件？比如：
<!--more-->
```
private static final int SEX_GIRL = 0;
private static final int SEX_BOY = 1;
```
当我们需要把这些作为参数传递的时候，就会出现取值范围不清楚的问题，调用者在不知道源码的情况下有可能会导致传值错误，比如：
```
public class Person {
    private static final int SEX_GIRL = 0;
    private static final int SEX_BOY = 1;

    ...

    private int sex;

    public int getSex() {
        return sex;
    }

    public void setSex(int sex) {
        this.sex = sex;
    }

    ...

   Person person = new Person();
   person.setSex(23333);
}
```

噢，天哪，谁知道23333的性别是男的还是女的亦或者是gay？为此，在Java1.5版本之后，枚举就被隆重的请了出来主持大局。

###### 2. 使用enum来避免传入不合法的参数
为了避免上面的情况，下面用枚举来完善下，代码如下：
```
public class Person {
    public enum Sex {
        SEX_BOY,SEX_GIRL
    }

    ...

    private Sex sex;

    public Sex getSex() {
        return sex;
    }

    public void setSex(Sex sex) {
        this.sex = sex;
    }

    ...

    Person person = new Person();
    person.setSex(Person.Sex.SEX_BOY);
}

```
这里指定了参数的类型是Sex，而Sex也只有两个参数可选，避免了上面的传错参数的风险，比较直观。

###### 3. 为什么又不用了？
上面的用法从表面上看起来是没有什么问题，的确也是这样！使用枚举真的很方便（至少我是这样认为的）

但是为什么呢，原因是众所周知的了，Android官方文档中也有说明，enum中的每一个值其实都是一个Object对象，每声明一个值就会创建一部分的内存以使它能够被引用到这个对象，相比于静态常量，enum会花费近2倍的内存空间。

到这里可能有人会问，现在的手机设备基本上都有比较大的内存空间，再多倍的内存创建也能顶的起吧？！emmu，这个不可否认，现在基本都是大内存的设备运行项目也流畅了许多，但是你不也不能否认的是，的确是还存在一部分人的设备还是比较小的内存的，开发不是只为一部分人......而且不要忘记，手机为一个应用所创建的内存空间还是有限的。

好吧，扯多了，回到原来思路上。

###### 4. 要怎么做？
刚才也说到，枚举在很大的程度上是限定了某些属性值得取值，当然还有其他别的作用。
既然是限定取值的，肯定有其他方法的是吧，咱们强大的Google大佬怎么会没有解决办法呢！

下面要用到的知识是：@IntDef 、@StringDef、@Retention、@interface，先上代码：
```
public class TestUtil {

    public static final int STRING_4G = 0;
    public static final int STRING_3G = 1;
    public static final int STRING_2G = 2;
    public static final int STRING_WIFI = 3;
    public static final int STRING_UNKNOWN = 4;

    @IntDef({STRING_4G,STRING_3G,STRING_2G,STRING_WIFI,STRING_UNKNOWN})
    @Retention(RetentionPolicy.SOURCE)
    public @interface NetWorkStatus{}

    public static String getNetWorkString(@NetWorkStatus int type) {
        if (type == STRING_4G) {
            return "NETWORK_4G";
        } else if (type == STRING_3G) {
            return "NETWORK_3G";
        } else if (type == STRING_2G) {
            return "NETWORK_2G";
        } else if (type == STRING_WIFI) {
            return "NETWORK_wifi";
        } else {
            return "unknown";
        }
    }
}
```
其中 @IntDef 定义了需要用到的固定常量，@Retention 注解告诉编译器在生成.class文件时不保留枚举注解数据。

而当我们调用这个方法的时候，如果直接传递数值参数的时候，lint编译器就会爆红色警告，如图：

![警告.png](http://upload-images.jianshu.io/upload_images/4463150-a4975eb7494d7572.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###### 5. 其他说明
1. 项目中如果存在大量的枚举使用，不妨试试这种方式代替使用
2. 如果用到枚举操作复杂的逻辑处理，按难易程度继续适当使用
3. 这种方式在直接传递数值时会警告提示，但是不影响实际的运行效果，还是可以传递其他数值进行编译运行，感觉不太安全，而枚举可以杜绝这情况的出现
4. Android内部很多都是差不多这样操作的，比如Toast的时间参数，如果直接传递0则会出现红色警告，进入源码会发现，多了个@的图标，如图：

![Toast.png](http://upload-images.jianshu.io/upload_images/4463150-46da48a7d583064e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![boom.png](http://upload-images.jianshu.io/upload_images/4463150-3485879c5cc22c0c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这跟自己定义的是一样的，也有@图标，如图：

![#￥#%.png](http://upload-images.jianshu.io/upload_images/4463150-40039e22d19ce53d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)