# Android开发学习之路-RecyclerView滑动删除和拖动排序

[Android开发学习之路-
RecyclerView使用初探](http://www.cnblogs.com/Fndroid/p/5245250.html)

[Android开发学习之路-
RecyclerView的Item自定义动画及DefaultItemAnimator源码分析](http://www.cnblogs.com/Fndroid/p/5252001.html)

[Android开发学习之路-下拉刷新怎么做？](http://www.cnblogs.com/Fndroid/p/5275862.html)

本篇是接着上面三篇之后的一个对RecyclerView的介绍，这里多说两句，如果你还在使用ListView的话，可以放弃掉ListView了。RecyclerView自动帮我们缓存Item视图（ViewHolder），允许我们自定义各种动作的动画和分割线，允许我们对Item进行一些手势操作。另外，因为Design库的推出大大方便我们编写带有Material风格的App，而ListView是不兼容这个库的，比如滑动的相互协调，只有RecyclerView能做到。

* * *

先看本篇内容的效果图：

![](http://images2015.cnblogs.com/blog/852227/201607/852227-20160710111314655-1686855606.gif)

效果内容主要有三部分：

  * **长按点击其中一个Item之后可以将其拖动到其他地方**
  * **向左右滑动可以删除某个Item**
  * **长按的时候会有一个浮起的动作，放下之后会重新重新对齐**

* * *



**①先易后难，卡片浮起效果  **

在Material Design中，物件的呈现是以3d的模式来进行的，也就是在原来的基础上增加了一个Z轴来表示物体的高度。

![](http://images2015.cnblogs.com/blog/852227/201607/852227-20160710132328077-381472148.png)

当我们点击一个卡片的时候，应该给予用户一些反馈，让用户知道自己在操作这个卡片，也就是触摸反馈。触摸效果图的触摸反馈是先出现水波纹，接着当卡片可以移动的时候先浮动，再开始移动，最后下落，效果如下：（浮起的动画可能要仔细看）

![](http://images2015.cnblogs.com/blog/852227/201607/852227-20160710133347999-930299373.gif)

实现：

水波纹效果使用系统提供的，因为Demo中使用的是一个CardView来呈现内容，所以只需要给CardView加上两个属性即可：

    
    
    android:clickable="true"
    android:foreground="?android:attr/selectableItemBackground"



浮起和下沉的动画也不难，改变View的translationZ属性即可：

    
    
    private void pickUpAnimation(View view) {
        ObjectAnimator animator = ObjectAnimator.ofFloat(view, "translationZ", 1f, 10f);
        animator.setInterpolator(new DecelerateInterpolator());
        animator.setDuration(300);
        animator.start();
    }





**②滑动（Swipe）和移动（Move）Item**

Swipe是指我们上面的左右滑动删除，Move则是我们对Item的移动，改变列表的排序。要实现这两个功能，需要用到一个类ItemTouchHelper，当我们构造好这个类之后，可以调用它的attachToRecyclerView方法将其绑定在某个RecyclerView中，当RecyclerView出现某些操作（滑动和移动）时，构造这个类的时候传入的回调类会回调相应的方法，我们在这些方法中对数据集进行操作即可。

    
    
    new ItemTouchHelper(new ItemTouchHelper.Callback() {  
        //省略代码
    }).attachToRecyclerView(mRecyclerView);



接着就是重写接口Callback的方法了，这里需要重写的有几个，分别是：

  * isItemViewSwipeEnable : Item是否可以滑动
  * isLongPressDragEnable ：Item是否可以长按 
  * getMovementFlags ： 获取移动标志
  * onMove ： 当一个Item被另外的Item替代时回调，也就是数据集的内容顺序改变
  * onMoved ： 当onMove返回true的时候回调
  * onSwiped ： 当某个Item被滑动离开屏幕之后回调
  * setSelectedChange ： 某个Item被长按选中会被回调，当某个被长按移动的Item被释放时也调用

    
    
    new ItemTouchHelper(new ItemTouchHelper.Callback() {
        private RecyclerView.ViewHolder vh;
    
        @Override
        public boolean isItemViewSwipeEnabled() {
            return true;
        }
    
        @Override
        public boolean isLongPressDragEnabled() {
            return true;
        }
    
        @Override
        public int getMovementFlags(RecyclerView recyclerView, RecyclerView.ViewHolder
                viewHolder) {
            // 拖拽的标记，这里允许上下左右四个方向
            int dragFlags = ItemTouchHelper.UP | ItemTouchHelper.DOWN | ItemTouchHelper.LEFT |
                    ItemTouchHelper.RIGHT;
            // 滑动的标记，这里允许左右滑动
            int swipeFlags = ItemTouchHelper.START | ItemTouchHelper.END;
            return makeMovementFlags(dragFlags, swipeFlags);
        }
    
        /*
            这个方法会在某个Item被拖动和移动的时候回调，这里我们用来播放动画，当viewHolder不为空时为选中状态
            否则为释放状态
         */
        @Override
        public void onSelectedChanged(RecyclerView.ViewHolder viewHolder, int actionState) {
            super.onSelectedChanged(viewHolder, actionState);
            if (viewHolder != null) {
                vh = viewHolder;
                pickUpAnimation(viewHolder.itemView);
            } else {
                if (vh != null) {
                    putDownAnimation(vh.itemView);
                }
            }
        }
    
    
        @Override
        public boolean onMove(RecyclerView recyclerView, RecyclerView.ViewHolder viewHolder,
                              RecyclerView.ViewHolder target) {
            // 移动时更改列表中对应的位置并返回true
            Collections.swap(newsList, viewHolder.getAdapterPosition(), target
                    .getAdapterPosition());
            return true;
        }
    
        /*
            当onMove返回true时调用
         */
        @Override
        public void onMoved(RecyclerView recyclerView, RecyclerView.ViewHolder viewHolder, int
                fromPos, RecyclerView.ViewHolder target, int toPos, int x, int y) {
            super.onMoved(recyclerView, viewHolder, fromPos, target, toPos, x, y);
            // 移动完成后刷新列表
            mAdapter.notifyItemMoved(viewHolder.getAdapterPosition(), target
                    .getAdapterPosition());
        }
    
        @Override
        public void onSwiped(RecyclerView.ViewHolder viewHolder, int direction) {
            // 将数据集中的数据移除
            newsList.remove(viewHolder.getAdapterPosition());
            // 刷新列表
            mAdapter.notifyItemRemoved(viewHolder.getAdapterPosition());
        }
    }).attachToRecyclerView(mRecyclerView);



这里直接贴出代码，并做了注释，应该比较简单了。



如果文章内容有误，请大家指出，感谢。

