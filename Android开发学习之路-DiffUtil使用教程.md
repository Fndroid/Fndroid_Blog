# Android开发学习之路-DiffUtil使用教程

谷歌最近更新了Support Library 24.2.0，而DiffUtil就是在这个版本添加的一个工具类。

DiffUtil是一个查找集合变化的工具类，是搭配RecyclerView一起使用的，如果你还不了解RecyclerView，可以阅读一些资料或者我的博客：[RecyclerView使用初探](http://www.cnblogs.com/Fndroid/p/5245250.html)

根据惯例，先放效果图：

![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160819224424031-573442771.gif)

可以看到，当我们点击按钮的时候，这个RecyclerView所显示的集合发生了改变，有的元素被增加了（8.Jason），也有的元素被移动了（3.Rose），甚至是被修改了（2.Fndroid）。RecyclerView对于每个Item的动画是以不同方式刷新的：

  * notifyItemInserted
  * notifyItemChanged
  * notifyItemMoved
  * notifyItemRemoved

而对于连续的几个Item的刷新，可以调用：

  * notifyItemRangeChanged
  * notifyItemRangeInserted
  * notifyItemRangeRemoved

而由于集合发生变化的时候，只可以调用notifyDataSetChanged方法进行整个界面的刷新，并不能根据集合的变化为每一个变化的元素添加动画。所以这里就有了DiffUtil来解决这个问题。

**DiffUtil的作用，就是找出集合中每一个Item发生的变化，然后对每个变化给予对应的刷新。**

这个DiffUtil使用的是 **Eugene Myers**
的差别算法，这个算法本身不能检查到元素的移动，也就是移动只能被算作先删除、再增加，而DiffUtil是在算法的结果后再进行一次移动检查。假设在不检测元素移动的情况下，算法的时间复杂度为O(N
+ D2)，而检测元素移动则复杂度为O(N2)。所以，如果集合本身就已经排好序，可以不进行移动的检测提升效率。



下面我们一起来看看这个工具怎么用。

首先对于每个Item，数据是一个Student对象：

    
    
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

接着我们定义布局（省略）和适配器：

    
    
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

初始化数据集合：

    
    
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

接着实例化Adapter并设置给RecyclerView：

    
    
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

这些内容都不是本篇的内容，但是，需要注意到的一个地方是Adapter的定义：

    
    
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
    12         // 省略部分代码
    13          ......  
    14     }   

**这里的setData方法并不是直接将ArrayList的引用保存，而是重新的建立一个ArrayList，先记着，后面会解释为什么要这样做** 。

* * *

DiffUtil的使用方法：

当鼠标按下时，修改ArrayList的内容：

    
    
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

2-6行是对集合进行修改，第8行先获取到adapter中的集合为旧的数据。

重点看第9行调用DiffUtil.calculateDiff方法来计算集合的差别，这里要传入一个CallBack接口的实现类（用于指定计算的规则）并且把新旧数据都传递给这个接口的实现类，最后还有一个boolean类型的参数，这个参数指定是否需要进行Move的检测，如果不需要，如果有Item移动了，会被认为是先remove，然后insert。这里指定为true，所以就有了动图显示的移动效果。

第10行重新将新的数据设置给Adapter。

第11行调用第9行得到的DiffResult对象的dispatchUpdatesTo方法通知RecyclerView刷新对应发生变化的Item。

**这里回到上面说的setData方法，因为我们在这里要区分两个集合，如果在setData方法中直接保存引用，那么在2-6行的修改就直接修改了Adapter中的集合了（Java知识）。**

如果设置不检查Item的移动，效果如下：

**![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160820002517687-1558905260.gif)**



接着我们看看CallBack接口的实现类如何定义：

    
    
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
    19         // 判断Item是否已经存在
    20         @Override
    21         public boolean areItemsTheSame(int oldItemPosition, int newItemPosition) {
    22             return old_students.get(oldItemPosition).getNum() == new_students.get(newItemPosition).getNum();
    23         }
    24 
    25         // 如果Item已经存在则会调用此方法，判断Item的内容是否一致
    26         @Override
    27         public boolean areContentsTheSame(int oldItemPosition, int newItemPosition) {
    28             return old_students.get(oldItemPosition).getName().equals(new_students.get(newItemPosition).getName());
    29         }
    30     }

这里根据学号判断是否同一个Item，根据姓名判断这个Item是否有被修改。



实际上，这个Callback抽象类还有一个方法getChangePayload()，这个方法的作用是我们可以通过这个方法告诉Adapter对这个Item进行局部的更新而不是整个更新。

先要知道这个payload是什么？payload是一个用来描述Item变化的对象，也就是我们的Item发生了哪些变化，这些变化就封装成一个payload，所以我们一般可以用Bundle来充当。

接着，getChangePayload()方法是在areItemsTheSame()返回true，而areContentsTheSame()返回false时被回调的，也就是一个Item的内容发生了变化，而这个变化有可能是局部的（例如微博的点赞，我们只需要刷新图标而不是整个Item）。所以可以在getChangePayload()中封装一个Object来告诉RecyclerView进行局部的刷新。

假设上例中学号和姓名用不同的TextView显示，当我们修改了一个学号对应的姓名时，局部刷新姓名即可（这里例子可能显得比较多余，但是如果一个Item很复杂，用处就比较大了）：

先是重写Callback中的该方法：

    
    
    1         @Nullable
    2         @Override
    3         public Object getChangePayload(int oldItemPosition, int newItemPosition) {
    4             Student newStudent = newStudents.get(newItemPosition);
    5             Bundle diffBundle = new Bundle();
    6             diffBundle.putString(NAME_KEY, newStudent.getName());
    7             return diffBundle;
    8         } 

返回的这个对象会在什么地方收到呢？实际上在RecyclerView.Adapter中有两个onBindViewHolder方法，一个是我们必须要重写的，而另一个的第三个参数就是一个payload的列表：

    
    
    1         @Override
    2         public void onBindViewHolder(RecyclerView.ViewHolder holder, int position, List payloads) {}

所以我们只需在Adapter中重写这个方法，如果List为空，执行原来的onBindViewHolder进行整个Item的更新，否则根据payloads的内容进行局部刷新：

    
    
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

这里的payloads不会为null，所以直接判断是否为空即可。

![](http://images2015.cnblogs.com/blog/852227/201609/852227-20160901145317215-2028201275.gif)



**这里注意：如果RecyclerView中加载了大量数据，那么算法可能不会马上完成，要注意ANR的问题，可以开启单独的线程进行计算。**

