# Android����ѧϰ֮·-ʹ��Handler��Message����UI

��Android�У��ڷ����߳��и���UI�ؼ��ǲ���ȫ�ģ�app������ʱ��ֱ��Crash�����Ե�������Ҫ�ڷ����߳��и���UI�ؼ�����ô����Ҫ�õ�Handler��Message��ʵ��

Demo�У�ʹ�õ�һ����ť��һ��TextView�������ť֮��ı�TextView�����ݣ���ť���ʱ���½�һ�����̣��ڽ����ж�UI�ؼ������޸ġ�

    
    
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
    23                 public void run() { //���½�һ���̣߳����½�һ��Message�Ķ�������Handler�Ķ��������Message
    24                     Message msg = new Message();
    25                     msg.what = UPDATE_TEXT; // �û��Զ����һ��ֵ�����ڱ�ʶ��ͬ���͵���Ϣ
    26                     hd.sendMessage(msg); // ������Ϣ
    27                 }
    28             }).start();
    29             break;
    30 
    31         default:
    32             break;
    33         }
    34     }
    35     
    36     // ����һ���ڲ���̳���Handler�����Ҹ���handleMessage�������ڴ������̴߳���������Ϣ
    37     class MyHandler extends Handler{
    38         @Override
    39         public void handleMessage(Message msg) {
    40             super.handleMessage(msg);
    41             switch (msg.what) {
    42             case UPDATE_TEXT: // ���ܵ���Ϣ֮�󣬶�UI�ؼ������޸�
    43                 tv.setText("�޸ĳɹ���");
    44                 break;
    45 
    46             default:
    47                 break;
    48             }
    49         }
    50     }
    51 }

���:��������ж���һ���ڲ��࣬���̳���Handler�����Ҹ���Handler�е�handleMessage���������������Handler����һ���շ��������������Զ�����Ϣ�����ݡ�Ȼ����onCreate�����У���ȡButton������ӵ���¼������¼������һ���̣߳����߳��У�ʹ��Message���װһ����Ϣ��Ȼ����Handler�Ķ����������Ϣ��Handler�����handleMessage�����ͻᱻ���ã��ﵽ�ı�TextView�����ݵ�Ч����

