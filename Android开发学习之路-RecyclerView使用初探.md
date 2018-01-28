# Android����ѧϰ֮·-RecyclerViewʹ�ó�̽

�ڽ���һЩMaterialDesign�淶������ʱ�򣬱���֮ǰ˵����CoordinateLayoutʵ�ֵ������۵�Ч����ʱ�������Ȼʹ��ListView����ô����Ч�������������ģ���ΪListView����������ؼ�����������ľ���RecyclerView��

��ListView������

��RecyclerViewֻ����item�����úͻ���

��RecyclerView������item�ķָ���񣨽���ItemDecoration��

��RecyclerView������item�Ķ���������ItemAnimator��

��RecyclerView������item�Ĳ��֣�����LayoutManager��

����Ϊ��̽�������Ȳ����ڢۢ��⼸�����ݣ��ȼ�¼һ���÷��������Ŀӣ�������ʱ���ٸ��£�

�ȷ�һ��Ч��ͼ������Ч����֮ǰ�Ĳ���Ҳ���ᵽ�����Բο�����Ҫ�����м��RecyclerView��������Ҫ�����ʹ�����Լ�������һЩ����

![](http://images2015.cnblogs.com/blog/852227/201603/852227-20160305165509799-488580700.gif)

������������汾����ν��

    
    
    compile 'com.android.support:recyclerview-v7:23.1.1'

�����ļ����£�ע�����һ�����Կ��Բ�������������Э������Ч���ģ����ǰ�����ͼƬ����ȥ��Ч����

    
    
    1 <android.support.v7.widget.RecyclerView
    2  android:id="@+id/main_lv"
    3  android:layout_width="match_parent"
    4  android:layout_height="match_parent"
    5  app:layout_behavior="@string/appbar_scrolling_view_behavior"/>

�Զ���һ��Adapter�̳���RecyclerView.Adapter���������£�

    
    
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
    50                 Snackbar snackBar = Snackbar.make(root, "Are you sure to delete it��", Snackbar.LENGTH_LONG);
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



���������췽���л�ȡ������Դ�Լ���View����View������ʾSnackBar�������֪��SnackBar���뿴��һƪ��Ȼ�󴴽�һ���ڲ��࣬�̳���RecyclerView.ViewHolder������������ÿһ��Item��ȫ���ؼ�����������д����������getItemCount��onBindViewHolder�Լ�onCreateViewHolder�����Ƿֱ��Ƿ���item��������ViewHolder�Լ�����ViewHolder������ViewHolder����ͨ��Item�Ĳ����ļ���ȡÿ���ؼ���ʵ������ViewHolder���ǰ�����Դ�е�����ӳ�䵽Item�Ŀؼ��У���Ϊ���ǵ�Item�л���ɾ�����޸İ�ť����������Ϊ������ӵ���¼�Ҳ����onBindViewHolder�н��С����������Ƕ���һ���ӿ�OnItemButtonClickListener���Ҷ������������ֱ������ص����ɾ�����޸ģ��������ӿڵĶ����ڰ�ť�����ʱ��ֱ���ýӿڵ���������������������item��position��



Activity���ִ������£�

    
    
    1 rv = (RecyclerView) findViewById(R.id.main_lv);
    2 adapter = new MyAdapter(data, root);
    3 rv.setLayoutManager(new LinearLayoutManager(this,LinearLayoutManager.VERTICAL,false)); // ���ò��֣������޷�����ʹ��
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

��������ȡRecyclerView��ʵ��֮�����ö�Ӧ�Ĳ��ֹ�������Ȼ��ͨ��setOnItemButtonClickListener���ûص�������ʵ�֣�����RecyclerView����adapter���ɡ�



���������⣺

�ٸտ�ʼ�������ò��֣�RecyclerView����ʾ����

��
**��Ϊ������ɾ��item֮�������notifyItemRemoved��������ʾ������ˢ�£���onBindViewHolder�У���ɾ����ť����֮����ýӿڷ�����ʱ������position�����position�Ǵ���ģ���Ϊ�ڰ󶨵�ʱ��̶��ˣ��������Դ�����ݱ�ɾ���ˣ�����Դ��size���С�����position�Ͳ��ٶ�Ӧ������Դ��ÿһ����ͻ�������ң���������Ӧ�ô���viewholder.getLayoutPosition()���õ���ֵ�����ֵ�Ƕ�Ӧ����������ġ�**

�������RecyclerView�ĵ�һ��ǰ�����һ��item�����ҵ���notifyItmeInserted(0)���ǲ��Ქ�Ŷ���Ч����

