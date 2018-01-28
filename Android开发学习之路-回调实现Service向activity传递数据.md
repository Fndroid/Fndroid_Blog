# Android����ѧϰ֮·-�ص�ʵ��Service��activity��������

���������ʱ�����������ͨ��bindService���󶨷�����Ҫ����񴫵����ݣ�����ֱ����Intent������bundle���ﵽЧ�������������������Ҫ�ӷ����з���һЩ���ݵ�Activity�е�ʱ��ʵ���������и��ָ����ķ���������˵ʹ�ûص���ʹ�ù㲥�ȵȣ�����˵����ʹ�ûص��ķ�����

�½�һ�����̣�����дһ������

    
    
     1 public class MyService extends Service {
     2     private boolean connecting = false;
     3     private Callback callback;
     4 
     5     @Nullable
     6     @Override
     7     public IBinder onBind(Intent intent) {
     8         return new Binder();
     9     }
    10 
    11     public class Binder extends android.os.Binder {
    12         public MyService getService() {
    13             return MyService.this;
    14         }
    15     }
    16 
    17     @Override
    18     public void onCreate() {
    19         super.onCreate();
    20         connecting = true;
    21         new Thread(new Runnable() {
    22 
    23             @Override
    24             public void run() {
    25                 int i = 0;
    26                 while (connecting == true) {
    27                     i++;
    28                     if (callback != null) {
    29                         callback.onDataChange(i + "");
    30                     }
    31                     try {
    32                         Thread.sleep(1000);
    33                     } catch (InterruptedException e) {
    34                         e.printStackTrace();
    35                     }
    36                 }
    37             }
    38         }).start();
    39     }
    40 
    41     public void setCallback(Callback callback) {
    42         this.callback = callback;
    43     }
    44 
    45     public static interface Callback {
    46         void onDataChange(String data);
    47     }
    48 
    49     @Override
    50     public void onDestroy() {
    51         super.onDestroy();
    52         connecting = false;
    53     }
    54 }

�ڷ����е�onCreate�����У����Ǵ���һ���߳���ģ���������У������߳�ÿ��1s�и�˽�б���i��ֵ������Ȼ�����Ǳ�д��һ�����еĽӿ�Callback�����Ҷ�����һ���ýӿڵ�˽�г�Ա��������onCreate�����е����˽ӿ�����ĺ���onDataChange�������������Զ�����һ��Binder�����ಢ��������ж����˺������ص�ǰ�����Service�������Ŀ�ľ�����Activity�п��Է��ʵ����Service�Ļص��ӿ�Callback��ʵ�ָýӿڵķ�����

Activity�������£�

    
    
     1 public class MainActivity extends AppCompatActivity implements View.OnClickListener,
     2         ServiceConnection {
     3 
     4     private TextView tvOut;
     5 
     6     @Override
     7     protected void onCreate(Bundle savedInstanceState) {
     8         super.onCreate(savedInstanceState);
     9         setContentView(R.layout.activity_main);
    10         tvOut = (TextView) findViewById(R.id.tvOut);
    11         findViewById(R.id.btnBindService).setOnClickListener(this);
    12     }
    13 
    14     @Override
    15     public void onClick(View v) {
    16         bindService(new Intent(this, MyService.class), this, BIND_AUTO_CREATE);
    17     }
    18 
    19     @Override
    20     public void onServiceConnected(ComponentName name, IBinder service) {
    21         MyService.Binder binder = (MyService.Binder) service;
    22         MyService myService = binder.getService();
    23         myService.setCallback(new MyService.Callback() {
    24             @Override
    25             public void onDataChange(String data) {
    26                 Message msg = new Message();
    27                 msg.obj = data;
    28                 handler.sendMessage(msg);
    29             }
    30         });
    31     }
    32 
    33     @Override
    34     public void onServiceDisconnected(ComponentName name) {
    35 
    36     }
    37 
    38     private Handler handler = new Handler() {
    39         @Override
    40         public void handleMessage(Message msg) {
    41             super.handleMessage(msg);
    42             tvOut.setText(msg.obj.toString());
    43         }
    44     };
    45 }

��Ϊ����󶨺󣬻��onBind�����з���һ��Binder��������������onServiceConnectde�����л�ȡ�������������ȴ�Binder�����л�ȡ�����Ǵӷ��񴫵ݹ�����MyService����Ȼ�����MyService�����setCallback����������������Ҫ�Ĵ����߼��������ǰ�i��ֵ��ӡ��������Ϊ�����п������̣߳���������Ҳ����ֱ�Ӹ���UI��



�ܽ᣺�ص�������Java�е�һ����Ҫ���ԣ���Android��ʹ�õ��ĵط��ܹ㷺���������Ǹ���ť�趨����¼��ȡ�����Ļص�����ʵ��ͨ���ڷ��Ͷ˶���ص��ӿڣ����ҵ��ýӿڵĻص�������Ȼ���ڽ��ն�ʵ�ָýӿڵķ�����ֻҪ�ӿڱ������ˣ��ͻ�ص����ն˵ı�ʵ���˵ķ������������ݾ��ܴ��ݹ�����



ע�⣺�����ķ����������߳������еģ�����ڷ����п������̣߳���ô�����߳��оͲ���ֱ�Ӹ���UI



