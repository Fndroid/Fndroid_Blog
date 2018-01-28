# Android����ѧϰ֮·-Service��Activity��ͨ��

�ںܶ�ʱ��Service����������Ҫ�ں�̨���У�����Ҫ��Activity����ͨ�ţ����߽���Activity��ָ�ӣ������ʵ�֣��������롣

����һ������

    
    
     1 // ����һ������Ȼ����onBind()�з����ڲ��ࣨ�̳���Binder����ʵ����ʹ�û�ܻ�ø�ʵ�����������˷���
     2 public class MyService extends Service {
     3 
     4     // ����һ���ڲ����ʵ���������ú󣬲�������
     5     private mBinder binder = new mBinder();
     6     
     7     // ����һ���ڲ��࣬�̳���Binder����Ҫ������ĺ���������������ڲ�����
     8     class mBinder extends Binder {
     9         public void Function1() {
    10             Log.d("MyService.mBinder", "Function1 executed");
    11         }
    12 
    13         public void Function2() {
    14             Log.d("MyService.mBinder", "Function2 executed");
    15         }
    16 
    17     }
    18     @Override
    19     public void onCreate() {
    20         super.onCreate();
    21         Log.d("MyService", "onCreate executed");
    22     }
    23     
    24     @Override
    25     public int onStartCommand(Intent intent, int flags, int startId) {
    26         Log.d("MyService", "onStartCommand executed");
    27         return super.onStartCommand(intent, flags, startId);
    28     }
    29      
    30     @Override
    31     public void onDestroy() {
    32         super.onDestroy();
    33         Log.d("MyService", "onDestroy executed");
    34     }
    35 
    36     @Override
    37     public IBinder onBind(Intent intent) {
    38         Log.d("MyService", "onBind executed");
    39         // �ڰ�ʱ��������ڲ���ʵ�����ڻ��onServiceConnected()�����У��ܻ�ô�ʵ��
    40         return binder;
    41     }
    42 
    43 }



����ж���������ť���ֱ����ڰ󶨺ͽ����

    
    
     1 public class MainActivity extends Activity implements OnClickListener {
     2     private Button bind;
     3     private Button unbind;
     4     private MyService.mBinder binder;
     5 
     6     private ServiceConnection connection = new ServiceConnection() {
     7 
     8         @Override
     9         // ��ͷ���İ�ʧȥ��ʱ���Զ����ã��������ɱ�����߱�����ʱ��ServiceConnection�ᱣ�ּ���ڻ�´�����ʱ���յ�һ��֪ͨ
    10         public void onServiceDisconnected(ComponentName name) {
    11             Log.d("MainActivity", "onServiceDisonnected executed");
    12         }
    13 
    14         @Override
    15         // ��ͷ������ʱ���Զ����ã���bindService()��������ʱ
    16         public void onServiceConnected(ComponentName name, IBinder service) {
    17             binder = (MyService.mBinder) service; // ͨ��Service����ת�͵õ�һ��MyService.mBinder�Ķ���
    18             binder.Function1(); // ִ��Service����ĺ�������ָ��Service����
    19             binder.Function2();
    20             Log.d("MainActivity", "onServiceConnected executed");
    21         }
    22     };
    23 
    24     @Override
    25     protected void onCreate(Bundle savedInstanceState) {
    26         super.onCreate(savedInstanceState);
    27         setContentView(R.layout.activity_main);
    28         bind = (Button) findViewById(R.id.bt_bind);
    29         unbind = (Button) findViewById(R.id.bt_unbind);
    30         bind.setOnClickListener(this);
    31         unbind.setOnClickListener(this);
    32     }
    33 
    34     @Override
    35     public void onClick(View v) {
    36         switch (v.getId()) {
    37         case R.id.bt_bind:
    38             Intent intent = new Intent(this, MyService.class);
    39             // ��������Intent�����Ѿ�����õ�ServiceConnection����BIND_AUTO_CREATEΪ��־����ʾ��ͷ���󶨺��Զ�����һ�������ʵ��
    40             bindService(intent, connection, BIND_AUTO_CREATE);
    41             break;
    42         case R.id.bt_unbind:
    43             // ���
    44             unbindService(connection);
    45             break;
    46         default:
    47             break;
    48         }
    49     }
    50 
    51 }



��AndroidManifest.xml��ע������

    
    
    <service android:name=".MyService"></service>
    



���н����

![](http://images2015.cnblogs.com/blog/852227/201601/852227-20160103110015745-2188442.png)

![](http://images2015.cnblogs.com/blog/852227/201601/852227-20160103110032807-1747832169.png)



ͨ�ŵ�ԭ��ִ�а󶨲�����ʱ�򣬴�������Intent����־λBIND_AUTO_CREATE֮�󣬷���ᱻ���������һ�ִ��onBind()�������������ڲ����ʵ������������onServiceConnected()�����ᱻִ�У���������Ĳ���IBinder��Ϊ�����ڷ����onBind()�������ص�ʵ������������ת�ͺ���ܵõ�һ�������ڲ����ʵ����ʵ�����Ե������еķ������ﵽ�ڻ�в��������Ч����ִ�н������󣬷����onDestroy�����ȱ�ִ�У�onServiceDisconnected()��������ִ�У���Ϊ�˷���ֻ���ڷ���ɱ������Crash��ʱ��ִ�У������´ΰ��ϵ�ʱ����һ��������Ϣ�����

