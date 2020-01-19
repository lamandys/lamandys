---
title: 如何简单的为recyclerview增加上拉加载功能
date: 2018-05-11 10:16:51
tags: Android
---
老项目里想加入分页的功能？要推翻以前写的一切逻辑？想引入上拉加载下拉刷新库又怕影响到其他代码逻辑？来试试这个不需要怎么更改代码逻辑就能实现的简单的分页效果吧！
<!-- more -->

### 为什么要自己手动去实现这个功能
也许有人会问，现在网上那么多轮子，每一个都比自己写的要好得多，而且都还经过大量的功能测试的，那为什么还要去自己实现这么一个功能呢？

其实也很好理解，现在大部分的功能在实现的时候也会去考虑是否使用别人做的轮子去用，有时候也的确非常的方便。但是对于一些古老的项目，里面的代码牵连着比较复杂的功能逻辑结构，如果要把之前写的一套去除的话，那工作量不可言喻。

### 自己实现和别人的轮子比谁更好？
这个我只能说，其实并不是所有的轮子都适合每个项目去用的。当然，可能我写的这个东西在GitHub或者是其他的网站上有着相似的功能或者就是一模一样的，只不过是我这个是我自己写的而已，也并不适用于其他的项目，这里仅提供一种实现的思路，也仅此而已。

### 实现前要考虑什么问题？
首先，我去写这个东西的前提是在旧项目中需要的，所以不能对现有的代码逻辑有过多的改动，对于接口和加载更多的新功能可以改动外，adapter里面的逻辑我觉得是不能更改的。所以最重要的就是在不改动原有adapter的逻辑前提下去实现。

其次，如果项目中有删除item功能的操作话，也许需要考虑删除后item变动的影响，我这个项目中没有删除的功能，只是展示出来，所以下面的功能实现暂不考虑。

### 如何动手？
动手之前需要考虑下，既然不能影响到原有的布局，假如原有的adapter有了一个头部个底部不一样的item，即是headerview和footerview，那么怎么才能在原有的基础上再新增一个底部的布局呢？

最常用的并且是最容易想得到的就是再在下面根据type来新增一个，在原有的列表数据上新增一个item，type是loadmore类型的。我们也不去想什么高大上的操作了，能实现出效果的才是硬道理，毕竟我们并没有太多的时间去进行完善的开发。好了，开干！

为了可以更好的使用，我这里使用外部传递view进来的方式，想要什么图案样式的自己定义好再传进来，当然我也没有做动画的效果，仅仅只是简单的图片。定义一个footerview和没有数据时展示的view
```
private View mFooterView, mNoMoreDataView;
```
定义加载更多的类型
```
private final int ITEM_TYPE_FOOTER = 98;
```
定义一个baseAdapter,不详细说了，因为是简单的增加这个功能而已，可能有bug
```
package com.mmc.fengshui.pass.order.adapter;

import android.content.Context;
import android.support.annotation.IntDef;
import android.support.v7.widget.RecyclerView;
import android.text.TextUtils;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.FrameLayout;
import android.widget.Toast;

import com.mmc.fengshui.R;

import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;

import oms.mmc.util.MMCUtil;

/**
 * 作者：子川
 * 时间：2018/5/9 下午12:17
 * 邮箱：lantianhua@linghit.com
 * 说明：recyclerView适配器封装
 */
public abstract class RecyclerViewAdapterWrapper extends RecyclerView.Adapter<RecyclerView.ViewHolder> {
    /**
     * 布局中的头部item
     */
    private final int ITEM_TYPE_HEADER = 99;
    /**
     * 布局中的脚部item
     */
    private final int ITEM_TYPE_FOOTER = 98;
    /**
     * 头部view，脚部view，还有就是没有更多数据时的view
     */
    private View mHeaderView, mFooterView, mNoMoreDataView;
    /**
     * 是否正在加载更多
     */
    private boolean isLoadingMore = false;
    /**
     * 是否没有更多数据了
     */
    private boolean isNoMoreData = false;
    /**
     * 上面的两种状态
     */
    public static final int STATE_LOAD_MORD = 1;
    public static final int STATE_NO_MORE_DATA = 2;
    /**
     * 接口形式代替枚举
     */
    @IntDef({STATE_LOAD_MORD, STATE_NO_MORE_DATA})
    @Retention(RetentionPolicy.SOURCE)
    public @interface ILoadMoreState {
    }

    @Override
    public final RecyclerView.ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        if (mHeaderView != null && viewType == ITEM_TYPE_HEADER) {
            // 头部view功能没有测试过，具体使用后者再处理
            return new HeaderOrFooterViewHolder(mHeaderView);
        } else if (mFooterView != null && viewType == ITEM_TYPE_FOOTER) {
            // 底部的控制，创建一个空的视图，根据状态去加载不同的view视图显示和隐藏
            return new HeaderOrFooterViewHolder(createNewPlaceHolderView(parent.getContext()));
        } else {
            // 返回到之前的状态
            return onCreateHolderView(parent, viewType);
        }
    }

    /**
     * 创建一个空的布局视图
     */
    private View createNewPlaceHolderView(Context context) {
        FrameLayout frameLayout = new FrameLayout(context);
        ViewGroup.LayoutParams lp = getLayoutParams(context);
        lp.height = ViewGroup.LayoutParams.WRAP_CONTENT;
        frameLayout.setLayoutParams(lp);
        return frameLayout;
    }

    public abstract RecyclerView.ViewHolder onCreateHolderView(ViewGroup parent, int viewType);

    public abstract void onBindHolderView(RecyclerView.ViewHolder holder, int position);

    public abstract int getItemType(int position);

    public abstract int getRealItemCount();

    @Override
    public final void onBindViewHolder(RecyclerView.ViewHolder holder, int position) {
        if (isHeaderViewPos(position)) {
            return;
        }
        if (isFooterViewPos(position)) {
            ViewGroup root = ((ViewGroup) ((HeaderOrFooterViewHolder) holder).itemView);
            Context context = root.getContext();
            if (isNoMoreData) {
                root.setVisibility(View.VISIBLE);
                root.removeAllViews();
                root.addView(getNoMoreView(context), getLayoutParams(context));
            } else if (isLoadingMore) {
                root.setVisibility(View.VISIBLE);
                root.removeAllViews();
                root.addView(mFooterView, getLayoutParams(context));
            } else {
                root.setVisibility(View.GONE);
            }
            return;
        }
        onBindHolderView(holder, position - getHeaderCount());
    }

    private View getNoMoreView(Context context) {
        if (mNoMoreDataView != null) {
            return mNoMoreDataView;
        }
        return LayoutInflater.from(context).inflate(R.layout.activity_recyclerview_nomoredata, null);
    }

    public RecyclerViewAdapterWrapper setLoadMoreState(@ILoadMoreState int state) {
        if (state == STATE_LOAD_MORD) {
            isLoadingMore = true;
            isNoMoreData = false;
        } else if (state == STATE_NO_MORE_DATA) {
            isLoadingMore = false;
            isNoMoreData = true;
        } else {
            isLoadingMore = false;
            isNoMoreData = false;
        }
        return this;
    }

    @Override
    public final int getItemCount() {
        return getHeaderCount() + getFooterCount() + getRealItemCount();
    }

    @Override
    public final int getItemViewType(int position) {
        if (isHeaderViewPos(position)) {
            return ITEM_TYPE_HEADER;
        } else if (isFooterViewPos(position)) {
            return ITEM_TYPE_FOOTER;
        }
        return getItemType(position - getHeaderCount());
    }

    public RecyclerViewAdapterWrapper setHeaderView(View view) {
        this.mHeaderView = view;
        return this;
    }

    public RecyclerViewAdapterWrapper setHeaderView(Context context, int layoutRes) {
        this.mHeaderView = LayoutInflater.from(context).inflate(layoutRes, null);
        return this;
    }

    public RecyclerViewAdapterWrapper setFooterView(View view) {
        this.mFooterView = view;
        return this;
    }

    public RecyclerViewAdapterWrapper setFooterView(Context context, int layoutRes) {
        this.mFooterView = LayoutInflater.from(context).inflate(layoutRes, null);
        return this;
    }

    public RecyclerViewAdapterWrapper setNoMoreDataView(View view) {
        this.mNoMoreDataView = view;
        return this;
    }

    public RecyclerViewAdapterWrapper setNoMoreDataView(Context context, int layoutRes) {
        this.mNoMoreDataView = LayoutInflater.from(context).inflate(layoutRes, null);
        return this;
    }

    private boolean isHeaderViewPos(int position) {
        return position < getHeaderCount();
    }

    private boolean isFooterViewPos(int position) {
        return position >= getHeaderCount() + getRealItemCount();
    }

    private ViewGroup.LayoutParams getLayoutParams(Context context) {
        return new ViewGroup.LayoutParams(ViewGroup.LayoutParams.MATCH_PARENT, MMCUtil.dipTopx(context, 35));
    }

    private int getHeaderCount() {
        return mHeaderView == null ? 0 : 1;
    }

    private int getFooterCount() {
        return mFooterView == null ? 0 : 1;
    }

    private class HeaderOrFooterViewHolder extends RecyclerView.ViewHolder {

        public HeaderOrFooterViewHolder(View itemView) {
            super(itemView);
        }
    }
}

```

嗯，就这样，看一下就能懂了。

因为这个功能是连着SwipeRefreshLayout来使用的，实际是为SwipeRefreshLayout增加上拉加载的功能，看代码：
```
public class SwipeRefreshWithLoadMoreLayout extends SwipeRefreshLayout {
    /**
     * RecyclerView对象
     */
    private RecyclerView mRecyclerView;
    /**
     * 是否正在加载更多
     */
    private boolean isLoadingMore = false;
    /**
     * 是否允许加载更多
     */
    private boolean isEnableLoadMore = true;
    /**
     * 加载更多监听
     */
    private IOnLoadMoreListener onLoadMoreListener;

    public SwipeRefreshWithLoadMoreLayout(Context context) {
        this(context, null);
    }

    public SwipeRefreshWithLoadMoreLayout(Context context, AttributeSet attrs) {
        super(context, attrs);
    }

    @Override
    protected void onLayout(boolean changed, int left, int top, int right, int bottom) {
        super.onLayout(changed, left, top, right, bottom);
        getRecyclerView();
    }

    private void getRecyclerView() {
        int childCount = getChildCount();
        if (childCount > 0) {
            if (getChildAt(0) instanceof RecyclerView) {
                mRecyclerView = (RecyclerView) getChildAt(0);
                mRecyclerView.addOnScrollListener(onScrollListener);
            }
        }
    }

    private int mTmpDy = -1;

    private RecyclerView.OnScrollListener onScrollListener = new RecyclerView.OnScrollListener() {
        @Override
        public void onScrollStateChanged(RecyclerView recyclerView, int newState) {
            super.onScrollStateChanged(recyclerView, newState);
            if (newState == RecyclerView.SCROLL_STATE_IDLE) {
                // 1表示是否能上拉，-1表示是否能下拉
                boolean isBottom = !recyclerView.canScrollVertically(RecyclerView.VERTICAL);
                if (mTmpDy > 0 && isEnableLoadMore && isBottom && onLoadMoreListener != null) {
                    isLoadingMore = true;
                    onLoadMoreListener.onLoadMore();
                    // 限制只执行一次该方法
                    mTmpDy = -1;
                }
            }
        }

        @Override
        public void onScrolled(RecyclerView recyclerView, int dx, int dy) {
            // dy > 0 表示上拉的动作，反之则下拉
            super.onScrolled(recyclerView, dx, dy);
            mTmpDy = dy;
        }
    };

    public interface IOnLoadMoreListener {
        void onLoadMore();
    }

    public SwipeRefreshWithLoadMoreLayout setEnableLoadMore(boolean enable) {
        this.isEnableLoadMore = enable;
        return this;
    }

    public SwipeRefreshWithLoadMoreLayout setIsLoadingMore(boolean isLoadingMore) {
        this.isLoadingMore = isLoadingMore;
        return this;
    }

    public SwipeRefreshWithLoadMoreLayout setOnLoadMoreListener(IOnLoadMoreListener listener) {
        this.onLoadMoreListener = listener;
        return this;
    }

    public boolean isLoadingMore() {
        return isLoadingMore;
    }

    public SwipeRefreshWithLoadMoreLayout setLoadMoreLayout(RecyclerView.Adapter adapter, int layoutRes) {
        if (isEnableLoadMore && adapter instanceof RecyclerViewAdapterWrapper) {
            RecyclerViewAdapterWrapper adapterWrapper = ((RecyclerViewAdapterWrapper) adapter);
            adapterWrapper.setFooterView(getContext(), layoutRes);
        }
        return this;
    }

    public SwipeRefreshWithLoadMoreLayout setLoadMoreNoMoreDataLayout(RecyclerView.Adapter adapter, int layout) {
        if (isEnableLoadMore && adapter instanceof RecyclerViewAdapterWrapper) {
            RecyclerViewAdapterWrapper adapterWrapper = ((RecyclerViewAdapterWrapper) adapter);
            adapterWrapper.setNoMoreDataView(getContext(), layout);
        }
        return this;
    }
}

```
嗯，很简单是吧，不多说了，没时间解释，有时间优化后再补充。

### 如何使用
1. 按照常规的做法，新建adapter，但是继承的是上面的baseAdapter
2. 因为SwipeRefreshLayout有setRefresh的方法，所以需要上啦加载的话调用setLoadMore方法即可
3. 自定义视图时使用setLoadMoreLayout方法
4. 还有一些方法要用的话看代码吧，也不复杂

