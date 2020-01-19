---
title: 使用SkeletonScreen提升用户感知体验
date: 2018-04-10 17:29:46
tags: Android
---

一直以来，无论是哪个端的应用中，为了提升应用的加载等待这段时间的用户感知体验，各种奇门遁甲之术层出不穷。比如菊花加载，或者是自定义的一些图片等，如下图所示：
<!--more-->
![菊花加载.gif](https://upload-images.jianshu.io/upload_images/4463150-6c5ce60b1dfc59dc.gif?imageMogr2/auto-orient/strip)

在很多的应用的交互中，这种菊花的加载的设计已然成为了一种习惯。

不过，有的应用为了追求体验更加完美的加载方式，比如Facebook和支付宝、饿了么、掘金等，使用的是另一种加载等待方式，这种方式有个统一的名字叫Skeleton Screen Loading。

![事例.gif](https://upload-images.jianshu.io/upload_images/4463150-32ad85ce654c48b1.gif?imageMogr2/auto-orient/strip)

好处？
1. 页面完全渲染完成之前，用户会看到一个样式简单，描绘了当前页面的大致框架，感知到页面正在逐步加载，加载完成后，最终骨架屏中各个占位部分将被真实的数据替换。
2. 提升用户体验。

好了，其实也只想告诉你们该怎么用而已，上面的都是copy的。为啥？我懒的说。

使用：
大部分的轮子都有现成的，不信你上GitHub看看搜搜。
1. 
```
dependencies {
       implementation 'com.ethanhua:skeleton:1.1.0'
       implementation 'io.supercharge:shimmerlayout:2.0.0'
    }
```
2. 
For RecyclerView:
```
skeletonScreen = Skeleton.bind(recyclerView)
                              .adapter(adapter)
                              .load(R.layout.item_skeleton_news)
                              .show();
```
 For View:
```
skeletonScreen = Skeleton.bind(rootView)
                              .load(R.layout.layout_img_skeleton)
                              .show();
```
3.
More Config:
```
.shimmer(true)      // whether show shimmer animation.                      default is true
.count(10)          // the recycler view item count.                        default is 10
.color(color)       // the shimmer color.                                   default is #a2878787
.angle(20)          // the shimmer angle.                                   default is 20;
.duration(1000)     // the shimmer animation duration.                      default is 1000;
.frozen(false)      // whether frozen recyclerView during skeleton showing  default is true; 
```
4. 
当数据返回后
 ```
skeletonScreen.hide()
```
5.
搞定。[demo](https://github.com/lamandys/skeletonDemo)
6. 
例子：
```
main.java
private void init() {
        LinearLayoutManager manager = new LinearLayoutManager(getApplicationContext());
        manager.setOrientation(LinearLayout.VERTICAL);
        mRecyclerView.setLayoutManager(manager);
        mRecyclerView.setHasFixedSize(true);
        MyAdapter adapter = new MyAdapter(titles);
        adapter.setOnClickItem(new MyAdapter.IOnClickItem() {
            @Override
            public void onClick() {
                startActivity(new Intent(MainActivity.this,ViewActivity.class));
            }
        });
        mRecyclerView.setAdapter(adapter);
        skeletonScreen = Skeleton.bind(mRecyclerView)
                .adapter(adapter)
                .load(R.layout.rv_item_skeleton)
                .show();
        new Handler(getMainLooper()).postDelayed(new Runnable() {
            @Override
            public void run() {
                skeletonScreen.hide();
            }
        },5000);
    }
```

```
rv_item_skeleton.xml

<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="90dp">

    <ImageView
        android:id="@+id/item_img"
        android:layout_width="60dp"
        android:layout_height="0dp"
        android:layout_marginLeft="20dp"
        android:background="#ececec"
        android:contentDescription="@null"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintDimensionRatio="1:1"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <TextView
        android:id="@+id/item_text"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginLeft="20dp"
        android:layout_marginRight="20dp"
        android:background="#ececec"
        app:layout_constraintBottom_toTopOf="@+id/item_place"
        app:layout_constraintLeft_toRightOf="@+id/item_img"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="@+id/item_img"
        tools:text="111111" />

    <TextView
        android:id="@+id/item_place"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginRight="20dp"
        android:background="#ececec"
        app:layout_constraintBottom_toBottomOf="@+id/item_img"
        app:layout_constraintLeft_toLeftOf="@+id/item_text"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/item_text" />

</android.support.constraint.ConstraintLayout>
```


