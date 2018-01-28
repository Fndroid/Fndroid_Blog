# Android开发学习之路-RecyclerView使用初探

在进行一些MaterialDesign规范开发的时候，比如之前说到的CoordinateLayout实现的向上折叠效果的时候，如果依然使用ListView，那么这种效果是做不出来的，因为ListView不兼容这个控件，而替代它的就是RecyclerView。

和ListView的区别：

①RecyclerView只关心item的重用和缓存

②RecyclerView不关心item的分隔风格（交给ItemDecoration）

③RecyclerView不关心item的动画（交给ItemAnimator）

④RecyclerView不关心item的布局（交给LayoutManager）

本文为初探，所以先不详解②③④这几个内容，先记录一下用法和遇到的坑，后续有时间再更新！

先放一下效果图，动画效果在之前的博客也有提到，可以参考，主要的是中间的RecyclerView，这里主要讲如何使用它以及遇到的一些问题

![](http://images2015.cnblogs.com/blog/852227/201603/852227-20160305165509799-488580700.gif)

先添加依赖，版本无所谓：

    
    
    compile 'com.android.support:recyclerview-v7:23.1.1'

布局文件如下，注意最后一个属性可以不用理，这是用来协调动画效果的（就是把上面图片推上去的效果）

    
    
    1 <android.support.v7.widget.RecyclerView
    2  android:id="@+id/main_lv"
    3  android:layout_width="match_parent"
    4  android:layout_height="match_parent"
    5  app:layout_behavior="@string/appbar_scrolling_view_behavior"/>

自定义一个Adapter继承自RecyclerView.Adapter，代码如下：

    
    
     1 public class MyAdapter extends RecyclerView.Adapter {
     2     private static final String TAG = "MyAdapter";
     3 
     4     private final List<Map<String, Object>> data;
     5     private final View root;
     6     private OnItemButtonCLickListener listener;
     7 
     8     public MyAdapter(List<Map<String, Object>> data, View root) {
     9         this.data = data;
    10         this.root = root;
    11     }
    12 
    13     public void setOnItemButtonClickListener(OnItemButtonCLickListener listener){
    14         this.listener = listener;
    15     }
    16 
    17 
    18     class ViewHolder extends RecyclerView.ViewHolder {
    19         TextView reason, money, date;
    20         Button delete,alter;
    21 
    22         public ViewHolder(View itemView) {
    23             super(itemView);
    24             reason = (TextView) itemView.findViewById(R.id.main_rv_item_reason);
    25             money = (TextView) itemView.findViewById(R.id.main_rv_item_money);
    26             date = (TextView) itemView.findViewById(R.id.main_rv_item_date);
    27             delete = (Button) itemView.findViewById(R.id.main_rv_item_remove);
    28             alter = (Button) itemView.findViewById(R.id.main_rv_item_alter);
    29         }
    30 
    31     }
    32 
    33     @Override
    34     public RecyclerView.ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
    35         ViewHolder vh = new ViewHolder(LayoutInflater.from(parent.getContext()).inflate(R.layout
    36                 .main_lv_item, null));
    37         return vh;
    38         
    39     }
    40 
    41     @Override
    42     public void onBindViewHolder(RecyclerView.ViewHolder holder, final int position) {
    43         final ViewHolder vh = (ViewHolder) holder;
    44         vh.date.setText(data.get(position).get("date").toString());
    45         vh.reason.setText(data.get(position).get("reason").toString());
    46         vh.money.setText(data.get(position).get("money").toString());
    47         vh.delete.setOnClickListener(new View.OnClickListener() {
    48             @Override
    49             public void onClick(View v) {
    50                 Snackbar snackBar = Snackbar.make(root, "Are you sure to delete it？", Snackbar.LENGTH_LONG);
    51                 snackBar.setAction("YES", new MyOnClickListener(vh.getLayoutPosition()));
    52                 snackBar.show();
    53             }
    54         });
    55         vh.alter.setOnClickListener(new View.OnClickListener() {
    56             @Override
    57             public void onClick(View v) {
    58                 listener.OnItemAlter(vh.getLayoutPosition());
    59             }
    60         });
    61     }
    62 
    63     @Override
    64     public int getItemCount() {
    65         return data.size();
    66     }
    67 
    68     private class MyOnClickListener implements View.OnClickListener {
    69         private final int position;
    70 
    71         public MyOnClickListener(int position) {
    72             this.position = position;
    73         }
    74 
    75         @Override
    76         public void onClick(View v) {
    77             listener.OnItemDelete(position);
    78         }
    79     }
    80 
    81     interface OnItemButtonCLickListener {
    82         void OnItemAlter(int position);
    83         void OnItemDelete(int position);
    84     }
    85 }



解析：构造方法中获取到数据源以及根View，根View用来显示SnackBar，如果不知道SnackBar，请看上一篇。然后创建一个内部类，继承自RecyclerView.ViewHolder，用它来保存每一个Item的全部控件。接下来重写几个方法：getItemCount、onBindViewHolder以及onCreateViewHolder，他们分别是返回item个数，绑定ViewHolder以及创建ViewHolder，创建ViewHolder就是通过Item的布局文件获取每个控件的实例，绑定ViewHolder就是把数据源中的数据映射到Item的控件中，因为我们的Item中还有删除和修改按钮，所以我们为它们添加点击事件也是在onBindViewHolder中进行。接下来我们定义一个接口OnItemButtonClickListener并且定义两个方法分别用来回调点击删除和修改，并创建接口的对象，在按钮点击的时候分别调用接口的两个方法，并传入点击的item的position。



Activity部分代码如下：

    
    
    1 rv = (RecyclerView) findViewById(R.id.main_lv);
    2 adapter = new MyAdapter(data, root);
    3 rv.setLayoutManager(new LinearLayoutManager(this,LinearLayoutManager.VERTICAL,false)); // 设置布局，否则无法正常使用
    4 adapter.setOnItemButtonClickListener(this);
    5 rv.setAdapter(adapter);
    
    
     1 @Override
     2     public void OnItemAlter(int position) {
     3         //Intent intent = new Intent(MainActivity.this, AlterActivity.class);
     4         //intent.putExtra("id", Integer.parseInt(data.get(position).get("id").toString()));
     5         //intent.putExtra("date", data.get(position).get("date").toString());
     6         //intent.putExtra("money", data.get(position).get("money").toString());
     7         //intent.putExtra("reason", data.get(position).get("reason").toString());
     8         //startActivityForResult(intent, REQUEST_ALTER);
     9     }
    10 
    11     @Override
    12     public void OnItemDelete(int position) {
    13         Log.d(TAG, "OnItemDelete() called with: " + "position = [" + position + "]");
    14         //Map<String, Object> backup = data.get(position);
    15         //data.remove(position);
    16         //database.execSQL("delete from tally where id=?", new String[]{backup.get
    17         //        ("id").toString()});
    18         adapter.notifyItemRemoved(position);
    19     }

解析：获取RecyclerView的实例之后，设置对应的布局管理器，然后通过setOnItemButtonClickListener设置回调函数并实现，最后给RecyclerView设置adapter即可。



遇到的问题：

①刚开始忘记设置布局，RecyclerView不显示出来

②
**因为我们在删除item之后调用了notifyItemRemoved方法来显示动画并刷新，在onBindViewHolder中，对删除按钮监听之后调用接口方法的时候传入了position，这个position是错误的，因为在绑定的时候固定了，如果数据源的数据被删除了，数据源的size会变小，这个position就不再对应于数据源的每一个项就会产生混乱，所以这里应该传入viewholder.getLayoutPosition()所得到的值，这个值是对应点击而产生的。**

③如果在RecyclerView的第一项前面插入一个item，并且调用notifyItmeInserted(0)，是不会播放动画效果的

