# Android开发学习之路-回调实现Service向activity传递数据

开启服务的时候，如果我们是通过bindService来绑定服务并且要向服务传递数据，可以直接在Intent中设置bundle来达到效果，但是如果是我们需要从服务中返回一些数据到Activity中的时候，实现起来就有各种各样的方法，比如说使用回调，使用广播等等，今天说的是使用回调的方法。

新建一个工程，并编写一个服务：

    
    
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

在服务中的onCreate方法中，我们打开了一个线程来模拟服务的运行，并在线程每隔1s中给私有变量i赋值递增，然后我们编写了一个公有的接口Callback，并且定义了一个该接口的私有成员，并且在onCreate方法中调用了接口里面的函数onDataChange。接下来我们自定义了一个Binder的子类并在这个类中定义了函数返回当前的这个Service，这里的目的就是在Activity中可以访问到这个Service的回调接口Callback并实现该接口的方法。

Activity代码如下：

    
    
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

因为服务绑定后，会从onBind方法中返回一个Binder对象，这个对象会在onServiceConnectde方法中获取到，所以我们先从Binder对象中获取到我们从服务传递过来的MyService对象，然后调用MyService对象的setCallback方法来设置我们需要的处理逻辑，这里是把i的值打印出来，因为服务中开启了线程，所以这里也不能直接更新UI。



总结：回调机制是Java中的一个重要特性，在Android中使用到的地方很广泛，例如我们给按钮设定点击事件等。这里的回调，其实是通过在发送端定义回调接口，并且调用接口的回调方法，然后在接收端实现该接口的方法。只要接口被调用了，就会回调接收端的被实现了的方法，这样数据就能传递过来。



注意：开启的服务是在主线程中运行的，如果在服务中开启了线程，那么在子线程中就不能直接更新UI



