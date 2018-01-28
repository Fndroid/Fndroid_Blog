# Android开发学习之路-RecyclerView的Item自定义动画及DefaultItemAnimator源码分析

这是关于RecyclerView的第二篇，说的是如何自定义Item动画，但是请注意，本文不包含动画的具体实现方法，只是告诉大家如何去自定义动画，如何去参考源代码。

我们知道，RecyclerView默认会使用DefaultItemAnimator，所以如果我们需要自定义动画，那么应该好好的读读这个类的源代码，这样不仅仅是学习怎么自定义，还要学习Android的设计模式。

先弄明白一件事，DefaultItemAnimator继承自SimpleItemAnimator，SimpleItemAnimator继承自RecyclerView.ItemAnimator，所以如果需要自定义动画，最简单的方法是继承SimpleItemAnimator。其次，动画的类型有四种，分别是Add、Remove、Move以及Change，这里我们只列举Remove，举一反三。

我们先看SimpleItemAnimator中的源码，在SimpleItemAnimator中有几个重要的方法：

    
    
     1 @Override
     2     public boolean animateDisappearance(@NonNull ViewHolder viewHolder,
     3             @NonNull ItemHolderInfo preLayoutInfo, @Nullable ItemHolderInfo postLayoutInfo) {
     4         int oldLeft = preLayoutInfo.left;
     5         int oldTop = preLayoutInfo.top;
     6         View disappearingItemView = viewHolder.itemView;
     7         int newLeft = postLayoutInfo == null ? disappearingItemView.getLeft() : postLayoutInfo.left;
     8         int newTop = postLayoutInfo == null ? disappearingItemView.getTop() : postLayoutInfo.top;
     9         if (!viewHolder.isRemoved() && (oldLeft != newLeft || oldTop != newTop)) {
    10             disappearingItemView.layout(newLeft, newTop,
    11                     newLeft + disappearingItemView.getWidth(),
    12                     newTop + disappearingItemView.getHeight());
    13             if (DEBUG) {
    14                 Log.d(TAG, "DISAPPEARING: " + viewHolder + " with view " + disappearingItemView);
    15             }
    16             return animateMove(viewHolder, oldLeft, oldTop, newLeft, newTop);
    17         } else {
    18             if (DEBUG) {
    19                 Log.d(TAG, "REMOVED: " + viewHolder + " with view " + disappearingItemView);
    20             }
    21             return animateRemove(viewHolder);
    22         }
    23     }

解析：这个函数是重写RecyclerView.ItemAnimator的，接口中参数分别是ViewHolder、prelayoutInfo以及postLayoutInfo，第一个参数是指Item的ViewHolder，可以通过这个对象的itemView来获取它的View，第二个参数是指Item删除前的位置信息，第三个是指新的位置信息。再接下来会判断ViewHolder是否已经被移除以及位置是否发生变化，然后在调用animateRemove这个抽象方法，如果我们要自定义动画，就需要去实现它（回调思想）。

    
    
    1 public final void dispatchRemoveStarting(ViewHolder item) {
    2         onRemoveStarting(item);
    3     }
    
    
    1 public void onRemoveStarting(ViewHolder item) {
    2     }

解析：dispatchRemoveStaring个是一个final方法，也就是不能被重写，如果我们需要处理一些在Remove开始的时候的逻辑，我们就需要在animateRemove方法中调用这个方法，这个方法会执行一个onRemoveStaring方法，这个方法就允许我们重写，所以逻辑应该写在onRemoveStaring中，当我们调用dispatchRemoveStaring的时候，onRemoveStaring就会被执行。

这里只说了两个，但是，加上其他动作的就不只是两个啦。。。

**所以，当我们继承了SimpleItemAnimator的时候，需要实现里面的一些方法，一般有如下这些：**

**①
animateRemove（Add、Move和Change）：这些方法会在动画发生的时候回调，一般会在这个方法中用列表记录每个Item的动画以及属性**

**② endAnimation、endAnimations：分别是在一个Item或是多个Item需要立即停止的时候回调**

**③ isRunning：如果需要顺畅滑动的时候，必须要重写这个方法，很多时候比如在网络加载的时候滑动卡顿就是这个方法逻辑不对**

**④
run'PendingAnimations：这是最重要的一个方法。因为animateDisappearence等方法返回的是animateRemove等方法返回的值，而这个方法则是根据这些值来确定是否有准备好的动画需要播放，如果有，就会回调这个方法。在这个方法我们需要处理每一个动作（Remove、Add、Move以及Change）的动画**

**所以，我们的一般步骤就是：**

**①创建一个SimpleItemAnimator的子类**

**②创建每个动作的动作列表**

**③重写animateRemove等方法，当界面中有动作发生，这些函数会被回调，这里进行记录并返回true使得
**run'PendingAnimations开始执行****

**④重写run'PendingAnimations，当③的方法返回true的时候，就认为需要执行动画，我们需要把动画执行的逻辑写在这里面**

**⑤重写isRunning，提供动画播放状态，一般是返回动作列表是否为空**

**⑥如果有需要，重写endAnimation、endAnimations、onRemoveFinish等方法**

具体的步骤有了，但是我们还不清楚该怎么构建它，不用着急，为了方便我们，谷歌其实已经提供了DefaultItemAnimator，我们可以参考一些它的源码，没有人讲的比源码有道理，我们需要的是有足够的耐心！

DefaultItemAnimator中定义了一些ArrayList来存放动作的信息，如下：

    
    
    private ArrayList<ViewHolder> mPendingRemovals = new ArrayList<>();
    private ArrayList<ViewHolder> mRemoveAnimations = new ArrayList<>();
    
    
    1 @Override
    2     public boolean animateRemove(final ViewHolder holder) {
    3         resetAnimation(holder);
    4         mPendingRemovals.add(holder);
    5         return true;
    6     }

解析：可以看到animatorRemove方法直接是把viewholder加入列表中，然后返回true

    
    
     1 @Override
     2     public void runPendingAnimations() {
     3         boolean removalsPending = !mPendingRemovals.isEmpty();
     4         boolean movesPending = !mPendingMoves.isEmpty();
     5         boolean changesPending = !mPendingChanges.isEmpty();
     6         boolean additionsPending = !mPendingAdditions.isEmpty();
     7         if (!removalsPending && !movesPending && !additionsPending && !changesPending) {
     8             // nothing to animate
     9             return;
    10         }
    11         // First, remove stuff
    12         for (ViewHolder holder : mPendingRemovals) {
    13             animateRemoveImpl(holder);
    14         }
    15         mPendingRemovals.clear();
    16         // Next, move stuff
    17         ......
    18         // Next, change stuff, to run in parallel with move animations
    19         ......
    20         // Next, add stuff
    21         ......
    22     }            

解析：根据上面可以知道，runPendingAnimations会执行，可看到，在这个方法中遍历了动作列表，并让每个Item都执行了animatorRemoveImpl方法，其他动作的方法暂时先省略，有兴趣的可以自行阅读。

    
    
     1 private void animateRemoveImpl(final ViewHolder holder) {
     2         final View view = holder.itemView;
     3         final ViewPropertyAnimatorCompat animation = ViewCompat.animate(view);
     4         mRemoveAnimations.add(holder);
     5         animation.setDuration(getRemoveDuration())
     6                 .alpha(0).setListener(new VpaListenerAdapter() {
     7             @Override
     8             public void onAnimationStart(View view) {
     9                 dispatchRemoveStarting(holder);
    10             }
    11 
    12             @Override
    13             public void onAnimationEnd(View view) {
    14                 animation.setListener(null);
    15                 ViewCompat.setAlpha(view, 1);
    16                 dispatchRemoveFinished(holder);
    17                 mRemoveAnimations.remove(holder);
    18                 dispatchFinishedWhenDone();
    19             }
    20         }).start();
    21     }

解析：可以看到animatorRemoveImpl方法中实现了整个动画的具体逻辑，具体怎么做不在本文范围中，在我们执行了动画之后，也就是在动画的Listener中的onAnimatorEnd中调用了dispatchRemoveFinish，还记得这个方法吗，它会执行onRemoveFinish方法，onRemoveFinish方法是可以供给我们重写的。然后把item移除动作列表。

    
    
     1 @Override
     2     public boolean isRunning() {
     3         return (!mPendingAdditions.isEmpty() ||
     4                 !mPendingChanges.isEmpty() ||
     5                 !mPendingMoves.isEmpty() ||
     6                 !mPendingRemovals.isEmpty() ||
     7                 !mMoveAnimations.isEmpty() ||
     8                 !mRemoveAnimations.isEmpty() ||
     9                 !mAddAnimations.isEmpty() ||
    10                 !mChangeAnimations.isEmpty() ||
    11                 !mMovesList.isEmpty() ||
    12                 !mAdditionsList.isEmpty() ||
    13                 !mChangesList.isEmpty());
    14     }

解析：isRunning方法其实就是根据动作列表是否为空来返回结果

还有其他一些函数可以自己阅读源代码。



