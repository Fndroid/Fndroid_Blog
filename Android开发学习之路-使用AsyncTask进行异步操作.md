# Android����ѧϰ֮·-ʹ��AsyncTask�����첽����

ͨ������£�����Ҫʵ���첽������Ҳ���������߳̽��к�ʱ�����������ػ��߼���ͼƬ�ȣ�Ȼ����UI�������߳��и���UI��ʹ�õ���Handler��Message�������첽��ʵ�֣����ǣ��ȸ�ٷ���Androidϵͳ��ʵ�����ַ��������˷�װ�������AsyncTask��

Handler��Message��ʹ�ã�http://www.cnblogs.com/Fndroid/p/5098405.html

ʹ��AsyncTask������Ҫ�½�һ���࣬�����̳���AsyncTask�����ƶ�����������͡��������������Լ�����ֵ���ͣ���Щ���;��Ƿ���

����������Ҫ��дһЩ���������õ������¼�����

onPreExecute()��������������첽���ؽ���ǰִ�еģ�ͨ��������ʾһ����Ϣ�������ʾ

doInBackground()����������������߳��н��еģ��������ػ��߼��صȺ�ʱ�Ĳ�����������������ִ��

onProgressUpdate()�������������doInBackground������ִ����publishProgress()������ʱ��ִ�У�publishProgress()��������˷�������һЩ�����������̵Ĳ�����������Integer���������صĽ��ȵ�

onPostExecute()�������������doInBackground()����ִ�����return֮��ִ�У�����return���������ж�Ӧ����θ���UI

���ϵķ����У�ֻ��doInBackground()��������������߳���ִ�У�����������UI�̣߳����Կ���ֱ�Ӷ�UI���и��¡�

��������ģ��һ�����أ�������ɺ����ListView���������ʧ�ܾ�Toastһ��

    
    
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
    15         progressDialog = new ProgressDialog(this); // ����һ��ProgressDialog����ʾ������Ϣ
    16         progressDialog.setTitle("������������");
    17         progressDialog.setMessage("������...");
    18         progressDialog.setCancelable(true);
    19         start.setOnClickListener(new OnClickListener() {
    20             @Override
    21             public void onClick(View v) {
    22                 new DownLoad().execute();
    23             }
    24         });
    25     }
    26 
    27     // ���ﶨ���һ��AsyncTask���࣬�����������Ϊ�գ�����ָʾ����Ϊ���ͣ��첽��������Ϊ����
    28     class DownLoad extends AsyncTask<Void, Integer, Boolean> {
    29         @Override
    30         protected void onPreExecute() {
    31             progressDialog.show(); // �ڿ�ʼ�첽����ǰ���Ȱ�ProgressDialog��ʾ��������ʾ��������
    32         }
    33 
    34         @Override
    35         protected Boolean doInBackground(Void... params) {
    36             try {
    37                 for (int i = 1; i <= 5; i++) { // ÿ500����ִ��һ��publishProgress�����Ѳ�������ȥ
    38                     Thread.sleep(500);
    39                     publishProgress(i * 20);
    40                 }
    41             } catch (InterruptedException e) {
    42                 e.printStackTrace();
    43             }
    44             Random rd = new Random();
    45             int randomint = rd.nextInt(10);
    46             if (randomint % 2 == 0) { // ����һ��������������ż����return true��ʾ���سɹ���������ʾʧ��
    47                 return true;
    48             } else {
    49                 return false;
    50             }
    51         }
    52 
    53         @Override
    54         protected void onProgressUpdate(Integer... values) {
    55             Log.e("AsyncTask", "progress=" + values[0] + "%"); // ��publishProgressִ�к󴥷���������һ����ֵ������publishProgress�������Ĳ���
    56         }
    57 
    58         @Override
    59         protected void onPostExecute(Boolean result) {
    60             progressDialog.dismiss();
    61             // �ж��Ƿ����سɹ����ɹ������ListView��ʧ����Toastһ��
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

��ʾ��ͼ��

![](http://images2015.cnblogs.com/blog/852227/201601/852227-20160130110958005-1529619524.png)
![](http://images2015.cnblogs.com/blog/852227/201601/852227-20160130111009974-102307006.png)

Log��

![](http://images2015.cnblogs.com/blog/852227/201601/852227-20160130111203802-1172466959.png)

���Կ���ʱ����ÿ��500���룬onProgressUpdate()�����ͻ�ִ��һ�Ρ�

