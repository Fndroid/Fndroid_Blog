# ѧϰ�ռ�-�����浽�ӿڵ�APP

������ڸ�ϰJ2E����ѧϰһЩ�����϶��Ǻõģ����������ƶ������������������ˣ��й������Ƽ�һ��С���£����ݷ�ɽ������лл�ˡ�

��ƪ����Ҫ����Ч���ܼ򵥣����ǰ��Ҳ��͵ĵ�һҳÿ����Ŀ��ʾ��APP�ϣ���Ŀ�������⡢ժҪ��״̬����ͼ��

![](http://images2015.cnblogs.com/blog/852227/201611/852227-20161109092419920-1780857662.png)

������ƪ���ͽ����漰��

  1. ���ݿ⣨MySql������ƣ������������ݣ�
  2. �����棨��Python��ȡ��ҳ���ݣ�д�����ݿ⣩
  3. �򵥽ӿڿ�����Struts��Hibernate��
  4. APP��������Retrofit��Gson��RxJava�ȣ�

**��������̾��ǣ��ȴ������ݿ⣬ͨ�������ֶ���ȡ������ҳ����Ŀ���ݲ���������ݿ⣬���Ŵ�򵥵�JavaWeb��̨���ṩ�ӿڷ��ʣ�ͨ���������󷵻����ݿ��е����ݡ�**



**�� ���ݿ����**

Ҫ��ȡ���ݺͽӿڿ������϶�������Ҫ�ȴ������ݿ�����ݱ�ġ�����ʹ�õ���MySql�������Ĺ�����Navicat��������������ݣ�������Ҫ������Ӧ�ı�

![](http://images2015.cnblogs.com/blog/852227/201611/852227-20161109094609530-757856275.png)

����idΪ��������������������Ͽ��Խ��в����ɾ���Ȳ��ԡ�



**�� ��ȡ��ҳ����**

��̬��ҳ����ȡ�ǱȽϼ򵥵ģ���ʵ���Ǹ�����ҳԴ����н���ƥ�䣬��Python��������ʽ��Ϊǿ����������ʹ��Python�����в��������⣬����������Ҳ����ʹ��һЩ�����򻯲�����������õ�request��bs4������request�����������󣬶�bs4�������ڽ�����ҳԴ��õ�������Ҫ�����ݡ����ͨ��MySQLdb�����ݽ��д洢��

�ȷ�����ҳԴ�룬����ʹ��Chrome���۲�ṹ��

![](http://images2015.cnblogs.com/blog/852227/201611/852227-20161109095313233-1162476311.png)

�õ��ṹ֮��Ϳ��Խ��б��룺

�ļ�����MySpider.py

    
    
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
    11 # ����һ��������
    12 class Blog:
    13     title = ""
    14     desc = ""
    15     postDate = ""
    16     status = ""
    17 
    18 # ��������������ȡԴ�룬��ַΪ�Ҳ�����ҳ
    19 response = requests.get("http://www.cnblogs.com/Fndroid/")
    20 # ʹ��BeautifulSoup���д���
    21 soup = BeautifulSoup(response.text, "html.parser")
    22 
    23 blogs = []
    24 # ����Դ���ʽ��ȡ��Ŀ���ҵ�classΪday�ı�ǩ����ȡ����������div
    25 for day in soup.findAll(class_='day'):
    26     divs = day.findAll("div")
    27     n = 0
    28     b = Blog()
    29     for div in divs:
    30         if n == 0:
    31             # ��ȡ����ʱ��
    32             b.postDate = div.a.string
    33         elif n == 1:
    34             # ��ȡ����
    35             b.title = div.a.string
    36         elif n == 2:
    37             # ��ȡժҪ
    38             b.desc = div.div.contents[0]
    39         elif n == 5:
    40             # ��ȡ����״̬
    41             b.status = div.contents[0]
    42         elif n == 6:
    43             n = 0
    44             blogs.append(b)
    45             break
    46 
    47         n += 1
    48 
    49 # �������ݿ⣬���ݿ��û���root������root�����ݿ���myblog�������ʽutf8
    50 db = MySQLdb.connect("localhost", "root", "root", "myblog", charset="utf8")
    51 cursor = db.cursor()
    52 
    53 for bl in blogs:
    54     sub_sql = "'"+bl.title+"','"+bl.desc+"','"+bl.postDate+"','"+bl.status+"'"
    55     # ����sql��䣬��������
    56     sql = "insert into Blog(title,description,post_date,post_status) values("+sub_sql+")"
    57     try:
    58         cursor.execute(sql)
    59         db.commit()
    60     except:
    61         db.rollback()
    62 
    63 db.close()

��Ҫ�Ĺ��ܲ����Ѿ���Դ��ע���б�ע�ˡ��������г��򣬲鿴���ݿ������������ʾ��ȷ��

![](http://images2015.cnblogs.com/blog/852227/201611/852227-20161109095829795-407731584.png)

����idֵֻҪ�����������ɣ����Բ�����ͼ��Ӧ�����⣬���APP��Ҫ�����Ŀ��ת���������ݣ�����Ҫ��url��ȡ����������ֻ�Ǽ򵥵������Ͳ����ˡ�



**�� �ӿڿ���**

����ӿ���ʵҲ�ܺ���⣬����ͨ��һ��URL���ʵõ���Ӧ�����ݣ����ݸ�ʽ������JSON����Xml������ͨ����Щ���ݽ���ҳ����ʾ�ȵȡ�����������ʹ�õ���J2E�е�Struts��Hibernate�������򵥵ĺ�̨��Struts������������Hibernate���ڲ������ݿ⡣

ʵ���ϣ�PythonҲ�ǿ��������ģ�����Ŀǰ���ںܶ���С��ҵ�����õ�J2E������......

�����ʲô�ľͲ�˵�ˣ�����һ��һ��ѣ�����ֱ��ʹ��MyEclipse����ݷ��㡣

���ȣ���д��Ӧ�����ݿ��ʵ��Bean��

    
    
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

�����ڶ�Ӧ���б�дһ��Blog.hbn.xml�ļ�������Hibernate����ӳ�䣺

    
    
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

�����ʹ��MyEclipse������Щ�ļ�����ͨ���Դ���MyEclipse Hibernate�������ɡ�

���ţ�����һ��Dao����ȡ���ݿ����ݣ�

    
    
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

��󴴽�������һ��Action���������󣬲�������ݣ�����ʹ��Gson���������ݰ�װ������Ҫ�ǵõ���Gson��jar����

    
    
     1 public class BooksAction extends ActionSupport {
     2     
     3     @Override
     4     public String execute() throws Exception {
     5         BlogDao dao = new BlogDao();
     6         List<Blog> blogs = dao.getBlogs();
     7         String result = createJsonString(!blogs.isEmpty(), blogs);
     8         HttpServletRequest request = ServletActionContext.getRequest();
     9         // ��������������ö���request�У�������jsp�п��Ի�ȡ�õ�
    10         request.setAttribute("json", createJsonString(!blogs.isEmpty(), blogs));
    11         return "success";
    12     }
    13     
    14     /**
    15      * ͨ�����ݼ�����JSON��ʽ������
    16      * @param res ���ݼ��Ƿ�Ϊ��
    17      * @param blogs ���ݼ�
    18      * @return
    19      */
    20     private String createJsonString(boolean res, List<Blog> blogs) {
    21         JsonObject resultJson = new JsonObject();
    22         JsonArray array = new JsonArray();
    23         resultJson.addProperty("result", res? 1:0);
    24         resultJson.addProperty("err_msg", res? "�������ɹ���������":"����������");
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

����Struts.xml��

    
    
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

ֱ����ʾrequest���ö����ж�Ӧ��json��ʽֵ���ɡ�

�ӿ��Ѿ���д��ϣ��������������������Ҳ�����Ŀ�����ʱ����������������http://localhost:8080/WebDemo/listBlogs�����Ƿ�ɹ�������WebDemoΪ��Ŀ���ƣ�listBlogsΪAction����

�����Ч����

![](http://images2015.cnblogs.com/blog/852227/201611/852227-20161109102640952-344973908.png)

��϶��ǲ���ֱ�۵ģ��������ǿ��Գ���һ��һЩ���ڿ���������������ߣ�����Postman��ChromeӦ���̵����أ���

![](http://images2015.cnblogs.com/blog/852227/201611/852227-20161109103000014-677244081.png)

�����Ϳ��Կ�����Ӧ�ĸ�ʽ��



**�� APP��д**

���¾㱸��ֻǷ�����ˡ�APP������Ҳ���࣬ͨ��һ��RecyclerView��ʾÿ����Ŀ�ı��⡢ժҪ��״̬���ɡ�

��ʼ��ʹ�ÿ��б���һ��RecyclerView������ͨ��RxJava��Retrofit�����������󣬵õ����ݴ��ݸ������б�ˢ�½��档

ע�⣺���´�����ܻ�����������岻�ʵ�Lumbda���ʽ

�����沼�ִ���ʡ�ԣ�����ֻ��һ��RecyclerView��

��дRecyclerViewÿ��Item�Ĳ��֣�ʹ�����ݰ󶨣���

    
    
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

����Recycler��Adapter��

    
    
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
    11         // ��ȡ��ʵ�������洢��ViewHolder��
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
    23         // �������ݰ�����Դ
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

��дRetrofit���������

    
    
    public interface IdentifyService{
        @GET("listBlogs")
        public Observable<Blogs> getBlogs();
    }

����дActivity�����ݣ�

    
    
     1 public class MainActivity extends AppCompatActivity {
     2     private static final String TAG = "MainActivity";
     3 
     4     // ���ﲻ��дlocalhost����Ϊģ�����ͷ�����ip��ͬ
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
    19         // ���������Դ
    20         mBlogsArrayList = new ArrayList<>();
    21         mMyAdapter = new MyAdapter(this, mBlogsArrayList);
    22         mRecyclerView.setAdapter(mMyAdapter);
    23 
    24         // ʹ��Gson�������ݣ���RxJava2��װ����
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

��Ϊʹ���˸��ֿ�ܣ���������Ҳ�ܼ򵥣��Ͼ���2016���ˣ�˭���ÿ�ܶ���

�����ʹ�õĸ��ֿ����һ���򵥵�˵����

  * RxJava2���첽�������Ҫ���յ�
  * Retrofit�����������ṩ��Gson��RxJava2��֧�֣��ײ�Ҳ�ǻ���okhttp����������Ҳ�Ϻã�Ҳ�Ǳ������յ�
  * databinding�����ݰ󶨣����ٷ���Ʒ����ȥsetText��findViewById���������
  * RetroLumbda����Java7���ṩLumbda����֧�֣��Ͼ��ٷ�Ĭ��1.7����Ϊ1.8�ᵼ��Instant RunʧЧ�������㶮��
  * Butterknife������˵�˰����



[Դ���ַ](https://github.com/Fndroid/BlogDemo)

