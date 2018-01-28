# Android����ѧϰ֮·-DiffUtilʹ�ý̳�

�ȸ����������Support Library 24.2.0����DiffUtil����������汾��ӵ�һ�������ࡣ

DiffUtil��һ�����Ҽ��ϱ仯�Ĺ����࣬�Ǵ���RecyclerViewһ��ʹ�õģ�����㻹���˽�RecyclerView�������Ķ�һЩ���ϻ����ҵĲ��ͣ�[RecyclerViewʹ�ó�̽](http://www.cnblogs.com/Fndroid/p/5245250.html)

���ݹ������ȷ�Ч��ͼ��

![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160819224424031-573442771.gif)

���Կ����������ǵ����ť��ʱ�����RecyclerView����ʾ�ļ��Ϸ����˸ı䣬�е�Ԫ�ر������ˣ�8.Jason����Ҳ�е�Ԫ�ر��ƶ��ˣ�3.Rose���������Ǳ��޸��ˣ�2.Fndroid����RecyclerView����ÿ��Item�Ķ������Բ�ͬ��ʽˢ�µģ�

  * notifyItemInserted
  * notifyItemChanged
  * notifyItemMoved
  * notifyItemRemoved

�����������ļ���Item��ˢ�£����Ե��ã�

  * notifyItemRangeChanged
  * notifyItemRangeInserted
  * notifyItemRangeRemoved

�����ڼ��Ϸ����仯��ʱ��ֻ���Ե���notifyDataSetChanged�����������������ˢ�£������ܸ��ݼ��ϵı仯Ϊÿһ���仯��Ԫ����Ӷ������������������DiffUtil�����������⡣

**DiffUtil�����ã������ҳ�������ÿһ��Item�����ı仯��Ȼ���ÿ���仯�����Ӧ��ˢ�¡�**

���DiffUtilʹ�õ��� **Eugene Myers**
�Ĳ���㷨������㷨�����ܼ�鵽Ԫ�ص��ƶ���Ҳ�����ƶ�ֻ�ܱ�������ɾ���������ӣ���DiffUtil�����㷨�Ľ�����ٽ���һ���ƶ���顣�����ڲ����Ԫ���ƶ�������£��㷨��ʱ�临�Ӷ�ΪO(N
+ D2)�������Ԫ���ƶ����Ӷ�ΪO(N2)�����ԣ�������ϱ�����Ѿ��ź��򣬿��Բ������ƶ��ļ������Ч�ʡ�



��������һ�����������������ô�á�

���ȶ���ÿ��Item��������һ��Student����

    
    
     1 class Student {
     2     private String name;
     3     private int num;
     4 
     5     public Student(String name, int num) {
     6         this.name = name;
     7         this.num = num;
     8     }
     9 
    10     public String getName() {
    11         return name;
    12     }
    13 
    14     public void setName(String name) {
    15         this.name = name;
    16     }
    17 
    18     public int getNum() {
    19         return num;
    20     }
    21 
    22     public void setNum(int num) {
    23         this.num = num;
    24     }
    25 }

�������Ƕ��岼�֣�ʡ�ԣ�����������

    
    
     1     class MyAdapter extends RecyclerView.Adapter {
     2         private ArrayList<Student> data;
     3 
     4         ArrayList<Student> getData() {
     5             return data;
     6         }
     7 
     8         void setData(ArrayList<Student> data) {
     9             this.data = new ArrayList<>(data);
    10         }
    11 
    12         @Override
    13         public RecyclerView.ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
    14             View itemView = LayoutInflater.from(RecyclerViewActivity.this).inflate(R.layout.itemview, null);
    15             return new MyViewHolder(itemView);
    16         }
    17 
    18         @Override
    19         public void onBindViewHolder(RecyclerView.ViewHolder holder, int position) {
    20             MyViewHolder myViewHolder = (MyViewHolder) holder;
    21             Student student = data.get(position);
    22             myViewHolder.tv.setText(student.getNum() + "." + student.getName());
    23         }
    24 
    25         @Override
    26         public int getItemCount() {
    27             return data.size();
    28         }
    29 
    30         class MyViewHolder extends RecyclerView.ViewHolder {
    31             TextView tv;
    32 
    33             MyViewHolder(View itemView) {
    34                 super(itemView);
    35                 tv = (TextView) itemView.findViewById(R.id.item_tv);
    36             }
    37         }
    38     }

��ʼ�����ݼ��ϣ�

    
    
     1     private void initData() {
     2         students = new ArrayList<>();
     3         Student s1 = new Student("John", 1);
     4         Student s2 = new Student("Curry", 2);
     5         Student s3 = new Student("Rose", 3);
     6         Student s4 = new Student("Dante", 4);
     7         Student s5 = new Student("Lunar", 5);
     8         students.add(s1);
     9         students.add(s2);
    10         students.add(s3);
    11         students.add(s4);
    12         students.add(s5);
    13     }

����ʵ����Adapter�����ø�RecyclerView��

    
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_recycler_view);
            initData();
            recyclerView = (RecyclerView) findViewById(R.id.rv);
            recyclerView.setLayoutManager(new LinearLayoutManager(this));
            adapter = new MyAdapter();
            adapter.setData(students);
            recyclerView.setAdapter(adapter);
        }

��Щ���ݶ����Ǳ�ƪ�����ݣ����ǣ���Ҫע�⵽��һ���ط���Adapter�Ķ��壺

    
    
     1     class MyAdapter extends RecyclerView.Adapter {
     2         private ArrayList<Student> data;
     3 
     4         ArrayList<Student> getData() {
     5             return data;
     6         }
     7 
     8         void setData(ArrayList<Student> data) {
     9             this.data = new ArrayList<>(data);
    10         }
    11 
    12         // ʡ�Բ��ִ���
    13          ......  
    14     }   

**�����setData����������ֱ�ӽ�ArrayList�����ñ��棬�������µĽ���һ��ArrayList���ȼ��ţ���������ΪʲôҪ������** ��

* * *

DiffUtil��ʹ�÷�����

����갴��ʱ���޸�ArrayList�����ݣ�

    
    
     1     public void change(View view) {
     2         students.set(1, new Student("Fndroid", 2));
     3         students.add(new Student("Jason", 8));
     4         Student s2 = students.get(2);
     5         students.remove(2);
     6         students.add(s2);
     7 
     8         ArrayList<Student> old_students = adapter.getData();
     9         DiffUtil.DiffResult result = DiffUtil.calculateDiff(new MyCallback(old_students, students), true);
    10         adapter.setData(students);
    11         result.dispatchUpdatesTo(adapter);
    12     }

2-6���ǶԼ��Ͻ����޸ģ���8���Ȼ�ȡ��adapter�еļ���Ϊ�ɵ����ݡ�

�ص㿴��9�е���DiffUtil.calculateDiff���������㼯�ϵĲ������Ҫ����һ��CallBack�ӿڵ�ʵ���ࣨ����ָ������Ĺ��򣩲��Ұ��¾����ݶ����ݸ�����ӿڵ�ʵ���࣬�����һ��boolean���͵Ĳ������������ָ���Ƿ���Ҫ����Move�ļ�⣬�������Ҫ�������Item�ƶ��ˣ��ᱻ��Ϊ����remove��Ȼ��insert������ָ��Ϊtrue�����Ծ����˶�ͼ��ʾ���ƶ�Ч����

��10�����½��µ��������ø�Adapter��

��11�е��õ�9�еõ���DiffResult�����dispatchUpdatesTo����֪ͨRecyclerViewˢ�¶�Ӧ�����仯��Item��

**����ص�����˵��setData��������Ϊ����������Ҫ�����������ϣ������setData������ֱ�ӱ������ã���ô��2-6�е��޸ľ�ֱ���޸���Adapter�еļ����ˣ�Java֪ʶ����**

������ò����Item���ƶ���Ч�����£�

**![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160820002517687-1558905260.gif)**



�������ǿ���CallBack�ӿڵ�ʵ������ζ��壺

    
    
     1     private class MyCallback extends DiffUtil.Callback {
     2         private ArrayList<Student> old_students, new_students;
     3 
     4         MyCallback(ArrayList<Student> data, ArrayList<Student> students) {
     5             this.old_students = data;
     6             this.new_students = students;
     7         }
     8 
     9         @Override
    10         public int getOldListSize() {
    11             return old_students.size();
    12         }
    13 
    14         @Override
    15         public int getNewListSize() {
    16             return new_students.size();
    17         }
    18 
    19         // �ж�Item�Ƿ��Ѿ�����
    20         @Override
    21         public boolean areItemsTheSame(int oldItemPosition, int newItemPosition) {
    22             return old_students.get(oldItemPosition).getNum() == new_students.get(newItemPosition).getNum();
    23         }
    24 
    25         // ���Item�Ѿ����������ô˷������ж�Item�������Ƿ�һ��
    26         @Override
    27         public boolean areContentsTheSame(int oldItemPosition, int newItemPosition) {
    28             return old_students.get(oldItemPosition).getName().equals(new_students.get(newItemPosition).getName());
    29         }
    30     }

�������ѧ���ж��Ƿ�ͬһ��Item�����������ж����Item�Ƿ��б��޸ġ�



ʵ���ϣ����Callback�����໹��һ������getChangePayload()��������������������ǿ���ͨ�������������Adapter�����Item���оֲ��ĸ��¶������������¡�

��Ҫ֪�����payload��ʲô��payload��һ����������Item�仯�Ķ���Ҳ�������ǵ�Item��������Щ�仯����Щ�仯�ͷ�װ��һ��payload����������һ�������Bundle���䵱��

���ţ�getChangePayload()��������areItemsTheSame()����true����areContentsTheSame()����falseʱ���ص��ģ�Ҳ����һ��Item�����ݷ����˱仯��������仯�п����Ǿֲ��ģ�����΢���ĵ��ޣ�����ֻ��Ҫˢ��ͼ�����������Item�������Կ�����getChangePayload()�з�װһ��Object������RecyclerView���оֲ���ˢ�¡�

����������ѧ�ź������ò�ͬ��TextView��ʾ���������޸���һ��ѧ�Ŷ�Ӧ������ʱ���ֲ�ˢ���������ɣ��������ӿ����ԵñȽ϶��࣬�������һ��Item�ܸ��ӣ��ô��ͱȽϴ��ˣ���

������дCallback�еĸ÷�����

    
    
    1         @Nullable
    2         @Override
    3         public Object getChangePayload(int oldItemPosition, int newItemPosition) {
    4             Student newStudent = newStudents.get(newItemPosition);
    5             Bundle diffBundle = new Bundle();
    6             diffBundle.putString(NAME_KEY, newStudent.getName());
    7             return diffBundle;
    8         } 

���ص�����������ʲô�ط��յ��أ�ʵ������RecyclerView.Adapter��������onBindViewHolder������һ�������Ǳ���Ҫ��д�ģ�����һ���ĵ�������������һ��payload���б�

    
    
    1         @Override
    2         public void onBindViewHolder(RecyclerView.ViewHolder holder, int position, List payloads) {}

��������ֻ����Adapter����д������������ListΪ�գ�ִ��ԭ����onBindViewHolder��������Item�ĸ��£��������payloads�����ݽ��оֲ�ˢ�£�

    
    
     1         @Override
     2         public void onBindViewHolder(RecyclerView.ViewHolder holder, int position, List payloads) {
     3             if (payloads.isEmpty()) {
     4                 onBindViewHolder(holder, position);
     5             } else {
     6                 MyViewHolder myViewHolder = (MyViewHolder) holder;
     7                 Bundle bundle = (Bundle) payloads.get(0);
     8                 if (bundle.getString(NAME_KEY) != null) {
     9                     myViewHolder.name.setText(bundle.getString(NAME_KEY));
    10                     myViewHolder.name.setTextColor(Color.BLUE);
    11                 }
    12             }
    13         }

�����payloads����Ϊnull������ֱ���ж��Ƿ�Ϊ�ռ��ɡ�

![](http://images2015.cnblogs.com/blog/852227/201609/852227-20160901145317215-2028201275.gif)



**����ע�⣺���RecyclerView�м����˴������ݣ���ô�㷨���ܲ���������ɣ�Ҫע��ANR�����⣬���Կ����������߳̽��м��㡣**

