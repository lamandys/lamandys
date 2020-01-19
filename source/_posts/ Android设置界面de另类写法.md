---
title: Android设置界面de另类写法
date: 2017-05-27 11:26:38
tags: Android
---
###### 前言
话说，工作中总是会接到不同的需求，那么，在你看到需求时，你会去想该怎样去实现吗？有想过，实现这个功能大概有多少种方法吗？哪一种比较快？哪一种性能会好点？或者是哪一种做法比较优雅？
<!--more-->
###### 需求
比如，要实现下面这个设置界面的话，你会用哪种方式去实现？

![需求.png](http://upload-images.jianshu.io/upload_images/4463150-18e762e784944ab2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


###### 分析
有人说，这个界面那么简单，还用说嘛！！！有人脱口而出，或者说是习惯性的做法，说直接在布局中写啊。

那好，既然这样，那我就把在布局中实现的代码贴上来，你看看究竟有什么特别之处。

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@color/bazi_fragment_bg"
    android:orientation="vertical">

    <ScrollView
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:orientation="vertical">


            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="60dp"
                android:layout_marginTop="20dp"
                android:background="@android:color/white"
                android:orientation="horizontal">

                <ImageView
                    android:layout_width="wrap_content"
                    android:layout_height="60dp"
                    android:layout_gravity="center"
                    android:layout_marginLeft="20dp"
                    android:layout_marginRight="20dp"
                    android:src="@drawable/bazi_person_center_news" />

                <TextView
                    android:layout_width="0dp"
                    android:layout_height="60dp"
                    android:layout_weight="1"
                    android:gravity="center|start"
                    android:text="功能1" />

                <ImageView
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:layout_gravity="center"
                    android:layout_marginRight="20dp"
                    android:src="@drawable/person_center_right" />

            </LinearLayout>

            <View
                android:layout_width="match_parent"
                android:layout_height="1dp" />

            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="60dp"
                android:background="@android:color/white"
                android:orientation="horizontal">

                <ImageView
                    android:layout_width="wrap_content"
                    android:layout_height="60dp"
                    android:layout_gravity="center"
                    android:layout_marginLeft="20dp"
                    android:layout_marginRight="20dp"
                    android:src="@drawable/bazi_person_center_news" />

                <TextView
                    android:layout_width="0dp"
                    android:layout_height="60dp"
                    android:layout_weight="1"
                    android:gravity="center|start"
                    android:text="功能2" />

                <ImageView
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:layout_gravity="center"
                    android:layout_marginRight="20dp"
                    android:src="@drawable/person_center_right" />

            </LinearLayout>

            <View
                android:layout_width="match_parent"
                android:layout_height="1dp" />

            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="60dp"
                android:background="@android:color/white"
                android:orientation="horizontal">

                <ImageView
                    android:layout_width="wrap_content"
                    android:layout_height="60dp"
                    android:layout_gravity="center"
                    android:layout_marginLeft="20dp"
                    android:layout_marginRight="20dp"
                    android:src="@drawable/bazi_person_center_news" />

                <TextView
                    android:layout_width="0dp"
                    android:layout_height="60dp"
                    android:layout_weight="1"
                    android:gravity="center|start"
                    android:text="功能3" />

                <ImageView
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:layout_gravity="center"
                    android:layout_marginRight="20dp"
                    android:src="@drawable/person_center_right" />

            </LinearLayout>

            <View
                android:layout_width="match_parent"
                android:layout_height="1dp" />

            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="60dp"
                android:background="@android:color/white"
                android:orientation="horizontal">

                <ImageView
                    android:layout_width="wrap_content"
                    android:layout_height="60dp"
                    android:layout_gravity="center"
                    android:layout_marginLeft="20dp"
                    android:layout_marginRight="20dp"
                    android:src="@drawable/bazi_person_center_news" />

                <TextView
                    android:layout_width="0dp"
                    android:layout_height="60dp"
                    android:layout_weight="1"
                    android:gravity="center|start"
                    android:text="功能4" />

                <ImageView
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:layout_gravity="center"
                    android:layout_marginRight="20dp"
                    android:src="@drawable/person_center_right" />

            </LinearLayout>

            <View
                android:layout_width="match_parent"
                android:layout_height="1dp" />

            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="60dp"
                android:background="@android:color/white"
                android:orientation="horizontal">

                <ImageView
                    android:layout_width="wrap_content"
                    android:layout_height="60dp"
                    android:layout_gravity="center"
                    android:layout_marginLeft="20dp"
                    android:layout_marginRight="20dp"
                    android:src="@drawable/bazi_person_center_news" />

                <TextView
                    android:layout_width="0dp"
                    android:layout_height="60dp"
                    android:layout_weight="1"
                    android:gravity="center|start"
                    android:text="功能5" />

                <ImageView
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:layout_gravity="center"
                    android:layout_marginRight="20dp"
                    android:src="@drawable/person_center_right" />

            </LinearLayout>

            <View
                android:layout_width="match_parent"
                android:layout_height="1dp" />

            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="60dp"
                android:background="@android:color/white"
                android:orientation="horizontal">

                <ImageView
                    android:layout_width="wrap_content"
                    android:layout_height="60dp"
                    android:layout_gravity="center"
                    android:layout_marginLeft="20dp"
                    android:layout_marginRight="20dp"
                    android:src="@drawable/bazi_person_center_news" />

                <TextView
                    android:layout_width="0dp"
                    android:layout_height="60dp"
                    android:layout_weight="1"
                    android:gravity="center|start"
                    android:text="功能6" />

                <ImageView
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:layout_gravity="center"
                    android:layout_marginRight="20dp"
                    android:src="@drawable/person_center_right" />

            </LinearLayout>

            <View
                android:layout_width="match_parent"
                android:layout_height="1dp" />

            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="60dp"
                android:background="@android:color/white"
                android:orientation="horizontal">

                <ImageView
                    android:layout_width="wrap_content"
                    android:layout_height="60dp"
                    android:layout_gravity="center"
                    android:layout_marginLeft="20dp"
                    android:layout_marginRight="20dp"
                    android:src="@drawable/bazi_person_center_news" />

                <TextView
                    android:layout_width="0dp"
                    android:layout_height="60dp"
                    android:layout_weight="1"
                    android:gravity="center|start"
                    android:text="功能7" />

                <ImageView
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:layout_gravity="center"
                    android:layout_marginRight="20dp"
                    android:src="@drawable/person_center_right" />

            </LinearLayout>


            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="60dp"
                android:layout_marginTop="20dp"
                android:background="@android:color/white"
                android:orientation="horizontal">

                <ImageView
                    android:layout_width="wrap_content"
                    android:layout_height="60dp"
                    android:layout_gravity="center"
                    android:layout_marginLeft="20dp"
                    android:layout_marginRight="20dp"
                    android:src="@drawable/bazi_person_center_news" />

                <TextView
                    android:layout_width="0dp"
                    android:layout_height="60dp"
                    android:layout_weight="1"
                    android:gravity="center|start"
                    android:text="功能8" />

                <ImageView
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:layout_gravity="center"
                    android:layout_marginRight="20dp"
                    android:src="@drawable/person_center_right" />

            </LinearLayout>
        </LinearLayout>
    </ScrollView>

</LinearLayout>
```

看到了什么？代码量之多，嵌套之深！重要的是，其实他们都一样的布局啊！！！当然，这种方式 也是那些赶需求的人最爽的做法，为什么？它快啊！直接复制粘贴。。。

那么，究竟有什么做法是可以稍微爽一点而且又有点优雅的呢？好吧，我说的优雅不是你们想的那个[优*雅*]，[呜呜专属表情.png]

那么说说我的想法吧，既然它的一个特点是，布局的item都是相同的布局方式，想到没有？

![](http://upload-images.jianshu.io/upload_images/4463150-f8720a6dc677db7a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

好吧，我想到的是用listView来代替这整个相似的布局，只需要一个item布局就能搞定的事情为什么要复制一大堆？

说起listView，好像挺简单的，不过大家都在用ＲＶ来代替了，那么我这里也用ＲＶ来实现吧。

直接look代码呗：
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@color/fragment_bg">

    <android.support.v7.widget.RecyclerView
        android:background="@android:color/white"
        android:layout_marginTop="20dp"
        android:id="@+id/MainMeRV"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

    </android.support.v7.widget.RecyclerView>

</LinearLayout>
```

子item的布局：
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    xmlns:tools="http://schemas.android.com/tools"
    android:orientation="horizontal"
    android:layout_marginBottom="1dp">

    <ImageView
        android:id="@+id/personCenterItemIcon"
        android:layout_gravity="center"
        android:layout_width="wrap_content"
        android:layout_height="60dp"
        android:layout_marginLeft="20dp"
        android:layout_marginRight="20dp"
        tools:src="@drawable/ic_launcher"/>

    <TextView
        android:id="@+id/personCenterItemTitle"
        android:layout_width="0dp"
        android:layout_weight="1"
        android:layout_height="60dp"
        android:gravity="center|start"/>

    <ImageView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:src="@drawable/person_center_right"
        android:layout_marginRight="20dp"/>

</LinearLayout>
```

![](http://upload-images.jianshu.io/upload_images/4463150-43f9b68dc87ce5d0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

搞定。之后的操作就是设置Adapter和绑定数据之类的就不详细写了，大家都会。下面来说说其他的东西。

不知大家有没有仔细的看到上面的第一张需求图中，每个item下面都会有一个分割线，而且这条线并不是填充屏幕宽度的。没看到？再看一次咯。
![需求.png](http://upload-images.jianshu.io/upload_images/4463150-18e762e784944ab2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

那么在直接用xml布局中实现的话，非常简单是吧，但是在RecyclerView中要实现列表的分割线，可不是一件简单的事（看用那种方式），有的人喜欢在每个item下面直接在增加一个 View ，当然也是能实现这个效果，但我不建议这样使用。

在RV中实现分割线的其中一种做法是，继承RecyclerView.ItemDecoration实现onDraw方法：
```
import android.content.Context;
import android.graphics.Canvas;
import android.graphics.Rect;
import android.graphics.drawable.Drawable;
import android.os.Build;
import android.support.v7.widget.RecyclerView;
import android.view.View;

public class DrawerDecoration extends RecyclerView.ItemDecoration {

    Drawable mDivider;

    public DrawerDecoration(Context context) {
        mDivider = context.getResources().getDrawable(R.drawable.recyclerview_line);
    }

    @Override
    public void onDraw(Canvas c, RecyclerView parent, RecyclerView.State state) {
        int left = parent.getPaddingLeft();
        int right = parent.getWidth() - parent.getPaddingRight();

        int childCount = parent.getChildCount();
        for (int i = 0; i < childCount; i++) {
            View child = parent.getChildAt(i);
            RecyclerView.LayoutParams params = (RecyclerView.LayoutParams) child.getLayoutParams();
            int top = child.getBottom() + params.bottomMargin;
            int bottom = top + mDivider.getIntrinsicHeight();
            mDivider.setBounds(left, top, right, bottom);
            mDivider.draw(c);
        }
    }

    @Override
    public void getItemOffsets(Rect outRect, View view, RecyclerView parent, RecyclerView.State state) {
        outRect.bottom = mDivider.getIntrinsicHeight();
    }
}
```

这种常规的方式，做出的效果是，能在RV的每个item下面添加一条分割线，但是注意到的是，这条线的宽度是填充屏幕的，想要做出像需求图中的效果，就需要在drawer.setBounds()方法中操作相应的距离即可、
```
mDivider.setBounds(parent.getLeft()+20, top, parent.getRight()-20, bottom);
```

这样做还有几个地方是有问题的：
1. 列表的最后一个item下面是不需要有分割线的，可以做判断不画最后一条
2. 如果注意到需求图的话，就知道最后的那个item距离上一个item是有一段距离的，怎么实现？


![](http://upload-images.jianshu.io/upload_images/4463150-2bfd73e57c97d588.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我的实现是在画倒数第二个分割线的时候，把该分割线的高度设置为那一段距离，把宽度设置为填充屏幕。

哦，你以为这样就对了吗？你看看效果就知道了。

![](http://upload-images.jianshu.io/upload_images/4463150-45b9fd331921e274.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

看到了什么？没看到？


![](http://upload-images.jianshu.io/upload_images/4463150-38d4a1d11475aefc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

第8个item的文字位置，由于设置分割线把item的布局往下移动了，但是实际上文字的距离并没有进行位移。

怎么办？

难道不能这样去画那个分割线吗？那还有什么办法？

要建两个不一样的布局，第一个是普通的item布局，第二个是上面有个margin的布局？然后通过adapter的个getViewType()方法判断是不是最后一个，然后设置不同的布局？

其实好像不用那么麻烦，不过也有点麻烦，我的做法是，直接在adapter中判断是不是最后一个item，是的话直接给个topMargin等于分割线的高度就好啦。完美ing

###### 总结
如果需求中的布局是普通的列表一样，没有高度之分，也没有分割线的margin之类的，实现起来还是挺方便的。但是如果想对比较复杂一些的布局，用这种方式就不太好了。

相对来说嘛，不同的需求，实现的方式可能不一样，所以在接到需求时，我们不仿去想想，究竟怎样实现才是好的实现。

最后，感谢你的阅读，如果觉得哪里有问题的话，欢迎指出和批评建议。
