# Android开发学习之路-下拉刷新怎么做？

因为最近的开发涉及到了网络读取数据，那么自然少不了的就是下拉刷新的功能，搜索的方法一般是自己去自定义ListView或者RecyclerView来重写OnTouch或者OnScroll方法来实现手势的监听然后播放动画最后刷新界面

今天说的是一个Google官方提供的下拉刷新布局，名字叫做SwipeRefreshLayout，找到这个布局的时候真的是喜出望外啊，下面来记录一下它怎么用。

这里放一下效果图先，就是下面这个小圈圈啦

![](http://images2015.cnblogs.com/blog/852227/201603/852227-20160314150516631-118881221.gif)

首先是需要把这个布局套在我们需要刷新的控件之外，这里是RecyclerView

    
    
    <android.support.v4.widget.SwipeRefreshLayout
            android:id="@+id/reglost_srl"
            android:layout_width="match_parent"
            android:layout_height="match_parent">
    
            <android.support.v7.widget.RecyclerView
                android:id="@+id/reglost_rv"
                android:layout_width="match_parent"
                android:layout_height="match_parent" />
    
        </android.support.v4.widget.SwipeRefreshLayout>

当我们需要显示或者隐藏刷新这个小动画的时候，需要调用下面这个方法

    
    
    public void setRefreshing(boolean refreshing)

但是，如果是刚开始的时候我们要显示这个加载的动画，不能直接把true传入这个方法然后调用，因为我们在源代码中可以看到SwipeRefreshLayout中的一个名为mOriginalOffsetTop的变量没有被初始化，所以动画是可能显示不出来的，那么应该怎样来显示呢？用下面的方法

    
    
    refreshlayout.post(new Runnable() {
                @Override
                public void run() {
                    refreshlayout.setRefreshing(true);
                }
            });

**更新：如果使用24.2.0或者更新的Support Library，那么可以直接调用setRefreshing(true)即可。**

然后，我们还需要给动画结束后添加一个监听

    
    
    refreshlayout.setOnRefreshListener(this);
    
    
    @Override
        public void onRefresh() {
            refreshData(); // refresh the view
        }

当数据加载完成了，把动画隐藏，很简单

    
    
    refreshlayout.setRefreshing(false);

到这里，其实已经基本是全部的用法了，这个东西真的是方便，但是，不是就没有一点问题了，稍不注意，其实还是会遇到一些问题的。

在网上的很多代码中，不少的开发者都喜欢在从网络加载数据前把数据集合清空，也就是在onRefresh方法中先调用data.clear()，但是这里会出现一个问题，如果正在加载的时候我们往上滑动界面，App就会直接Crash掉，看图

![](http://images2015.cnblogs.com/blog/852227/201603/852227-20160314151719099-414844062.gif)

这个问题其实也很简单因为如果我们把数据清空了，但是列表没有刷新，如果滑动产生的话就会去访问data中的数据，直接就越界了。

所以正确的做法应该是先执行联网加载数据，当数据加载完了就清空集合，重新填入并刷新。



