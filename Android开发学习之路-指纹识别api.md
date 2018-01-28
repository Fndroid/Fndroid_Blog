# Android����ѧϰ֮·-ָ��ʶ��api

��android6.0֮��ȸ��ָ��ʶ������˹ٷ�֧�֣����컹�ڷż٣����Ծ����Ⳣ����һ�����api�����������˸��ָ���������

����ʹ��FingerPrintManager�����ʵ�ֵ�ʱ�����˺ܶ����⣬����������һЩ�����Ǳ�hide�˵ģ�Ҳ�������ǲ��ܵ��ã�����enroll()��Ҳ����˵����ǰ�Ĺٷ�֧����ʵ�����޵ģ������ܶ�ȡ�������Ѿ����ڵ�ָ�ƣ����ڽ����ģ���Ȼ����֤��Щָ�ƣ����ǲ������û���appʹ�õ�ʱ��¼��һ��ָ�ƣ�����app���������ܣ������һ��ȱ�ݰ�Ŀǰ��˵�������ͼҲ��չʾ��ʶ��һ����������������ָ�ƵĹ��ܡ�

��ʹ��FingerPrintManager��ʱ�������Ҫ��Ӧ�����ж�Ȩ�޵����⣬���������Ϊandroid
MҪ��Ļ���api��Ҫ�����õ�ʱ��Ҳû�з�Ӧ����û�й��ܣ�Ҳû����ʾ����Ȩ�ޣ�ԭ���д���֤��

����һ�ٳ����޹�֮�����������ر���࣬һ������FingerPrintManagerCompat������һ�����ݵ�FingerPrint�����࣬����һ�����ƵĽ���FingerPrintManagerCompatApi23��ʹ����������Ҳ��ʵ�ֵ�ʶ��ָ�ƵĹ��ܣ�����Ҳ���о��޵ģ������˵�ġ��������඼��ʵ�����Ч����������ҪAPI
Level 23������ﲻ�����ᱨ����Щ���ʲô��������

չʾͼ����Ϊû�����������ֻ����ģ��������һ�£�����Ҳֻ���ùٷ��ģ�Genymotion��Ѱ�ò�Ʋ���ģ��ָ�ƣ�

![](http://images2015.cnblogs.com/blog/852227/201602/852227-20160221145838561-1224180971.gif)

 ֱ���ϴ��룺

    
    
    public class MainActivity extends AppCompatActivity implements View.OnClickListener {
        private static final String TAG = "MainActivity";
        private Button check;
        private FingerprintManagerCompat manager;
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            check = (Button) findViewById(R.id.btn_check);
    
            check.setOnClickListener(this);
    
            // ��ȡһ��FingerPrintManagerCompat��ʵ��
            manager = FingerprintManagerCompat.from(this);
        }
    
        @Override
        public void onClick(View v) {
            switch (v.getId()) {
                case R.id.btn_check:
                    /**
                     * ��ʼ��֤��ʲôʱ��ֹͣ��ϵͳ��ȷ���������֤�ɹ�����ôϵͳ���ϵsensor�����ʧ�ܣ�������
                     * ��γ��ԣ��������ʧ�ܣ����ܾ�һ��ʱ�䣬Ȼ��ر�sensor����һ��ʱ��֮��������������
                     * 
                     * ���ĸ�����Ϊ�ص㣬��Ҫ����һ��FingerprintManagerCompat.AuthenticationCallback������
                     * ����дһЩ��������ͬ������ص���ͬ�ĺ���
                     */
                    manager.authenticate(null, 0, null, new MyCallBack(), null);
                    break;
            }
        }
    
        public class MyCallBack extends FingerprintManagerCompat.AuthenticationCallback {
            private static final String TAG = "MyCallBack";
    
            // �����ִ����ʱ��ص��˺����������γ��Զ�ʧ���˵�ʱ��errString�Ǵ�����Ϣ
            @Override
            public void onAuthenticationError(int errMsgId, CharSequence errString) {
                Log.d(TAG, "onAuthenticationError: " + errString);
            }
    
            // ��ָ����֤ʧ�ܵ�ʱ���ص��˺�����ʧ��֮�������γ��ԣ�ʧ�ܴ��������ֹͣ��Ӧһ��ʱ��Ȼ����ֹͣsensor�Ĺ���
            @Override
            public void onAuthenticationFailed() {
                Log.d(TAG, "onAuthenticationFailed: " + "��֤ʧ��");
            }
    
            @Override
            public void onAuthenticationHelp(int helpMsgId, CharSequence helpString) {
                Log.d(TAG, "onAuthenticationHelp: " + helpString);
            }
    
            // ����֤��ָ�Ƴɹ�ʱ��ص��˺�����Ȼ���ټ���ָ��sensor
            @Override
            public void onAuthenticationSucceeded(FingerprintManagerCompat.AuthenticationResult
                                                          result) {
                Log.d(TAG, "onAuthenticationSucceeded: " + "��֤�ɹ�");
            }
        }
    
    }



�в�����������⣬�����¼�£�

1.�����ʧ�ܻ��߳ɹ�֮��Sensor�������ּ����µ�ָ�ƣ�

����ΪAPI���µ�Ե�ʣ�������ݵ�Manager�໹���������Զ������Ĺ��ܣ��������ǿ����Լ�дһ������ΪApi�й涨������ص���Error����Succeed����֮��sensor�ᱻ�رգ�ֱ����һ�����µ���authenticate������Ȩ���������ǲ�����Error��Succeedֱ�ӵ��������������Ϊ���ڰ�ȫ�ԵĿ��ǣ����������߶�ʱ����������Ȩ���������ԵĲ��ԣ�android����������30s֮�����´�Sensor��Ȩ��������������Ҫ���ģ�����ͨ��Handler��sendMessageDelayed��������һ���ӳٵ���Ϣ������Handler�����µ���authenticate����������Ĵ������£�

    
    
    private Handler handler = new Handler() {
        @Override
        public void handleMessage(Message msg) {
            super.handleMessage(msg);
            Log.d(TAG, "handleMessage: ����ָ��ģ��");
            manager.authenticate(null, 0, null, new MyCallBack(), handler);
        }
    };
    
    
    @Override
    public void onAuthenticationError(int errMsgId, CharSequence errString) {
        handler.sendMessageDelayed(new Message(), 1000 * 30);
        Log.d(TAG, "onAuthenticationError: " + errString);
    }
    
    @Override
    public void onAuthenticationSucceeded(FingerprintManagerCompat.AuthenticationResult result) {
        handler.sendMessageDelayed(new Message(), 1000 * 30);
        Log.d(TAG, "onAuthenticationSucceeded: " + "��֤�ɹ�");
    }



2.Ϊʲô6.0���µ�һЩ��ָ���ֻ�������FingerprintManager������ָ�ƣ���û��ָ�Ƶ��ֻ��������

�������ʱ��ΪĳЩ���̣�С�ס�vivo�ȣ���ָ��ʶ�������Rom�������FingerprintManager��API��ʵ�������API����6.0�ż���ġ��ĵ���

![](http://images2015.cnblogs.com/blog/852227/201609/852227-20160924210048246-484109509.png)

��͵�����һ�����⣺�����Ҫ��6.0���»���û������6.0ָ�Ƶ��ֻ�����ָ�Ʋ�����ʱ��Ҫ��ô����

ʹ��FingerprintManagerCompat�϶��ǲ��еģ���Ϊ�ĵ�Ҳ˵�ˣ�����M��ϵͳ�汾��FingerprintManagerCompat�����ֻ��Ƿ���ָ��ʶ��ģ�飬����Ϊû��ָ��ʶ����ô����ʵ�����ǿ�����FingerprintManager�����ģ���ΪС�׵ȳ����Ѿ���API�ӽ�ȥ�ˣ�����Ҫ��ֲ��ԣ��Ͼ����ǹٷ���api����

�ڹ����У�ʹ��������������һ��FingerprintManager��

    
    
    ��������FingerprintManager manager = (FingerprintManager) getSystemService(FINGERPRINT_SERVICE);

����ط�Ҫ��֤���CompleteSdkVersion�汾Ҫ����23������IDEҲ�Ҳ�������ࡣ

���ţ���������Ҫ����ֻ��Ƿ�֧��ָ��ʶ��

    
    
    ��������manager.hasEnrolledFingerprints();

���ʱ��������App��Ҫ��6.0�ļ����ϵ�ƽ̨���У�����Ҫ����[����ʱȨ�޼��](http://www.cnblogs.com/Fndroid/p/5542526.html)���������£�

    
    
            if (ActivityCompat.checkSelfPermission(this, Manifest.permission.USE_FINGERPRINT) !=
                    PackageManager.PERMISSION_GRANTED) {
                manager.hasEnrolledFingerprints();
                return;
            }

�������Studio�л�����ʾ���������Ҫ���ע�⣬������6.0�����ϵ�ƽ̨�в���ʹ�ã�

![](http://images2015.cnblogs.com/blog/852227/201609/852227-20160924211029965-363735357.png)

��ʵ���ϣ����ǲ���������ע�⣬��Ϊ���ǲ�����Ҫ��6.0�����ϵ�ƽ̨ʹ�ã���Ҫ��6.0���µ�ƽ̨ʹ�ã���ô���ǿ���ֱ��ѡ�е�4�е�Disable
inspection�������������

�����ǰ�APP��װ���ֻ��е�ʱ������ֻ�û��ָ��ʶ��ģ�飬APP�ͻ�Crash��Log�л���ʾClassNotFoundException�����ڵͰ汾��ָ��ʶ��Ļ���������ȴû�����⡣�����֤������һ��ʼ˵���ģ����̵�Rom�У�û��ָ��ʶ����ֻ���������Ӷ�Ӧ��API��������Ȼ����ʾ�Ҳ������ࡣ

��ô���Ǿ�����ô�õ����FingerprintManager���أ�

�ܼ򵥣���Application���Ƚ����жϣ�ͨ������������Ƿ���ڸ��࣬Ȼ��ѽ������������

    
    
    public class MyApplication extends Application {
        public static final String HAS_FINGERPRINT_API = "hasFingerPrintApi";
        public static final String SETTINGS = "settings";
    
        @Override
        public void onCreate() {
            super.onCreate();
            SharedPreferences sp = getSharedPreferences(SETTINGS, MODE_PRIVATE);
            if (sp.contains(HAS_FINGERPRINT_API)) { // ����Ƿ���ڸ�ֵ������ÿ�ζ�ͨ�����������
                return;
            }
            SharedPreferences.Editor editor = sp.edit();
            try {
                Class.forName("android.hardware.fingerprint.FingerprintManager"); // ͨ�������ж��Ƿ���ڸ���
                editor.putBoolean(HAS_FINGERPRINT_API, true);
            } catch (ClassNotFoundException e) {
                editor.putBoolean(HAS_FINGERPRINT_API, false);
                e.printStackTrace();
            }
            editor.apply();
        }
    }

�����ط�ֻ��Ҫ����ֵȡ���жϼ��ɡ�

����һ����ߵļ����ֻ���С��֧�֣����Ӳ�֧�֡�



3.��ôȡ��������

�ܼ򵥣�authenticate�����еĵڶ���������һ��CancellationSignal�����������������ά��ȡ�������ģ���Щ��������ȡ���������趨ȡ���ص��ȡ����ԣ����Ҫȡ������������Ͳ�����null�����԰Ѵ��������޸ģ�

��Activity�����һ��CancellationSignal������

    
    
    1     private CancellationSignal mCancellationSignal = new CancellationSignal();

���ţ���Ҫ��֤��ʱ�������������Ҫȡ����ʱ�򣬵�����������cancel�������ɣ�

    
    
     1     @Override
     2     public void onClick(View v) {
     3         switch (v.getId()) {
     4             case R.id.start:
     5                 if (mCancellationSignal.isCanceled()) {
     6                     mCancellationSignal = new CancellationSignal();
     7                 }
     8                 mFingerprintManagerCompat.authenticate(null, 0, mCancellationSignal, new MyCallBack(), null);
     9                 break;
    10             case R.id.stop:
    11                 mCancellationSignal.cancel();
    12                 break;
    13         }
    14     }



