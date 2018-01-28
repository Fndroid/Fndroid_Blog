# 学习日记-从爬虫到接口到APP

最近都在复习J2E，多学习一些东西肯定是好的，而且现在移动开发工作都不好找了，有工作就推荐一下小弟呗，广州佛山地区，谢谢了。

这篇博客要做的效果很简单，就是把我博客的第一页每个条目显示在APP上，条目包括标题、摘要和状态，如图：

![](http://images2015.cnblogs.com/blog/852227/201611/852227-20161109092419920-1780857662.png)

所以这篇博客将会涉及：

  1. 数据库（MySql）简单设计（建表、插入数据）
  2. 简单爬虫（用Python爬取网页内容，写入数据库）
  3. 简单接口开发（Struts和Hibernate）
  4. APP网络请求（Retrofit、Gson、RxJava等）

**大体的流程就是：先创建数据库，通过爬虫手段爬取博客首页的条目内容并填充至数据库，接着搭建简单的JavaWeb后台，提供接口访问，通过网络请求返回数据库中的数据。**



**① 数据库设计**

要爬取数据和接口开发，肯定都是需要先创建数据库和数据表的。这里使用的是MySql，操作的工具是Navicat。对于上面的数据，我们需要建立对应的表：

![](http://images2015.cnblogs.com/blog/852227/201611/852227-20161109094609530-757856275.png)

其中id为主键且自增长。创建完毕可以进行插入和删除等测试。



**② 爬取网页数据**

静态网页的爬取是比较简单的，其实就是根据网页源码进行解析匹配，而Python的正则表达式较为强大，所以这里使用Python来进行操作，另外，基础的爬虫也可以使用一些库来简化操作，这里会用到request和bs4两个，request用于网络请求，而bs4则是用于解析网页源码得到我们想要的数据。最后，通过MySQLdb对数据进行存储。

先分析网页源码，可以使用Chrome来观察结构：

![](http://images2015.cnblogs.com/blog/852227/201611/852227-20161109095313233-1162476311.png)

得到结构之后就可以进行编码：

文件名：MySpider.py

    
    
     1 #coding=utf-8
     2 
     3 import sys
     4 import requests
     5 from bs4 import BeautifulSoup
     6 import MySQLdb
     7 
     8 reload(sys)
     9 sys.setdefaultencoding('utf-8')
    10 
    11 # 定义一个博客类
    12 class Blog:
    13     title = ""
    14     desc = ""
    15     postDate = ""
    16     status = ""
    17 
    18 # 进行网络请求拉取源码，地址为我博客首页
    19 response = requests.get("http://www.cnblogs.com/Fndroid/")
    20 # 使用BeautifulSoup进行处理
    21 soup = BeautifulSoup(response.text, "html.parser")
    22 
    23 blogs = []
    24 # 根据源码格式爬取栏目，找到class为day的标签，获取并遍历其子div
    25 for day in soup.findAll(class_='day'):
    26     divs = day.findAll("div")
    27     n = 0
    28     b = Blog()
    29     for div in divs:
    30         if n == 0:
    31             # 爬取发表时间
    32             b.postDate = div.a.string
    33         elif n == 1:
    34             # 爬取标题
    35             b.title = div.a.string
    36         elif n == 2:
    37             # 爬取摘要
    38             b.desc = div.div.contents[0]
    39         elif n == 5:
    40             # 爬取文章状态
    41             b.status = div.contents[0]
    42         elif n == 6:
    43             n = 0
    44             blogs.append(b)
    45             break
    46 
    47         n += 1
    48 
    49 # 连接数据库，数据库用户名root，密码root，数据库名myblog，编码格式utf8
    50 db = MySQLdb.connect("localhost", "root", "root", "myblog", charset="utf8")
    51 cursor = db.cursor()
    52 
    53 for bl in blogs:
    54     sub_sql = "'"+bl.title+"','"+bl.desc+"','"+bl.postDate+"','"+bl.status+"'"
    55     # 构造sql语句，插入数据
    56     sql = "insert into Blog(title,description,post_date,post_status) values("+sub_sql+")"
    57     try:
    58         cursor.execute(sql)
    59         db.commit()
    60     except:
    61         db.rollback()
    62 
    63 db.close()

主要的功能步骤已经在源码注释中标注了。接着运行程序，查看数据库内容如下则表示正确：

![](http://images2015.cnblogs.com/blog/852227/201611/852227-20161109095829795-407731584.png)

其中id值只要是自增长即可，可以不与上图对应。另外，如果APP需要点击条目跳转到博客内容，还需要把url获取下来，这里只是简单的事例就不拉了。



**③ 接口开发**

这个接口其实也很好理解，就是通过一个URL访问得到对应的数据，数据格式可以是JSON或者Xml，我们通过这些数据进行页面显示等等。而我们这里使用的是J2E中的Struts和Hibernate来搭建这个简单的后台。Struts用来拦截请求、Hibernate用于操作数据库。

实际上，Python也是可以做到的，但是目前国内很多中小企业都是用的J2E，所以......

环境搭建什么的就不说了，网上一搜一大堆，或者直接使用MyEclipse，快捷方便。

首先，编写对应于数据库的实体Bean：

    
    
     1 public class Blog implements java.io.Serializable {
     2 
     3     // Fields
     4 
     5     private Integer id;
     6     private String title;
     7     private String description;
     8     private String postDate;
     9     private String postStatus;
    10 
    11     // Constructors
    12 
    13     /** default constructor */
    14     public Blog() {
    15     }
    16 
    17     /** full constructor */
    18     public Blog(String title, String description, String postDate, String postStatus) {
    19         this.title = title;
    20         this.description = description;
    21         this.postDate = postDate;
    22         this.postStatus = postStatus;
    23     }
    24 
    25     // Property accessors
    26 
    27     public Integer getId() {
    28         return this.id;
    29     }
    30 
    31     public void setId(Integer id) {
    32         this.id = id;
    33     }
    34 
    35     public String getTitle() {
    36         return this.title;
    37     }
    38 
    39     public void setTitle(String title) {
    40         this.title = title;
    41     }
    42 
    43     public String getDescription() {
    44         return this.description;
    45     }
    46 
    47     public void setDescription(String description) {
    48         this.description = description;
    49     }
    50 
    51     public String getPostDate() {
    52         return this.postDate;
    53     }
    54 
    55     public void setPostDate(String postDate) {
    56         this.postDate = postDate;
    57     }
    58 
    59     public String getPostStatus() {
    60         return this.postStatus;
    61     }
    62 
    63     public void setPostStatus(String postStatus) {
    64         this.postStatus = postStatus;
    65     }
    66 
    67 }

接着在对应包中编写一个Blog.hbn.xml文件，用于Hibernate数据映射：

    
    
     1 <?xml version="1.0" encoding="utf-8"?>
     2 <!DOCTYPE hibernate-mapping PUBLIC "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
     3 "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">
     4 <hibernate-mapping>
     5     <class name="com.fndroid.entity.Blog" table="blog" catalog="myblog">
     6         <id name="id" type="java.lang.Integer">
     7             <column name="id" />
     8             <generator class="identity" />
     9         </id>
    10         <property name="title" type="java.lang.String">
    11             <column name="title" not-null="true" />
    12         </property>
    13         <property name="description" type="java.lang.String">
    14             <column name="description" not-null="true" />
    15         </property>
    16         <property name="postDate" type="java.lang.String">
    17             <column name="post_date" not-null="true" />
    18         </property>
    19         <property name="postStatus" type="java.lang.String">
    20             <column name="post_status" not-null="true" />
    21         </property>
    22     </class>
    23 </hibernate-mapping>

如果你使用MyEclipse，则这些文件可以通过自带的MyEclipse Hibernate工具生成。

接着，创建一个Dao来获取数据库内容：

    
    
     1 public class BlogDao {
     2 
     3     public List<Blog> getBlogs() {
     4         Configuration conf = new Configuration().configure();
     5         SessionFactory sessionFactory = conf.buildSessionFactory();
     6         Session session = sessionFactory.openSession();
     7         Query query = session.createQuery("from Blog");
     8         List<Blog> list = query.list();
     9         return list;
    10     }
    11 }

最后创建并配置一个Action来拦截请求，并填充数据，这里使用Gson来进行数据包装，所以要记得导入Gson的jar包：

    
    
     1 public class BooksAction extends ActionSupport {
     2     
     3     @Override
     4     public String execute() throws Exception {
     5         BlogDao dao = new BlogDao();
     6         List<Blog> blogs = dao.getBlogs();
     7         String result = createJsonString(!blogs.isEmpty(), blogs);
     8         HttpServletRequest request = ServletActionContext.getRequest();
     9         // 将数据填充至内置对象request中，这样在jsp中可以获取得到
    10         request.setAttribute("json", createJsonString(!blogs.isEmpty(), blogs));
    11         return "success";
    12     }
    13     
    14     /**
    15      * 通过数据集生成JSON格式的数据
    16      * @param res 数据集是否为空
    17      * @param blogs 数据集
    18      * @return
    19      */
    20     private String createJsonString(boolean res, List<Blog> blogs) {
    21         JsonObject resultJson = new JsonObject();
    22         JsonArray array = new JsonArray();
    23         resultJson.addProperty("result", res? 1:0);
    24         resultJson.addProperty("err_msg", res? "服务器成功返回数据":"服务器错误");
    25         if (res){
    26             for (Blog blog : blogs) {
    27                 JsonObject bObject = new JsonObject();
    28                 bObject.addProperty("id", blog.getId());
    29                 bObject.addProperty("title", blog.getTitle());
    30                 bObject.addProperty("desc", blog.getDescription());
    31                 bObject.addProperty("post_date", blog.getPostDate());
    32                 bObject.addProperty("status", blog.getPostStatus());
    33                 array.add(bObject);
    34             }
    35         }
    36         resultJson.add("blogs", array);
    37         return resultJson.toString();
    38     }
    39 }

配置Struts.xml：

    
    
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE struts PUBLIC "-//Apache Software Foundation//DTD Struts Configuration 2.1//EN" "http://struts.apache.org/dtds/struts-2.1.dtd">
    <struts>
        <package name="books" extends="struts-default">
            <action name="listBlogs" method="execute" class="com.fndroid.action.BooksAction">
                <result name="success">success.jsp</result>
            </action>
        </package>
    </struts>    

success.jsp

    
    
    <%@ page language="java" import="java.util.*" pageEncoding="utf-8"%>
    <%=request.getAttribute("json") %>

直接显示request内置对象中对应的json格式值即可。

接口已经编写完毕，接着启动服务器，并且布置项目，这个时候可以用浏览器访问http://localhost:8080/WebDemo/listBlogs来看是否成功，其中WebDemo为项目名称，listBlogs为Action名：

浏览器效果：

![](http://images2015.cnblogs.com/blog/852227/201611/852227-20161109102640952-344973908.png)

这肯定是不够直观的，所以我们可以尝试一下一些用于开发的请求分析工具，例如Postman（Chrome应用商店下载）：

![](http://images2015.cnblogs.com/blog/852227/201611/852227-20161109103000014-677244081.png)

这样就可以看到对应的格式。



**④ APP编写**

万事俱备，只欠东风了。APP的内容也不多，通过一个RecyclerView显示每个条目的标题、摘要和状态即可。

初始化使用空列表构造一个RecyclerView，接着通过RxJava和Retrofit进行网络请求，得到数据传递给数据列表并刷新界面。

注意：以下代码可能会出现令人身体不适的Lumbda表达式

主界面布局代码省略，里面只有一个RecyclerView。

编写RecyclerView每个Item的布局（使用数据绑定）：

    
    
     1 <?xml version="1.0" encoding="utf-8"?>
     2 <layout xmlns:android="http://schemas.android.com/apk/res/android">
     3 
     4     <data>
     5 
     6         <variable
     7             name="blog"
     8             type="com.fndroid.retrofitdemo.Blogs.BlogsBean"/>
     9     </data>
    10 
    11     <LinearLayout
    12         android:layout_marginTop="8dp"
    13         android:layout_marginBottom="8dp"
    14         android:layout_width="match_parent"
    15         android:layout_height="wrap_content"
    16         android:orientation="vertical">
    17 
    18         <TextView
    19             android:textSize="16sp"
    20             android:textAlignment="center"
    21             android:text="@{blog.title}"
    22             android:layout_width="match_parent"
    23             android:layout_height="wrap_content"/>
    24 
    25         <TextView
    26             android:textStyle="italic"
    27             android:text="@{blog.desc}"
    28             android:layout_width="match_parent"
    29             android:layout_height="wrap_content"/>
    30 
    31         <TextView
    32             android:text="@{blog.status}"
    33             android:layout_width="match_parent"
    34             android:layout_height="wrap_content"/>
    35 
    36     </LinearLayout>
    37 
    38 </layout>

创建Recycler的Adapter：

    
    
     1 public class MyAdapter extends RecyclerView.Adapter {
     2     private Context mContext;
     3     private ArrayList<Blogs.BlogsBean> mBlogsArrayList;
     4     public MyAdapter(Context context, ArrayList<Blogs.BlogsBean> blogsArrayList) {
     5         this.mContext = context;
     6         this.mBlogsArrayList = blogsArrayList;
     7     }
     8 
     9     @Override
    10     public RecyclerView.ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
    11         // 获取绑定实例，并存储在ViewHolder中
    12         ItemBlogBinding binding = DataBindingUtil.inflate(LayoutInflater.from(mContext), R.layout
    13                 .item_blog, parent, false);
    14         VH vh = new VH(binding.getRoot());
    15         vh.binding = binding;
    16         return vh;
    17     }
    18 
    19     @Override
    20     public void onBindViewHolder(RecyclerView.ViewHolder holder, int position) {
    21         Blogs.BlogsBean blogsBean = mBlogsArrayList.get(position);
    22         VH vh = (VH) holder;
    23         // 设置数据绑定数据源
    24         vh.binding.setVariable(com.fndroid.retrofitdemo.BR.blog, blogsBean);
    25     }
    26 
    27     @Override
    28     public int getItemCount() {
    29         return mBlogsArrayList.size();
    30     }
    31 
    32     class VH extends RecyclerView.ViewHolder{
    33         ItemBlogBinding binding;
    34         public VH(View itemView) {
    35             super(itemView);
    36         }
    37     }
    38 }

编写Retrofit的请求服务：

    
    
    public interface IdentifyService{
        @GET("listBlogs")
        public Observable<Blogs> getBlogs();
    }

最后编写Activity的内容：

    
    
     1 public class MainActivity extends AppCompatActivity {
     2     private static final String TAG = "MainActivity";
     3 
     4     // 这里不能写localhost，因为模拟器和服务器ip不同
     5     private static final String URL = "http:192.168.1.181:8080/WebDemo/";
     6     private ArrayList<Blogs.BlogsBean> mBlogsArrayList;
     7     private MyAdapter mMyAdapter;
     8 
     9     @BindView(R.id.main_rv)
    10     RecyclerView mRecyclerView;
    11 
    12     @Override
    13     protected void onCreate(Bundle savedInstanceState) {
    14         super.onCreate(savedInstanceState);
    15         setContentView(R.layout.activity_main);
    16         ButterKnife.bind(this);
    17 
    18         mRecyclerView.setLayoutManager(new LinearLayoutManager(this));
    19         // 传入空数据源
    20         mBlogsArrayList = new ArrayList<>();
    21         mMyAdapter = new MyAdapter(this, mBlogsArrayList);
    22         mRecyclerView.setAdapter(mMyAdapter);
    23 
    24         // 使用Gson解析数据，用RxJava2封装请求
    25         Retrofit ret = new Retrofit.Builder().baseUrl(URL).addConverterFactory
    26                 (GsonConverterFactory.create()).addCallAdapterFactory(RxJava2CallAdapterFactory
    27                 .create()).build();
    28         IdentifyService identifyService = ret.create(IdentifyService.class);
    29         Observable<Blogs> blogs = identifyService.getBlogs();
    30         blogs.subscribeOn(Schedulers.newThread()).observeOn(AndroidSchedulers.mainThread())
    31                 .subscribe(b -> {
    32                     mBlogsArrayList.addAll(b.getBlogs());
    33                     mMyAdapter.notifyDataSetChanged();
    34                 });
    35     }
    36 }

因为使用了各种框架，所以内容也很简单，毕竟都2016年了，谁不用框架对吗。

这里对使用的各种框架做一个简单的说明：

  * RxJava2：异步请求必须要掌握的
  * Retrofit：它聪明的提供了Gson、RxJava2等支持，底层也是基于okhttp，所以性能也较好，也是必须掌握的
  * databinding（数据绑定）：官方出品，免去setText、findViewById等冗余代码
  * RetroLumbda：在Java7上提供Lumbda语言支持，毕竟官方默认1.7，改为1.8会导致Instant Run失效，所以你懂的
  * Butterknife：不多说了吧这个



[源码地址](https://github.com/Fndroid/BlogDemo)

