# Android开发学习之路-下拉刷新以及GridView的使用

GridView是类似于ListView的控件，只是GridView可以使用多个列来呈现内容，而ListView是以行为单位，所以用法上是差不多的。

主布局文件，因为要做下拉刷新，所以加了一个ProgressBar，GridView的numColumns属性是指每一行有多少列

![](http://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif)![](http://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif)

    
    
     1 <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
     2     xmlns:tools="http://schemas.android.com/tools"
     3     android:layout_width="match_parent"
     4     android:layout_height="match_parent"
     5     tools:context="com.example.girdlayoutdemo.MainActivity" >
     6 
     7     <ProgressBar
     8         android:id="@+id/pb"
     9         android:layout_width="wrap_content"
    10         android:layout_height="wrap_content"
    11         android:layout_centerHorizontal="true" />
    12 
    13     <GridView
    14         android:layout_below="@id/pb"
    15         android:id="@+id/gv"
    16         android:layout_width="match_parent"
    17         android:layout_height="match_parent"
    18         android:layout_margin="5dp"
    19         android:gravity="center"
    20         android:numColumns="2" >
    21     </GridView>
    22 
    23 </RelativeLayout>

View Code

每个Item的布局文件，这里比较简单的一张图片加一段文字

![](http://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif)![](http://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif)

    
    
     1 <?xml version="1.0" encoding="utf-8"?>
     2 <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
     3     android:layout_width="match_parent"
     4     android:layout_height="match_parent"
     5     android:orientation="vertical" >
     6 
     7     <ImageView
     8         android:id="@+id/item_iv"
     9         android:layout_width="fill_parent"
    10         android:layout_height="0dp"
    11         android:layout_weight="3" />
    12 
    13     <TextView
    14         android:id="@+id/item_tv"
    15         android:layout_width="match_parent"
    16         android:layout_height="0dp"
    17         android:layout_weight="1" />
    18 
    19 </LinearLayout>

View Code

主活动代码：

![](http://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif)![](http://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif)

    
    
      1 public class MainActivity extends Activity {
      2     private GridView gv;
      3     private ProgressBar pb;
      4     private List<Map<String, Object>> list;
      5     private SimpleAdapter adapter;
      6     private GestureDetector gsDetector;
      7 
      8     private Handler handler = new Handler() {
      9         @Override
     10         public void handleMessage(Message msg) {
     11             switch (msg.what) {
     12             case 1:
     13                 pb.setVisibility(View.GONE);
     14                 Toast.makeText(MainActivity.this, "刷新成功", 200).show();
     15                 break;
     16             default:
     17                 break;
     18             }
     19         }
     20     };
     21 
     22     @Override
     23     protected void onCreate(Bundle savedInstanceState) {
     24         super.onCreate(savedInstanceState);
     25         setContentView(R.layout.activity_main);
     26         pb = (ProgressBar) findViewById(R.id.pb);
     27         gv = (GridView) findViewById(R.id.gv);
     28         pb.setVisibility(View.GONE);
     29         initData();
     30         adapter = new SimpleAdapter(this, list, R.layout.item_layout,
     31                 new String[] { "image", "text" }, new int[] { R.id.item_iv,
     32                         R.id.item_tv });
     33         gv.setAdapter(adapter);
     34         gsDetector = new GestureDetector(this, new Mlistener());
     35         gv.setOnTouchListener(new OnTouchListener() {
     36 
     37             @Override
     38             public boolean onTouch(View v, MotionEvent event) {
     39                 // Log.e("MainActivity", event.getX()+"");
     40                 return gsDetector.onTouchEvent(event);
     41             }
     42         });
     43     }
     44 
     45     private void initData() {
     46         list = new ArrayList<Map<String, Object>>();
     47         for (int i = 0; i < 20; i++) {
     48             Map<String, Object> map = new HashMap<String, Object>();
     49             map.put("image", R.drawable.gift_item_default);
     50             map.put("text", "一只冰莹猪猪");
     51             list.add(map);
     52         }
     53     }
     54 
     55     class Mlistener implements OnGestureListener {
     56 
     57         @Override
     58         public boolean onDown(MotionEvent e) {
     59             return false;
     60         }
     61 
     62         @Override
     63         public void onShowPress(MotionEvent e) {
     64 
     65         }
     66 
     67         @Override
     68         public boolean onSingleTapUp(MotionEvent e) {
     69             return false;
     70         }
     71 
     72         @Override
     73         public boolean onScroll(MotionEvent e1, MotionEvent e2,
     74                 float distanceX, float distanceY) {
     75             return false;
     76         }
     77 
     78         @Override
     79         public void onLongPress(MotionEvent e) {
     80 
     81         }
     82 
     83         @Override
     84         public boolean onFling(MotionEvent e1, MotionEvent e2, float velocityX,
     85                 float velocityY) {
     86             if (e2.getY() - e1.getY() > 0 && gv.getFirstVisiblePosition() == 0) {
     87                 pb.setVisibility(View.VISIBLE);
     88                 Animation animation = new ScaleAnimation(1f, 1f, 0, 1f);
     89                 animation.setDuration(300);
     90                 pb.startAnimation(animation);
     91 
     92                 new Thread(new Runnable() {
     93                     @Override
     94                     public void run() {
     95                         try {
     96                             Thread.sleep(2000);
     97                             Message msg = new Message();
     98                             msg.what = 1;
     99                             handler.sendMessage(msg);
    100                         } catch (InterruptedException e) {
    101                             // TODO Auto-generated catch block
    102                             e.printStackTrace();
    103                         }
    104                     }
    105                 }).start();
    106 
    107             }
    108             return false;
    109         }
    110 
    111     }
    112 
    113 }

View Code



解析：

在onCreate方法中先获取两个组件，然后把progressBar设置为隐藏，下拉的时候再显示，刷新完毕再隐藏。然后为GridView设置数据源，这里方便起见用SimpleAdapter，然后给GridView设置ontouchListener，并在onTouch方法中把触摸事件交给我们自定义的GestureDetector对象来处理，在GestureDetector的onFling方法中处理下拉事件，在里面判断是否下拉以及GridView是否在最顶端，如果是，显示progressBar控件并开一个线程来处理刷新，这里做模拟就睡眠2000毫秒，最后用message对象返回一个消息给Handler，Handler在主线程中更新GridView。

    
    
     1 @Override
     2     protected void onCreate(Bundle savedInstanceState) {
     3         super.onCreate(savedInstanceState);
     4         setContentView(R.layout.activity_main);
     5         pb = (ProgressBar) findViewById(R.id.pb);
     6         gv = (GridView) findViewById(R.id.gv);
     7         pb.setVisibility(View.GONE);
     8         initData();
     9         adapter = new SimpleAdapter(this, list, R.layout.item_layout,
    10                 new String[] { "image", "text" }, new int[] { R.id.item_iv,
    11                         R.id.item_tv });
    12         gv.setAdapter(adapter);
    13         gsDetector = new GestureDetector(this, new Mlistener());
    14         gv.setOnTouchListener(new OnTouchListener() {
    15 
    16             @Override
    17             public boolean onTouch(View v, MotionEvent event) {
    18                 // Log.e("MainActivity", event.getX()+"");
    19                 return gsDetector.onTouchEvent(event);
    20             }
    21         });
    22     }
    
    
     1 class Mlistener implements OnGestureListener {
     2 
     3         @Override
     4         public boolean onDown(MotionEvent e) {
     5             return false;
     6         }
     7 
     8         @Override
     9         public void onShowPress(MotionEvent e) {
    10 
    11         }
    12 
    13         @Override
    14         public boolean onSingleTapUp(MotionEvent e) {
    15             return false;
    16         }
    17 
    18         @Override
    19         public boolean onScroll(MotionEvent e1, MotionEvent e2,
    20                 float distanceX, float distanceY) {
    21             return false;
    22         }
    23 
    24         @Override
    25         public void onLongPress(MotionEvent e) {
    26 
    27         }
    28 
    29         @Override
    30         public boolean onFling(MotionEvent e1, MotionEvent e2, float velocityX,
    31                 float velocityY) {
    32             if (e2.getY() - e1.getY() > 0 && gv.getFirstVisiblePosition() == 0) {
    33                 pb.setVisibility(View.VISIBLE);
    34                 Animation animation = new ScaleAnimation(1f, 1f, 0, 1f);
    35                 animation.setDuration(300);
    36                 pb.startAnimation(animation);
    37 
    38                 new Thread(new Runnable() {
    39                     @Override
    40                     public void run() {
    41                         try {
    42                             Thread.sleep(2000);
    43                             Message msg = new Message();
    44                             msg.what = 1;
    45                             handler.sendMessage(msg);
    46                         } catch (InterruptedException e) {
    47                             // TODO Auto-generated catch block
    48                             e.printStackTrace();
    49                         }
    50                     }
    51                 }).start();
    52 
    53             }
    54             return false;
    55         }
    56 
    57     }



