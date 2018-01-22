---
title: ConstraintLayout从相识到白头
date: 2017-11-26 19:07:05
tags: android
---
[ConstraintLayout](https://developer.android.com/reference/android/support/constraint/ConstraintLayout.html) 又名约束布局，出世于谷歌的大怀抱中，公布在2016年谷歌大会，现如今已经不是那个弱小病多的小子了，它被越来越多的人所认识。
<!--more-->
在Android studio2.3版本中，新建布局时已经默认的把constraintlayout作为根布局，这其中所代表的含义估计也不用我再次详说了。所以你还有什么理由不去学习？

截止到目前（2017-11-26），constraintlayout最新的版本是
```
 implementation 'com.android.support.constraint:constraint-layout:1.1.0-beta3'
```
这里的beta版本我在用的过程中暂时觉得还OK，如果你觉得beta的版本可能有坑，那你可以使用稳定的版本
```
 implementation 'com.android.support.constraint:constraint-layout:1.0.2'
```
用102版本的时候，似乎是不用加入谷歌的仓库，代码应该还是在jcenter仓库中，但是如果使用110版本的话就需要添加谷歌仓库
```
repositories {
    google()
}
```
当然，我下面的例子是使用的110版本，毕竟后面版本的东西多了很多实用的功能，比如 **Barrier** 这个属性，贼鸡儿好用，下面慢慢介绍吧。

整体的图片是这样的，可以在编辑视图中进行拖拉拽放等操作，想要学视图编辑的话，[点鸡这里去学习吧，别人的，比较详细，我下面说的是写代码的形式，毕竟传统的手活不能丢啊（别污啊，我认真的）](http://www.jianshu.com/p/6f8bee9c410d)

![雨蓝图.png](http://upload-images.jianshu.io/upload_images/4463150-aff7c000cc1dda00.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

呃，你也看到了，我本次想举个栗子，就是中间儿那个鸡，在放大点看看
![舅是这个.png](http://upload-images.jianshu.io/upload_images/4463150-3c084bb78e6a2af6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

布局真的很简单，如果用线性布局或者相对布局，似乎儿会有嵌套，是似乎儿啊，有高手也是不嵌套也能搞定的^_^

在看看我实现这个布局的代码吧，可能对于没接触过这个布局的人觉得，怎么代码辣么多，好吧，确实是多，不过都是精，咳咳，华

```
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <ImageView
        android:id="@+id/shopImage"
        android:layout_width="0dp"
        android:layout_height="100dp"
        android:layout_marginTop="10dp"
        android:src="@mipmap/test"
        app:layout_constraintDimensionRatio="1:1"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="@+id/guideLine2"
        app:layout_constraintTop_toTopOf="parent"
        />

    <android.support.constraint.Guideline
        android:id="@+id/guideLine2"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        app:layout_constraintGuide_percent="0.35"
        />

    <TextView
        android:id="@+id/shopName"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="冬季大棉衣"
        app:layout_constraintLeft_toLeftOf="@+id/guideLine2"
        app:layout_constraintTop_toTopOf="@+id/shopImage"
        app:layout_constraintBottom_toTopOf="@+id/shopAddress"
        />

    <TextView
        android:id="@+id/shopAddress"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="商品所在地点比较长"
        app:layout_constraintLeft_toLeftOf="@+id/guideLine2"
        app:layout_constraintTop_toBottomOf="@+id/shopName"
        app:layout_constraintBottom_toTopOf="@+id/shopPrice"
        />

    <TextView
        android:id="@+id/shopPrice"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="价格是10蚊鸡"
        app:layout_constraintLeft_toLeftOf="@+id/guideLine2"
        app:layout_constraintTop_toBottomOf="@+id/shopAddress"
        app:layout_constraintBottom_toBottomOf="@+id/shopImage"
        />

    <android.support.constraint.Barrier
        android:id="@+id/barrier1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:barrierDirection="right"
        app:constraint_referenced_ids="shopPrice,shopAddress,shopName"/>

    <Button
        android:id="@+id/cancelOrder"
        android:layout_width="wrap_content"
        android:layout_height="40dp"
        android:text="取消订单"
        app:layout_constraintLeft_toLeftOf="@+id/barrier1"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="@+id/shopImage"
        app:layout_constraintBottom_toTopOf="@+id/gotoPay"
        />

    <Button
        android:id="@+id/gotoPay"
        android:layout_width="wrap_content"
        android:layout_height="40dp"
        android:text="去付款"
        app:layout_constraintLeft_toLeftOf="@+id/barrier1"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/cancelOrder"
        app:layout_constraintBottom_toBottomOf="@+id/shopImage"
        />

    <android.support.constraint.Group
        android:id="@+id/buttonGroup"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:constraint_referenced_ids="gotoPay,cancelOrder"/>

</android.support.constraint.ConstraintLayout>
```
嗯，慢慢的来解析吧。先学习一下最基本的属性
1. layout_constraintLeft_toLeftOf  
2. layout_constraintLeft_toRightOf 
3. layout_constraintRight_toLeftOf
4. layout_constraintRight_toRightOf
5. layout_constraintTop_toTopOf
6. layout_constraintTop_toBottomOf
7. layout_constraintBottom_toTopOf
8. layout_constraintBottom_toBottomOf
9. layout_constraintStart_toStartOf
10. layout_constraintStart_toEndOf
11. layout_constraintEnd_toStartOf
12. layout_constraintEnd_toEndOf
13. layout_constraintBaseline_toBaselineOf

- 这个应该很好理解，相对布局，该控件在目标控件的xxx（上下左右）
- start_to_start_of跟left_to_left_of有什么区别？这个跟margin_left和margin_start的用法一样，还是不知道？？（黑人问号）
MarginStart指的是控件距离开头View部分的间距大小，MarginLeft则指的是控件距离左边View部分的间距大小，MarginEnd和MarginRight同理。
- 根局部就是constraintlayout，是Android支持包下的一个新布局，全类名记得填完
- baseline_to_baseline_of这个又是什么？
![这图是盗过来的.png](http://upload-images.jianshu.io/upload_images/4463150-7dd3b44ba6631de0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
其实baseline就是基线，比如两个textView，要使他们的文字排成一排，那么用baseLine对齐就好了。

![文本对齐.png](http://upload-images.jianshu.io/upload_images/4463150-e361e468a042286c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <TextView
        android:id="@+id/text1"
        android:layout_width="100dp"
        android:layout_height="100dp"
        android:background="@android:color/darker_gray"
        android:text="文本1"
        android:textSize="20sp"
        android:textColor="@android:color/black"
        android:gravity="center"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintBottom_toBottomOf="parent"
        />

    <TextView
        android:layout_width="180dp"
        android:layout_height="100dp"
        android:background="@android:color/darker_gray"
        android:text="文本2和文本1对其"
        android:textSize="20sp"
        android:textColor="@android:color/black"
        app:layout_constraintBaseline_toBaselineOf="@id/text1"
        app:layout_constraintLeft_toRightOf="@+id/text1"
        app:layout_constraintRight_toRightOf="parent"
        />

</android.support.constraint.ConstraintLayout>
```

现在在回到刚才的例子中，是不是看得很明白了？打住，好像还有几个不认识的，layout_constraintDimensionRatio又是什么？

有时候我们在设置图片的时候，因为图片太大了，想要按比例去缩放图片，你是不是去看看这个图片多大，然后拿计算器去算如果宽等于xx，那么高等于yyy？亦或者是在代码中去计算？别傻了，现在有了这个属性就搞定，layout_constraintDimensionRatio默认定义的是宽高比，如1:2就是宽高比例1比2，但是有个前提是，如果已知宽100dp，想要设置宽高比，那么高必须设置为0dp，不然比例设置没效果，相反成立。

什么？我偏偏要设置高宽比呢，就不喜欢宽高比，贼鸡儿跟你玩，别怕，谷歌爸爸不是吃素的，设置成这样也行
```
 app:layout_constraintDimensionRatio="h,1:2"
```
![宽高比例1:2.png](http://upload-images.jianshu.io/upload_images/4463150-371ee23e8a8ee8f5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![宽高比例2:1.png](http://upload-images.jianshu.io/upload_images/4463150-5e6b87e837d28721.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

[Guideline](https://developer.android.com/reference/android/support/constraint/Guideline.html) 又是什么？咳咳，我有句mmp不知当讲不当讲。

guideline是constraintlayout的一个工具，只能用于该布局中，在其他布局中使用无效。这个东西我理解的话，相当于margin，也可以叫做事辅助线。图中的虚线就是guideline

![guideline.png](http://upload-images.jianshu.io/upload_images/4463150-1e1b3750af87d51f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="距离左边100dp1"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        android:layout_marginTop="20dp"
        android:layout_marginLeft="100dp"
        />

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="距离左边100dp2"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        android:layout_marginTop="80dp"
        android:layout_marginLeft="100dp"
        />

    <android.support.constraint.Guideline
        android:id="@+id/guide1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        app:layout_constraintGuide_begin="100dp"/>

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="距离左边100dp3"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintLeft_toLeftOf="@+id/guide1"
        android:layout_marginTop="150dp"
        />

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="距离左边100dp4"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintLeft_toLeftOf="@+id/guide1"
        android:layout_marginTop="210dp"
        />

</android.support.constraint.ConstraintLayout>
```
[Barrier](https://developer.android.com/reference/android/support/constraint/Barrier.html)这个呢，在1.0.x版本中这个属性是不存在的，只有在1.1.x版本后存在。但是这个属性真的贼鸡儿好玩

具体的应用场景我就不说了，先上图让你明明白白的。
![右边的按钮在左边的右边，以最长的那个为准.png](http://upload-images.jianshu.io/upload_images/4463150-b33d3496d0f2b245.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <Button
        android:id="@+id/button1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="这个是按钮1"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        android:layout_marginLeft="20dp"
        android:layout_marginTop="20dp"
        />

    <Button
        android:id="@+id/button2"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="你是不是没看过那么长的按钮呢"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/button1"
        android:layout_marginLeft="20dp"
        android:layout_marginTop="20dp"
        />

    <android.support.constraint.Barrier
        android:id="@+id/barrier1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:barrierDirection="right"
        app:constraint_referenced_ids="button2,button1"/>

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="我在他们的后面"
        app:layout_constraintLeft_toLeftOf="@+id/barrier1"
        tools:layout_editor_absoluteY="40dp"/>


    <Button
        android:id="@+id/button3"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="你是不是没看过那么长的按钮呢"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        android:layout_marginLeft="20dp"
        android:layout_marginTop="170dp"
        />

    <Button
        android:id="@+id/button4"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="这个按钮4"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/button3"
        android:layout_marginLeft="20dp"
        android:layout_marginTop="20dp"
        />

    <android.support.constraint.Barrier
        android:id="@+id/barrier2"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:barrierDirection="right"
        app:constraint_referenced_ids="button3,button4"/>


    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="我还在右边"
        app:layout_constraintLeft_toLeftOf="@+id/barrier2"
        tools:layout_editor_absoluteY="208dp"/>
</android.support.constraint.ConstraintLayout>
```

[constraint.Group](https://developer.android.com/reference/android/support/constraint/Group.html) 这个比较简单，可以理解为一个控件把一些其他控件包起来，一起做一些羞羞的事情，比如咳咳咳，想啥呢，我是说把它们藏起来。
![处理前.png](http://upload-images.jianshu.io/upload_images/4463150-1b900ddbc0e510c3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![处理后.png](http://upload-images.jianshu.io/upload_images/4463150-8584645542711f7a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <Button
        android:id="@+id/button1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="这个是按钮1"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        android:layout_marginLeft="20dp"
        android:layout_marginTop="20dp"
        />

    <Button
        android:id="@+id/button2"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="这个是按钮2"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/button1"
        android:layout_marginLeft="20dp"
        android:layout_marginTop="20dp"
        />

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="这个是按钮3"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/button2"
        android:layout_marginLeft="20dp"
        android:layout_marginTop="20dp"
        />

    <android.support.constraint.Group
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:visibility="invisible"
        app:constraint_referenced_ids="button1,button2"/>

</android.support.constraint.ConstraintLayout>
```
很简单是不是，相当于一同处理了。以上的一些属性都是在相对布局中相似的，你有没有疑问，线性布局LinearLayout的权重Weight属性，怎么才能在约束布局中体现呢，别急，慢慢玩鸡儿。

**Chains**隆重出场，好像没人欢迎，赶快鼓掌，呃，没人认识！！

不介绍什么是链条了，其实就是你依赖我我依赖你，你侬我侬的一种状态，互相依赖，使得他们存在一定的关系。默认的链条样式是Spread chain.
![所有链条的样式.png](http://upload-images.jianshu.io/upload_images/4463150-02b6aa7650862021.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

下面我一一举例子说明，其实就实现上面的几种可能。
![Spread chain.png](http://upload-images.jianshu.io/upload_images/4463150-7d0c10d2506dbbf9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <android.support.constraint.Guideline
        android:id="@+id/guide1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        app:layout_constraintGuide_begin="10dp"/>

    <Button
        android:id="@+id/button1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="我做饭"
        app:layout_constraintTop_toTopOf="@+id/guide1"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toLeftOf="@+id/button2"
        />

    <Button
        android:id="@+id/button2"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="我洗菜"
        app:layout_constraintTop_toTopOf="@+id/guide1"
        app:layout_constraintLeft_toRightOf="@+id/button1"
        app:layout_constraintRight_toLeftOf="@+id/button3"
        />

    <Button
        android:id="@+id/button3"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="那我做菜"
        app:layout_constraintTop_toTopOf="@+id/guide1"
        app:layout_constraintLeft_toRightOf="@+id/button2"
        app:layout_constraintRight_toRightOf="parent"
        />

</android.support.constraint.ConstraintLayout>
```
在 button1 中加入layout_constraintHorizontal_chainStyle属性就能变成不一样的风格
```
<Button
        android:id="@+id/button1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="我做饭"
        app:layout_constraintHorizontal_chainStyle="spread_inside"
        app:layout_constraintTop_toTopOf="@+id/guide1"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toLeftOf="@+id/button2"
        />
```
![Spread inside chain.png](http://upload-images.jianshu.io/upload_images/4463150-ff6892fd80329789.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

再变Packed chain
```
<Button
        android:id="@+id/button1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="我做饭"
        app:layout_constraintHorizontal_chainStyle="packed"
        app:layout_constraintTop_toTopOf="@+id/guide1"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toLeftOf="@+id/button2"
        />
```
![Packed chain.png](http://upload-images.jianshu.io/upload_images/4463150-827ca46a9ae46b9a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

再变Spread weight chain
```
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <android.support.constraint.Guideline
        android:id="@+id/guide1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        app:layout_constraintGuide_begin="10dp"/>

    <Button
        android:id="@+id/button1"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:text="我做饭"
        app:layout_constraintHorizontal_chainStyle="spread"
        app:layout_constraintHorizontal_weight="3"
        app:layout_constraintTop_toTopOf="@+id/guide1"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toLeftOf="@+id/button2"
        />

    <Button
        android:id="@+id/button2"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:text="我洗菜"
        app:layout_constraintTop_toTopOf="@+id/guide1"
        app:layout_constraintHorizontal_weight="2"
        app:layout_constraintLeft_toRightOf="@+id/button1"
        app:layout_constraintRight_toLeftOf="@+id/button3"
        />

    <Button
        android:id="@+id/button3"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="那我做菜"
        app:layout_constraintTop_toTopOf="@+id/guide1"
        app:layout_constraintLeft_toRightOf="@+id/button2"
        app:layout_constraintRight_toRightOf="parent"
        />

</android.support.constraint.ConstraintLayout>
```
![weighted chain.png](http://upload-images.jianshu.io/upload_images/4463150-ad0ad3492b1a6207.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

再变packed chain with bias，什么是bias，等会解析
```
<Button
        android:id="@+id/button1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="我做饭"
        app:layout_constraintHorizontal_chainStyle="packed"
        app:layout_constraintHorizontal_bias="0.2"
        app:layout_constraintTop_toTopOf="@+id/guide1"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toLeftOf="@+id/button2"
        />
```
![packed chain with bias.png](http://upload-images.jianshu.io/upload_images/4463150-9b15f483b8f4b1d7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

对，你也看到啦，实际上是加了这个layout_constraintHorizontal_bias属性，它是啥？它是牛x的百分比属性，也叫做偏移，有横向的偏移，也有竖直方向的偏移。
```
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <Button
        android:id="@+id/button1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="居中贴上显示"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        />

    <Button
        android:id="@+id/button2"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="距离左边是屏幕的百分之30"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintHorizontal_bias="0.3"
        />

    <Button
        android:id="@+id/button3"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="贴近右边"
        app:layout_constraintTop_toBottomOf="@+id/button2"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintHorizontal_bias="1"
        />

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="贴底部显示"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintVertical_bias="1"
        />
</android.support.constraint.ConstraintLayout>
```
![想怎么贴就怎么玩.png](http://upload-images.jianshu.io/upload_images/4463150-a69bdab6d86abb2f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

官方文档中还有 [ConstraintSet](https://developer.android.com/reference/android/support/constraint/ConstraintSet.html) 和 [Placeholder](https://developer.android.com/reference/android/support/constraint/Placeholder.html) 这个东西，不过还没接触到，就先不介绍了。看起来Placeholder这玩儿似乎还挺好玩的

呃，最后说两句吧，自从学了这布局，我就抛弃了其他布局了，官方推荐的，没毛病，有人测试过性能方面的，不过我没测试，[这是别人家的测试性能对比，我觉得没啥的，只要好用，和以前的差不多也行](http://www.jianshu.com/p/fae1d533597b)