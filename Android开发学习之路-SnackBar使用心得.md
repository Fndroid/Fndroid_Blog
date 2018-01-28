# Android开发学习之路-SnackBar使用心得

SnackBar是DesignSupportLibrary中的一个重要的控件，用于在界面下面提示一些关键信息，跟Toast不同的地方是SnackBar允许用户向右滑动消除它，同时，也允许在SnackBar中设定一个Action，当用户点击了SnackBar里面的按钮的时候，可以进行一些操作，所以，功能绝对是很强大的。

SnackBar的构造：

    
    
    // 参数分别是父容器，提示信息，持续时间  
    public static Snackbar make(@NonNull View view, @NonNull CharSequence text,@Duration int duration)

SnackBar的常用方法：

    
    
    // 用于给SnackBar设定一个Action，点击之后会回调OnclickListener中的Onclick方法  
    public Snackbar setAction(CharSequence text, final View.OnClickListener listener)
    
    
    // 用于设定Action的字体颜色  
    public Snackbar setActionTextColor(@ColorInt int color)
    
    
    // 设定提示的字体  
    public Snackbar setText(@NonNull CharSequence message)
    
    
    // 展示SnackBar  
    public void show()
    
    
    // 清除SnackBar  
    public void dismiss()
    
    
    // 设置回调，比如OnDismissed或者OnShown  
    public Snackbar setCallback(Callback callback)

简单是使用就不说了，这里直接说一下设置Action和设置Callback

要求：实现一个撤销的操作，用户点击列表中每一项的删除按钮之后，删除（数据库中）对应的行，然后弹出SnackBar提示是否撤销，如果选择是，则恢复删除的行，否则删除该行，效果如下

![](http://images2015.cnblogs.com/blog/852227/201603/852227-20160301194841611-1523472700.gif)

思路：

①先对我们要删除的这个行进行备份，然后在点击删除按钮的时候把行删除，把数据从源删除，然后刷新

②弹出SnackBar，设定Action，如果点了Yes，就把数据插入到源中，刷新

③设定CallBack，监听SnackBar的消失事件，如果不是点击Action而消失，则将数据从SQLite中删除

注意：这里为什么要设定监听？因为如果不进行监听，需要在删除的时候访问数据库，撤销是时候进行数据库的插入操作，这样列表中的条目位置会发生改变

我们可以在Adapter中对删除按钮进行监听，代码如下：

    
    
     1 delete.setOnClickListener(new View.OnClickListener() {
     2             @Override
     3             public void onClick(View v) {
     4                 backupMap = mdata.get(position);
     5                 mdata.remove(position);
     6                 notifyDataSetChanged();
     7                 Snackbar snackBar = Snackbar.make(root, "是否撤销删除？", Snackbar.LENGTH_LONG);
     8                 snackBar.setAction("YES", new MyOnClickListener(position));
     9                 snackBar.setCallback(new MyCallback());
    10                 snackBar.show();
    11             }
    12         });

如果点击了删除按钮，我们对数据进行备份，然后删除数据源mdata对应的这个map，接下来刷新界面。然后第7行构造了一个SnackBar，询问是否撤销删除，并把持续时间设置为LONG以免用户未阅读完文字SnackBar就消失了。第8行设定了点击的Action，提示信息为"YES"，并提供了点击的监听。第9行设定了Callback，判断SnackBar的消失状态。第10行就直接把SnackBar展示出来。

下面来看Action的点击事件：

    
    
     1 private class MyOnClickListener implements View.OnClickListener {
     2         private final int position;
     3 
     4         public MyOnClickListener(int position) {
     5             this.position = position;
     6         }
     7 
     8         @Override
     9         public void onClick(View v) {
    10             mdata.add(position, backupMap);
    11             notifyDataSetChanged();
    12         }
    13     }

如果点击了Action，就用备份的数据进行恢复

下面看Callback：

    
    
     1 private class MyCallback extends Snackbar.Callback {
     2         @Override
     3         public void onDismissed(Snackbar snackbar, int event) {
     4             super.onDismissed(snackbar, event);
     5             if (event == DISMISS_EVENT_SWIPE || event == DISMISS_EVENT_TIMEOUT || event ==
     6                     DISMISS_EVENT_CONSECUTIVE) {
     7                 mdatabase.execSQL("delete from tally where id=?", new String[]{backupMap.get
     8                         ("id").toString()});
     9             }
    10         }
    11     }

这里看到我们重写了onDismissed方法，在SnackBar消失的时候会回调这个方法，我们先判断这个消失的类型，如果是点击了Action，就不用删除数据库中的数据，否则就对数据库进行删除。

SnackBar消失的类型对应的常量：

    
    
    /** Indicates that the Snackbar was dismissed via a swipe.*/
    public static final int DISMISS_EVENT_SWIPE = 0;
    /** Indicates that the Snackbar was dismissed via an action click.*/
    public static final int DISMISS_EVENT_ACTION = 1;
    /** Indicates that the Snackbar was dismissed via a timeout.*/
    public static final int DISMISS_EVENT_TIMEOUT = 2;
    /** Indicates that the Snackbar was dismissed via a call to {@link #dismiss()}.*/
    public static final int DISMISS_EVENT_MANUAL = 3;
    /** Indicates that the Snackbar was dismissed from a new Snackbar being shown.*/
    public static final int DISMISS_EVENT_CONSECUTIVE = 4;

可以看到，从0到4分别是滑动清除、点击Action、持续时间结束、调用dismiss方法以及有新的SnackBar产生

因为每次SnackBar消失的时候都是调用了dismiss方法，所以我们只需要判断此时的状态是否为滑动清除、持续时间结束或者新的SnackBar产生，如果是，则证明用户不需要撤销，则对数据库进行删除。

最后，如果使用SnackBar，记得添加依赖！！

