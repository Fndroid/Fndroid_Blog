# Android����ѧϰ֮·-PopupWindow�ͷ�QQ��ɾ��

������ҵ��Ҫ��һ������QQ����ɾ��Ч����ListView����Ϊ�����ÿ��item����һ����ť�����Ծ�����PopupWindow�������¼һ��

�ȷ�һ��Ч��ͼ��

![](http://images2015.cnblogs.com/blog/852227/201604/852227-20160409200921562-1932888444.gif)

��˵��һ������������⣺

��û��������QQ������������item������ָ�ƶ�����ȻPopupWindow��update���������Ƕ�̬�ƶ�����������Ļ���ƶ���û�ж���Ч����ֱ�ӵ�����

����ϸ�۲���Է��֣�item�Ļ�����ɾ����ť�Ļ����Ƿֿ��ģ��޷���֤���ǻ�һ�𲥷ţ�QQ�Ķ�������

**��˵˵��ŵ�˼·����Ϊ����û����item������Button�������õ���PopupWindow����ɾ����ť�����ǿ���ͨ���Զ���ListView���������ƣ�����ж������󻬶�������PopupWindow��̬��ʾ����������item��һ��ƽ�Ƶ�Ч���������ߵĳ���ʱ��һ�µ�ʱ��Ч���ͳ����ˡ�������������û������PopupWindow���������PopupWindow����ʧ���ص�dismiss�����������ٰ�item�ƻ�ԭλ��**



����һ��PopupWindow����������������Ļ�ϵ�����λ�õ���һ����ʾ����ʾ��Ĳ������ǿ����Լ����壬���������ɾ����ť����ʵ����һ��PopupWindow��

�������Ҫʹ��PopupWindow����������������������һ���Զ��岼�ֵ�PopupWindow

    
    
    View view = LayoutInflater.from(context).inflate(R.layout.item_delete, null);
    PopupWindow mPopupWindow = new PopupWindow(view, WindowManager.LayoutParams.WRAP_CONTENT, WindowManager.LayoutParams.WRAP_CONTENT, true);

�������Ҫ��ʾ���PopupWindow�����Ե���showAtLocation����showAsDropDown��ǰ����ֱ�Ӱ�PopupWindow��ʾ��ĳ���ض�λ�ã���������ĳ��λ�ô������µ������������ǵ��õ�һ������������Ȥ�Ŀ��Գ����µڶ���������ʲô�Ŀ��Բ��ùܣ������Ŀ���API��֪���ˣ�֮���ٵ���update��������λ����Ϣ

    
    
    mPopupWindow.showAtLocation(itemView, Gravity.LEFT | Gravity.TOP, locations[0]+ itemView.getWidth() - popWidth, locations[1]);  
    mPopupWindow.update();

���ڶ�PopupWindow���ñ���ʲô�ģ���Ҫ��ʱ�򿴿�API��֪���ˣ����ﲻ��˵��



����Ч����ʵ�֣�����һ��һ��������

���Զ���һ��ListView����ϸ���������Ѿ�����������ע��

    
    
    public class SlidableListView extends ListView {
        private static final String TAG = "SlidableListView";
        private int touchSlop;
    
        private View itemView; // ÿ��Item��View
        private int mCurrentDownPosition; // ��ǰ�����λ�ã������ҳ������Item
        private PopupWindow mPopupWindow; // ɾ����ť
        private int popHeight, popWidth; // ɾ����ť�ĸߺͿ�
        private boolean isSliding = false; // ��ָ�Ƿ������󻬶�
        private int downX, downY, moveX, moveY; // �����X��Y���Լ��ƶ���X��Y
        private OnItemDeleteListener onItemDeleteListener; // ���ɾ����ťʱ��Ļص��ӿ�
    
        public SlidableListView(Context context) {
            super(context);
        }
    
        // �ڲ�����ʹ���Զ���ؼ����õĹ��캯�����������һЩ��Ҫ�ĳ�ʼ��
        public SlidableListView(Context context, AttributeSet attrs) {
            super(context, attrs);
            // ��ʼ��ɾ����ť���ǵ��ð�ť���Խ��ս���
            View view = LayoutInflater.from(context).inflate(R.layout.item_delete, null);
            mPopupWindow = new PopupWindow(view, WindowManager.LayoutParams.WRAP_CONTENT,
                    WindowManager.LayoutParams.WRAP_CONTENT, true);
            // ��ȡɾ����ť�Ŀ�͸�
            view.measure(0, 0);
            popWidth = view.getMeasuredWidth();
            popHeight = view.getMeasuredHeight();
            // ��ȡ��������С����
            touchSlop = ViewConfiguration.get(context).getScaledTouchSlop();
        }
    
        public SlidableListView(Context context, AttributeSet attrs, int defStyleAttr) {
            super(context, attrs, defStyleAttr);
        }
    
        // ������touch�¼������ݲ�ͬ������зַ�
        @Override
        public boolean dispatchTouchEvent(MotionEvent ev) {
            int action = ev.getAction();
            int x = (int) ev.getX(), y = (int) ev.getY();
            switch (action) {
                case MotionEvent.ACTION_DOWN:
                    downX = x;
                    downY = y;
                    // ���ɾ����ť������ʾ����ô��������������¼������Ѱ�ť��������
                    if (mPopupWindow.isShowing()) {
                        mPopupWindow.dismiss();
                        return false; // ��ֹ�¼�����
                    }
                    mCurrentDownPosition = pointToPosition(downX, downY);
                    // ��ȡ������item
                    itemView = getChildAt(mCurrentDownPosition - getFirstVisiblePosition());
                    break;
                case MotionEvent.ACTION_MOVE:
                    moveX = x;
                    moveY = y;
                    int deltaX = moveX - downX;
                    int deltaY = moveY - downY;
                    // ���ж�Ϊ�󻬵�ʱ�򣬰��¼�����OnTouchEvent������
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
            // �ж��Ƿ��Ѿ������һ���
            if (isSliding) {
                switch (action) {
                    case MotionEvent.ACTION_UP:
                        isSliding = false;
                        break;
                    case MotionEvent.ACTION_MOVE:
                        // ��ȡitem����Ļ�е�λ����Ϣ
                        int[] locations = new int[2];
                        itemView.getLocationOnScreen(locations);
                        // ��ɾ����ť���ö����������Զ��˳��ִ��ҵ���ƽ�ƣ����ش�����
                        mPopupWindow.setAnimationStyle(R.style.PopupWindowAnimations);
                        // ����ɾ����ť��λ�ã�����������item�����Ҷ�
                        mPopupWindow.showAtLocation(itemView, Gravity.LEFT | Gravity.TOP, locations[0]
                                + itemView.getWidth() - popWidth, locations[1]);
                        mPopupWindow.update();
                        // ��item����ƽ�ƶ��������ɾ����ť
                        ObjectAnimator.ofFloat(itemView, "translationX", 0, -popWidth).setDuration
                                (200).start();
                        // ��ȡɾ����ť��ʵ����������һ��TextView
                        TextView delete = (TextView) mPopupWindow.getContentView().findViewById(R.id
                                .delete);
                        // ���ü��������ɾ����ť��ʧ�������ť��ĵط�������back�����ص�
                        mPopupWindow.setOnDismissListener(new PopupWindow.OnDismissListener() {
                            @Override
                            public void onDismiss() {
                                // ��item�ƻ�ԭλ��
                                ObjectAnimator.ofFloat(itemView, "translationX", -popWidth, 0)
                                        .setDuration(200).start();
                            }
                        });
                        // ��ɾ����ť���õ���¼�
                        delete.setOnClickListener(new OnClickListener() {
                            @Override
                            public void onClick(View v) {
                                // ��ȡ�����itemλ�ã������ûص��ӿڷ������ͳ�ȥ������ɾ����ť
                                int position = SlidableListView.this.getPositionForView(itemView);
                                onItemDeleteListener.onItemDelete(position);
                                mPopupWindow.dismiss();
                            }
                        });
                        break;
                }
                // ������ڻ����������¼�
                return true;
            }
            return super.onTouchEvent(ev);
        }
    
        public void setOnItemDeleteListener(OnItemDeleteListener onItemDeleteListener) {
            this.onItemDeleteListener = onItemDeleteListener;
        }
    
        // �ص�ɾ���¼��Ľӿ�
        public interface OnItemDeleteListener {
            void onItemDelete(int position);
        }
    }

���Զ�����ʧ�ͽ���Ķ�������Ϊϵͳ��û��������Ҫ��ƽ�ƽ���

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

���Զ���Adapter�����ﲻ�����ˣ�����

���ڲ�����ʹ���Զ����ListView

    
    
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

����Activity��ʹ��adapter�������ݼ���

    
    
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
            // ʵ�ֻص��ӿڣ������߼�
            slidableListView.setOnItemDeleteListener(new SlidableListView.OnItemDeleteListener() {
                @Override
                public void onItemDelete(int position) {
                    data.remove(position);
                    adapter.notifyDataSetChanged();
                }
            });
        }
    }



