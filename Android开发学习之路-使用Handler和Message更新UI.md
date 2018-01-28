# Android开发学习之路-使用Handler和Message更新UI

在Android中，在非主线程中更新UI控件是不安全的，app在运行时会直接Crash，所以当我们需要在非主线程中更新UI控件，那么就需要用到Handler和Message来实现

Demo中，使用到一个按钮和一个TextView，点击按钮之后改变TextView的内容，按钮点击时候新建一个进程，在进程中对UI控件进行修改。

    
    
     1 public class MainActivity extends Activity implements OnClickListener {
     2     private static final int UPDATE_TEXT = 1;
     3     private Button send;
     4     private TextView tv;
     5     
     6     private Handler hd = new MyHandler();
     7 
     8     @Override
     9     protected void onCreate(Bundle savedInstanceState) {
    10         super.onCreate(savedInstanceState);
    11         setContentView(R.layout.activity_main);
    12         send = (Button) findViewById(R.id.bt_sendMessage);
    13         tv = (TextView) findViewById(R.id.tv_text);
    14         send.setOnClickListener(this);
    15     }
    16 
    17     @Override
    18     public void onClick(View v) {
    19         switch (v.getId()) {
    20         case R.id.bt_sendMessage:
    21             new Thread(new Runnable() {
    22                 @Override
    23                 public void run() { //　新建一个线程，并新建一个Message的对象，是用Handler的对象发送这个Message
    24                     Message msg = new Message();
    25                     msg.what = UPDATE_TEXT; // 用户自定义的一个值，用于标识不同类型的消息
    26                     hd.sendMessage(msg); // 发送消息
    27                 }
    28             }).start();
    29             break;
    30 
    31         default:
    32             break;
    33         }
    34     }
    35     
    36     // 定义一个内部类继承自Handler，并且覆盖handleMessage方法用于处理子线程传过来的消息
    37     class MyHandler extends Handler{
    38         @Override
    39         public void handleMessage(Message msg) {
    40             super.handleMessage(msg);
    41             switch (msg.what) {
    42             case UPDATE_TEXT: // 接受到消息之后，对UI控件进行修改
    43                 tv.setText("修改成功！");
    44                 break;
    45 
    46             default:
    47                 break;
    48             }
    49         }
    50     }
    51 }

理解:先在主活动中定义一个内部类，并继承自Handler，并且覆盖Handler中的handleMessage方法，这个方法在Handler中是一个空方法，便于我们自定义消息的内容。然后在onCreate方法中，获取Button并且添加点击事件，在事件中添加一个线程，在线程中，使用Message类封装一个消息，然后用Handler的对象发送这个消息，Handler对象的handleMessage方法就会被调用，达到改变TextView的内容的效果。

