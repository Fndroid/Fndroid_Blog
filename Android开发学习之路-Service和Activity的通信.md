# Android开发学习之路-Service和Activity的通信

在很多时候，Service都不仅仅需要在后台运行，还需要和Activity进行通信，或者接受Activity的指挥，如何来实现，来看代码。

定义一个服务

    
    
     1 // 创建一个服务，然后在onBind()中返回内部类（继承自Binder）的实例，使得活动能获得该实例，并操作此服务
     2 public class MyService extends Service {
     3 
     4     // 创建一个内部类的实例，被活动获得后，操作服务
     5     private mBinder binder = new mBinder();
     6     
     7     // 创建一个内部类，继承自Binder，需要活动操作的函数都定义在这个内部类中
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
    39         // 在绑定时返回这个内部类实例，在活动的onServiceConnected()函数中，能获得此实例
    40         return binder;
    41     }
    42 
    43 }



主活动中定义两个按钮，分别用于绑定和解绑活动。

    
    
     1 public class MainActivity extends Activity implements OnClickListener {
     2     private Button bind;
     3     private Button unbind;
     4     private MyService.mBinder binder;
     5 
     6     private ServiceConnection connection = new ServiceConnection() {
     7 
     8         @Override
     9         // 活动和服务的绑定失去的时候自动调用，比如服务被杀死或者报错，这时候ServiceConnection会保持激活，在活动下次启动时会收到一个通知
    10         public void onServiceDisconnected(ComponentName name) {
    11             Log.d("MainActivity", "onServiceDisonnected executed");
    12         }
    13 
    14         @Override
    15         // 活动和服务解绑的时候自动调用，即bindService()函数调用时
    16         public void onServiceConnected(ComponentName name, IBinder service) {
    17             binder = (MyService.mBinder) service; // 通过Service向下转型得到一个MyService.mBinder的对象
    18             binder.Function1(); // 执行Service里面的函数，即指挥Service操作
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
    39             // 传入服务的Intent，和已经定义好的ServiceConnection对象，BIND_AUTO_CREATE为标志，表示活动和服务绑定后自动创建一个服务的实例
    40             bindService(intent, connection, BIND_AUTO_CREATE);
    41             break;
    42         case R.id.bt_unbind:
    43             // 解绑
    44             unbindService(connection);
    45             break;
    46         default:
    47             break;
    48         }
    49     }
    50 
    51 }



在AndroidManifest.xml中注册这个活动

    
    
    <service android:name=".MyService"></service>
    



运行结果：

![](http://images2015.cnblogs.com/blog/852227/201601/852227-20160103110015745-2188442.png)

![](http://images2015.cnblogs.com/blog/852227/201601/852227-20160103110032807-1747832169.png)



通信的原理：执行绑定操作的时候，传入服务的Intent，标志位BIND_AUTO_CREATE之后，服务会被创建，并且会执行onBind()方法，返回了内部类的实例，接下来，onServiceConnected()方法会被执行，这个方法的参数IBinder即为我们在服务的onBind()方法返回的实例，对其向下转型后就能得到一个服务内部类的实例，实例可以调用类中的方法，达到在活动中操作服务的效果。执行解绑操作后，服务的onDestroy方法先被执行，onServiceDisconnected()方法不会执行，因为此方法只有在服务被杀死或者Crash的时候执行，并在下次绑定上的时候发送一个报告信息给活动。

