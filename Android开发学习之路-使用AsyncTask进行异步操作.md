# Android开发学习之路-使用AsyncTask进行异步操作

通常情况下，我们要实现异步操作，也就是在子线程进行耗时操作比如下载或者加载图片等，然后在UI（主）线程中更新UI，使用的是Handler和Message来进行异步的实现，但是，谷歌官方在Android系统其实对这种方法进行了封装，这就是AsyncTask。

Handler和Message的使用：http://www.cnblogs.com/Fndroid/p/5098405.html

使用AsyncTask，必须要新建一个类，这个类继承自AsyncTask并且制定传入参数类型、描述过程类型以及返回值类型，这些类型均是泛型

接下来，需要重写一些方法，常用的有以下几个：

onPreExecute()：这个方法是在异步加载进行前执行的，通常用来显示一个消息框后者提示

doInBackground()：这个方法是在子线程中进行的，所有下载或者加载等耗时的操作，都可以在这里执行

onProgressUpdate()：这个方法是在doInBackground方法中执行了publishProgress()方法的时候执行，publishProgress()方法是向此方法传递一些用于描述过程的参数，比如用Integer来描述下载的进度等

onPostExecute()：这个方法是在doInBackground()方法执行完毕return之后执行，根据return的类型来判断应该如何更新UI

以上的方法中，只有doInBackground()这个方法是在子线程中执行，其他都是在UI线程，所以可以直接对UI进行更新。

这里我们模拟一个下载，下载完成后更新ListView，如果下载失败就Toast一下

    
    
     1 public class MainActivity extends Activity {
     2 
     3     private Button start;
     4     private ProgressDialog progressDialog;
     5     private ListView mListView;
     6     private SimpleAdapter mAdapter;
     7     private List<Map<String, Object>> mList;
     8 
     9     @Override
    10     protected void onCreate(Bundle savedInstanceState) {
    11         super.onCreate(savedInstanceState);
    12         setContentView(R.layout.activity_main);
    13         start = (Button) findViewById(R.id.start);
    14         mListView = (ListView) findViewById(R.id.listview);
    15         progressDialog = new ProgressDialog(this); // 构建一个ProgressDialog来提示下载信息
    16         progressDialog.setTitle("正在下载数据");
    17         progressDialog.setMessage("下载中...");
    18         progressDialog.setCancelable(true);
    19         start.setOnClickListener(new OnClickListener() {
    20             @Override
    21             public void onClick(View v) {
    22                 new DownLoad().execute();
    23             }
    24         });
    25     }
    26 
    27     // 这里定义的一个AsyncTask子类，输入参数类型为空，过程指示参数为整型，异步返回类型为布尔
    28     class DownLoad extends AsyncTask<Void, Integer, Boolean> {
    29         @Override
    30         protected void onPreExecute() {
    31             progressDialog.show(); // 在开始异步下载前，先把ProgressDialog显示出来，提示正在下载
    32         }
    33 
    34         @Override
    35         protected Boolean doInBackground(Void... params) {
    36             try {
    37                 for (int i = 1; i <= 5; i++) { // 每500毫秒执行一次publishProgress，并把参数穿进去
    38                     Thread.sleep(500);
    39                     publishProgress(i * 20);
    40                 }
    41             } catch (InterruptedException e) {
    42                 e.printStackTrace();
    43             }
    44             Random rd = new Random();
    45             int randomint = rd.nextInt(10);
    46             if (randomint % 2 == 0) { // 生成一个随机数，如果是偶数则return true表示下载成功，奇数表示失败
    47                 return true;
    48             } else {
    49                 return false;
    50             }
    51         }
    52 
    53         @Override
    54         protected void onProgressUpdate(Integer... values) {
    55             Log.e("AsyncTask", "progress=" + values[0] + "%"); // 在publishProgress执行后触发，参数的一个数值就是在publishProgress传过来的参数
    56         }
    57 
    58         @Override
    59         protected void onPostExecute(Boolean result) {
    60             progressDialog.dismiss();
    61             // 判断是否下载成功，成功则更新ListView，失败则Toast一下
    62             if (result) {
    63                 mList = new ArrayList<Map<String, Object>>();
    64                 for (int i = 0; i < 50; i++) {
    65                     Map<String, Object> map = new HashMap<String, Object>();
    66                     map.put("string", "hello");
    67                     mList.add(map);
    68                 }
    69                 mAdapter = new SimpleAdapter(getApplicationContext(), mList,
    70                         android.R.layout.simple_list_item_1,
    71                         new String[] { "string" },
    72                         new int[] { android.R.id.text1 });
    73                 mListView.setAdapter(mAdapter);
    74             } else {
    75                 Toast.makeText(getApplicationContext(), "download error!",
    76                         Toast.LENGTH_SHORT).show();
    77             }
    78         }
    79 
    80     }
    81 
    82 }

显示截图：

![](http://images2015.cnblogs.com/blog/852227/201601/852227-20160130110958005-1529619524.png)
![](http://images2015.cnblogs.com/blog/852227/201601/852227-20160130111009974-102307006.png)

Log：

![](http://images2015.cnblogs.com/blog/852227/201601/852227-20160130111203802-1172466959.png)

可以看到时间是每隔500毫秒，onProgressUpdate()函数就会执行一次。

