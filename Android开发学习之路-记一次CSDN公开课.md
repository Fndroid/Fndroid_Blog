# Android开发学习之路-记一次CSDN公开课

今天的CSDN公开课[Android事件处理重难点快速掌握](http://edu.csdn.net/huiyiCourse/detail/170)中老师讲到一个概念我觉得不正确。

原话是这样的： **点击事件可以通过事件监听和回调两种方法实现** 。

我一听到之后我的表情是这样的：

![](http://images2015.cnblogs.com/blog/852227/201607/852227-20160720211024732-2073452208.jpg)

这跟我学的看的都不一样啊，这还分监听和回调。这个时候我立马提出问题，嗯，讲课老师看到了。

老师就说了：是不一样的，我们第四点（最后一点）会讲。

好，我立马坐好准备受教听了大半小时。听到最后我的表情是这样的：

![](http://images2015.cnblogs.com/blog/852227/201607/852227-20160720211417044-903576331.jpg)

废话说够了，开个小玩笑，不要见怪。

* * *

我们都知道，监听事件其实只有一种，就是回调。如果有人不明白什么是回调？可以看看我的一篇文章中的开头那个例子：[回调学习笔记](http://www.cnblogs.com/Fndroid/p/5308713.html)

我们以Button为例，Button是View的子类这个毋庸置疑，所以我们直接看View中的源码：

    
    
        public void setOnClickListener(@Nullable OnClickListener l) {
            if (!isClickable()) {
                setClickable(true);
            }
            getListenerInfo().mOnClickListener = l;
        }

这里很简单，只是把一个OnClickListener的具体子类保存起来而已。当我们点击按钮的时候，系统必然会回调onTouchEvent这个方法，而在View中这个方法是这样的：

    
    
     1     public boolean onTouchEvent(MotionEvent event) {
     2         final float x = event.getX();
     3         final float y = event.getY();
     4         final int viewFlags = mViewFlags;
     5         final int action = event.getAction();
     6 
     7        // 代码省略
     8 
     9         if (((viewFlags & CLICKABLE) == CLICKABLE ||
    10                 (viewFlags & LONG_CLICKABLE) == LONG_CLICKABLE) ||
    11                 (viewFlags & CONTEXT_CLICKABLE) == CONTEXT_CLICKABLE) {
    12             switch (action) {
    13                 case MotionEvent.ACTION_UP:
    14                     boolean prepressed = (mPrivateFlags & PFLAG_PREPRESSED) != 0;
    15                     if ((mPrivateFlags & PFLAG_PRESSED) != 0 || prepressed) {
    16                         boolean focusTaken = false;
    17                         if (isFocusable() && isFocusableInTouchMode() && !isFocused()) {
    18                             focusTaken = requestFocus();
    19                         }
    20 
    21                         if (prepressed) {
    22                             setPressed(true, x, y);
    23                        }
    24 
    25                         if (!mHasPerformedLongPress && !mIgnoreNextUpEvent) {
    26                             if (!focusTaken) {
    27 
    28                                 if (mPerformClick == null) {
    29                                     mPerformClick = new PerformClick();
    30                                 }
    31                                 if (!post(mPerformClick)) {
    32                                     performClick();
    33                                 }
    34                             }
    35                         }
    36 
    37                         if (mUnsetPressedState == null) {
    38                             mUnsetPressedState = new UnsetPressedState();
    39                         }
    40 
    41                         if (prepressed) {
    42                             postDelayed(mUnsetPressedState,
    43                                     ViewConfiguration.getPressedStateDuration());
    44                         } else if (!post(mUnsetPressedState)) {
    45                             mUnsetPressedState.run();
    46                         }
    47 
    48                         removeTapCallback();
    49                     }
    50                     mIgnoreNextUpEvent = false;
    51                     break;
    52 
    53                 case MotionEvent.ACTION_DOWN:
    54                    // 代码省略
    55                     break;
    56 
    57                 case MotionEvent.ACTION_CANCEL:
    58                     // 代码省略
    59                     break;
    60 
    61                 case MotionEvent.ACTION_MOVE:
    62                    // 代码省略
    63                     break;
    64             }
    65 
    66             return true;
    67         }
    68 
    69         return false;
    70     }

这里我删除了很多代码，我们知道一个点击是由一个ACTION_DOWN、若干个ACTION_MOVE和一个ACTION_UP组成的，也就是说，当点击完成，会执行ACTION_UP中的代码。在第32行中，调用了一个方法叫做performClick，这个方法就是直接执行点击事件的，我们看到这个方法的内部：

    
    
     1     public boolean performClick() {
     2         final boolean result;
     3         final ListenerInfo li = mListenerInfo;
     4         if (li != null && li.mOnClickListener != null) {
     5             playSoundEffect(SoundEffectConstants.CLICK);
     6             li.mOnClickListener.onClick(this);
     7             result = true;
     8         } else {
     9             result = false;
    10         }
    11 
    12         sendAccessibilityEvent(AccessibilityEvent.TYPE_VIEW_CLICKED);
    13         return result;
    14     }

第6行中，调用了我们传进来的那个接口的实现类中的onClick方法，这就是回调，因为这不是真正的调用。

* * *

总结：无论是什么监听事件，都是通过回调来实现的。我们点击之后，事件由系统捕获，有底层控件逐步上传到控件，然后回调给View的onTouchEvent方法，这个方法回调给我们设置的接口中onClick方法。我固然知道回调和监听不同，但是它们绝对不会是两种方法。



我看到有人说李刚老师的《疯狂Android讲义》中确实是这么定义的，我这里补充说一下吧。书本认为监听和回调时两种事件处理方法本就有误。如果有去看过[官方文档](https://developer.android.com/guide/topics/ui/ui-
events.html)，那么其实在文档中就有说到，内容如下：

![](http://images2015.cnblogs.com/blog/852227/201607/852227-20160722122122826-214768099.png)

 翻译：

在你用来构建布局的各个View中，你可能已经注意到几个共有的回调方法看上去对UI事件非常有用。这些方法会在对象发生相应动作的时候由Android框架调用。例如，当一个View（例如Button）被触摸，这个View的onTouchEvent（）方法会被执行。然而，为了截获这些事件，你必须要继承这个类并重写这些方法。为了处理这些事件而继承所有的View对象是不实际的。
**这就是为什么View类中包含一系列嵌套了一些带有让你更容易定义的回调的接口。这些接口，被称为事件监听器** ，是你在你的UI中捕获用户行为的凭证。

这里说明了事件监听器其实就是由回调方法组成的接口。也就会监听器是由回调实现的。为什么要这样呢？理由很简单，因为我们处理的操作一般是在活动中而不是在View中！



那么究竟上面说的回调方法有哪些？在文档的下部分中，列举了几个常用的回调：

![](http://images2015.cnblogs.com/blog/852227/201607/852227-20160722123340263-1525578070.png)



* * *

哈哈，最后一次更新下。有人觉得李刚老师那么厉害，不会错的。我就说说我的看法吧，如果你认真去看《疯狂Android讲义》，你会发现，这本书的大部分内容就是翻译和API介绍，说是Android开发的权威，我觉得甚至都称不上。楼下有人也说到了，只要我去群里问一问，大家就能发现我是搞笑的，但实际上我也尝试了，在知乎、微博或者QQ群中提问了，得到的答案多是：都是回调，不明白他为什么这样写。我觉得吧，总是会有人硬是觉得它没问题的，比较概念没理解好，你也很难跟他说明。

