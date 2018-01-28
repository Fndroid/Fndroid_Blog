# Android开发学习之路-PopupWindow和仿QQ左滑删除

这周作业，要做一个类似QQ的左滑删除效果的ListView，因为不想给每个item都放一个按钮，所以决定用PopupWindow，这里记录一下

先放一下效果图：

![](http://images2015.cnblogs.com/blog/852227/201604/852227-20160409200921562-1932888444.gif)

先说明一下这里面的问题：

①没有做到像QQ那样可以允许item跟随手指移动，虽然PopupWindow有update方法让我们动态移动，但是在屏幕外移动会没有动画效果，直接弹进来

②仔细观察可以发现，item的滑动和删除按钮的滑动是分开的，无法保证它们会一起播放，QQ的动画可以

**再说说大概的思路，因为我们没有让item都带上Button，所以用到了PopupWindow来做删除按钮，我们可以通过自定义ListView来监听手势，如果判断了是左滑动，则让PopupWindow动态显示出来，在让item做一个平移的效果，当两者的持续时间一致的时候，效果就出来了。接下来，如果用户点击了PopupWindow以外的区域，PopupWindow会消失，回调dismiss方法，我们再把item移回原位。**



介绍一下PopupWindow，它允许我们在屏幕上的任意位置弹出一个提示框，提示框的布局我们可以自己定义，我们上面的删除按钮，其实就是一个PopupWindow。

如果我们要使用PopupWindow，可以像下面这样，定义一个自定义布局的PopupWindow

    
    
    View view = LayoutInflater.from(context).inflate(R.layout.item_delete, null);
    PopupWindow mPopupWindow = new PopupWindow(view, WindowManager.LayoutParams.WRAP_CONTENT, WindowManager.LayoutParams.WRAP_CONTENT, true);

如果我们要显示这个PopupWindow，可以调用showAtLocation或者showAsDropDown，前者是直接把PopupWindow显示在某个特定位置，后者是在某个位置从上向下弹出，这里我们调用第一个方法，有兴趣的可以尝试下第二个，参数什么的可以不用管，不懂的看看API就知道了，之后再调用update方法更新位置信息

    
    
    mPopupWindow.showAtLocation(itemView, Gravity.LEFT | Gravity.TOP, locations[0]+ itemView.getWidth() - popWidth, locations[1]);  
    mPopupWindow.update();

至于对PopupWindow设置背景什么的，需要的时候看看API就知道了！这里不详说。



至于效果的实现，我们一步一步的来。

①自定义一个ListView，详细的做法，已经在下面做了注释

    
    
    public class SlidableListView extends ListView {
        private static final String TAG = "SlidableListView";
        private int touchSlop;
    
        private View itemView; // 每个Item的View
        private int mCurrentDownPosition; // 当前点击的位置，用来找出点击的Item
        private PopupWindow mPopupWindow; // 删除按钮
        private int popHeight, popWidth; // 删除按钮的高和宽
        private boolean isSliding = false; // 手指是否在向左滑动
        private int downX, downY, moveX, moveY; // 点击的X和Y，以及移动的X和Y
        private OnItemDeleteListener onItemDeleteListener; // 点击删除按钮时候的回调接口
    
        public SlidableListView(Context context) {
            super(context);
        }
    
        // 在布局中使用自定义控件调用的构造函数，这里进行一些必要的初始化
        public SlidableListView(Context context, AttributeSet attrs) {
            super(context, attrs);
            // 初始化删除按钮，记得让按钮可以接收焦点
            View view = LayoutInflater.from(context).inflate(R.layout.item_delete, null);
            mPopupWindow = new PopupWindow(view, WindowManager.LayoutParams.WRAP_CONTENT,
                    WindowManager.LayoutParams.WRAP_CONTENT, true);
            // 获取删除按钮的宽和高
            view.measure(0, 0);
            popWidth = view.getMeasuredWidth();
            popHeight = view.getMeasuredHeight();
            // 获取滑动的最小距离
            touchSlop = ViewConfiguration.get(context).getScaledTouchSlop();
        }
    
        public SlidableListView(Context context, AttributeSet attrs, int defStyleAttr) {
            super(context, attrs, defStyleAttr);
        }
    
        // 先拦截touch事件，根据不同情况进行分发
        @Override
        public boolean dispatchTouchEvent(MotionEvent ev) {
            int action = ev.getAction();
            int x = (int) ev.getX(), y = (int) ev.getY();
            switch (action) {
                case MotionEvent.ACTION_DOWN:
                    downX = x;
                    downY = y;
                    // 如果删除按钮正在显示，那么不处理这个触摸事件，并把按钮隐藏起来
                    if (mPopupWindow.isShowing()) {
                        mPopupWindow.dismiss();
                        return false; // 阻止事件传递
                    }
                    mCurrentDownPosition = pointToPosition(downX, downY);
                    // 获取触摸的item
                    itemView = getChildAt(mCurrentDownPosition - getFirstVisiblePosition());
                    break;
                case MotionEvent.ACTION_MOVE:
                    moveX = x;
                    moveY = y;
                    int deltaX = moveX - downX;
                    int deltaY = moveY - downY;
                    // 当判断为左滑的时候，把事件交给OnTouchEvent来处理
                    if (moveX < downX && Math.abs(deltaX) > touchSlop && Math.abs(deltaY) <
                            touchSlop) {
                        isSliding = true;
                    }
                    break;
            }
            return super.dispatchTouchEvent(ev);
        }
    
        @Override
        public boolean onTouchEvent(MotionEvent ev) {
            int action = ev.getAction();
            // 判断是否已经有左右滑动
            if (isSliding) {
                switch (action) {
                    case MotionEvent.ACTION_UP:
                        isSliding = false;
                        break;
                    case MotionEvent.ACTION_MOVE:
                        // 获取item在屏幕中的位置信息
                        int[] locations = new int[2];
                        itemView.getLocationOnScreen(locations);
                        // 给删除按钮设置动画，这里自定了出现从右到左平移，隐藏从左到右
                        mPopupWindow.setAnimationStyle(R.style.PopupWindowAnimations);
                        // 设置删除按钮的位置，这里设置在item的最右端
                        mPopupWindow.showAtLocation(itemView, Gravity.LEFT | Gravity.TOP, locations[0]
                                + itemView.getWidth() - popWidth, locations[1]);
                        mPopupWindow.update();
                        // 给item播放平移动画，配合删除按钮
                        ObjectAnimator.ofFloat(itemView, "translationX", 0, -popWidth).setDuration
                                (200).start();
                        // 获取删除按钮的实例，这里是一个TextView
                        TextView delete = (TextView) mPopupWindow.getContentView().findViewById(R.id
                                .delete);
                        // 设置监听，如果删除按钮消失（点击按钮外的地方，按下back键）回调
                        mPopupWindow.setOnDismissListener(new PopupWindow.OnDismissListener() {
                            @Override
                            public void onDismiss() {
                                // 把item移回原位置
                                ObjectAnimator.ofFloat(itemView, "translationX", -popWidth, 0)
                                        .setDuration(200).start();
                            }
                        });
                        // 给删除按钮设置点击事件
                        delete.setOnClickListener(new OnClickListener() {
                            @Override
                            public void onClick(View v) {
                                // 获取点击的item位置，并调用回调接口方法发送出去，隐藏删除按钮
                                int position = SlidableListView.this.getPositionForView(itemView);
                                onItemDeleteListener.onItemDelete(position);
                                mPopupWindow.dismiss();
                            }
                        });
                        break;
                }
                // 如果有在滑动，消化事件
                return true;
            }
            return super.onTouchEvent(ev);
        }
    
        public void setOnItemDeleteListener(OnItemDeleteListener onItemDeleteListener) {
            this.onItemDeleteListener = onItemDeleteListener;
        }
    
        // 回调删除事件的接口
        public interface OnItemDeleteListener {
            void onItemDelete(int position);
        }
    }

②自定义消失和进入的动画，因为系统中没有我们想要的平移进入

style.xml:

    
    
    <style name="PopupWindowAnimations" parent="@android:style/Animation">
        <item name="android:windowEnterAnimation">@anim/delete_enter</item>
        <item name="android:windowExitAnimation">@anim/delete_exit</item>
    </style>

anim/delete_enter.xml:

    
    
    <?xml version="1.0" encoding="utf-8"?>
    <set xmlns:android="http://schemas.android.com/apk/res/android">
        <translate
            android:interpolator="@android:anim/accelerate_decelerate_interpolator"
            android:fromXDelta="200"
            android:toXDelta="0"
            android:fromYDelta="0"
            android:toYDelta="0"
            android:duration="200"/>
    </set>

anim/delete_exit.xml:

    
    
    <?xml version="1.0" encoding="utf-8"?>
    <set xmlns:android="http://schemas.android.com/apk/res/android">
        <translate
            android:fromXDelta="0"
            android:toXDelta="200"
            android:fromYDelta="0"
            android:toYDelta="0"
            android:duration="200"/>
    </set>

③自定义Adapter，这里不给出了，都懂

④在布局中使用自定义的ListView

    
    
    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
                  android:orientation="vertical"
                  android:layout_width="match_parent"
                  android:layout_height="match_parent">
    
        <sixthweek.fndroid.com.sixthweek.SlidableListView
            android:id="@+id/slidableListView"
            android:layout_width="match_parent"
            android:layout_height="match_parent" />
    
    </LinearLayout>

⑤在Activity中使用adapter传入数据即可

    
    
    public class SlidableListViewActivity extends AppCompatActivity {
        private SlidableListView slidableListView;
        private MyAdapter adapter;
        private List<Map<String, String>> data;
    
    
        @Override
        protected void onCreate(@Nullable Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_slidablelistview);
            slidableListView = (SlidableListView) findViewById(R.id.slidableListView);
            data = new ArrayList<>();
            for (int i = 0; i < 20; i++) {
                Map<String, String> map = new HashMap();
                map.put("name", "QQ" + i);
                data.add(map);
            }
            adapter = new MyAdapter(this, data);
            slidableListView.setAdapter(adapter);
            // 实现回调接口，处理逻辑
            slidableListView.setOnItemDeleteListener(new SlidableListView.OnItemDeleteListener() {
                @Override
                public void onItemDelete(int position) {
                    data.remove(position);
                    adapter.notifyDataSetChanged();
                }
            });
        }
    }



