# Android����ѧϰ֮·-��������ֿ���ȫ����

����������֮�ʣ�΢��΢��֧��������ǵ����ɣ����ǣ��Լ����������Ǳȱ�����һ������º��û������������־�Ӧ�˶�����

�����յ������ʱ��������ѣ�Ȼ����ת������Ľ��淽���û�

˼·����ȡ"��ȡ֪ͨ��Ϣ"Ȩ�ޣ�Ȼ����������ϵͳ֪ͨ���ж������΢�ź���ͽ������ѣ���������Ȼ����ת��������ڵĵط�

���棺

![](http://images2015.cnblogs.com/blog/852227/201602/852227-20160209123718980-1614733833.png)

 �����Ϊ�����֣�һ�����ǿ��Զ�App���в����ģ�������һ�����Ի����Ľ��棬��ʾ�û����������������������ִ������£�

![](http://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif)![](http://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif)

    
    
    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout
        android:id="@+id/root"
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_marginLeft="10dp"
        android:layout_marginRight="10dp"
        android:layout_marginTop="5dp"
        android:orientation="vertical"
        tools:context="com.fndroid.administrator.justforyou.MainActivity">
    
    
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="horizontal">
    
            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_weight="3"
                android:text="����ʾ��"/>
    
            <CheckBox
                android:id="@+id/isMusic"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"/>
    
        </LinearLayout>
    
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content">
    
            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_gravity="center"
                android:text="��������"/>
    
            <SeekBar
                android:id="@+id/seekbar"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_gravity="center"
                android:layout_weight="1"/>
    
        </LinearLayout>
    
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="horizontal">
    
            <TextView
                android:layout_width="0dp"
                android:layout_height="wrap_content"
                android:layout_weight="3"
                android:text="�к������������"/>
    
            <CheckBox
                android:id="@+id/isUnlock"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"/>
        </LinearLayout>
    
        <Button
            android:id="@+id/setPermision"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="center"
            android:text="����֪ͨȨ��"/>
    
        <ScrollView
            android:layout_width="match_parent"
            android:layout_height="match_parent">
    
            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:orientation="vertical">
    
                <TextView
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:layout_marginTop="10dp"
                    android:text="������"/>
    
                <TextView
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="�����Ϊ���˿������ã�ֻ�ܶ�΢�ź���������ѡ������ʹ�ñ������ʹ�ò�����ɵĸ�����Ϊ���뱾���޹ء����ʹ�ù��̲��������������κε����û���Ϣ��Ϊ�������ʹ�á�"/>
    
    
                <TextView
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:layout_marginTop="10dp"
                    android:text="ʹ�÷�����"/>
    
                <TextView
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="�����δ���������ȡ֪ͨȨ�ޣ������ť"����֪ͨȨ��"/>
    
                <TextView
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="���ڱ�����Ҳ๴ѡ�ϣ���ȷ����ʾ��Ϣ"/>
    
                <ImageView
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:scaleType="fitCenter"
                    android:src="@drawable/inf"/>
    
                <TextView
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="�۹ر�΢��Ⱥ����Ϣ����ţ�ȡ��ͼ�е���ɫ��ť��"/>
    
                <ImageView
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:src="@drawable/inf2"/>
            </LinearLayout>
        </ScrollView>
    </LinearLayout>

View Code



app�򿪵�ʱ����һ�����񣬱�дһ��NotificationListenerService�����ಢʵ��onNotificationPosted��onNotificationRemoved������ǰ��ķ��������յ�֪ͨ��ʱ�����

![](http://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif)![](http://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif)

    
    
    // ��дһ��NotificationListenerService�����ಢʵ��onNotificationPosted��onNotificationRemoved����
    // �����������ڴ�SDK�汾21��ʱ��ʼ����˷ǳ��󣬲���д���ܼ���21�����豸
    public class NotificationService extends NotificationListenerService {
    
        private KeyguardManager.KeyguardLock kl;
    
        @Override
        public void onNotificationPosted(StatusBarNotification sbn) {
            // ���������õ���Ϣ������SharedPreferences�У���������л�ȡ
            SharedPreferences sharedPreferences = getSharedPreferences("userdata", MODE_PRIVATE);
    
            // �ж���Ϣ�Ƿ�Ϊ΢�ź��
            if (sbn.getNotification().tickerText.toString().contains("[΢�ź��]") && sbn.getPackageName
                    ().equals("com.tencent.mm")) {
    
                // ��ȡ������Ϣ���ж��Ƿ�õ�����Ļ���⿪������������ԭ���ǰ������رյ�
                if (sharedPreferences.getBoolean("isUnlock",true)) {
                    KeyguardManager km = (KeyguardManager) getSystemService(getApplicationContext()
                            .KEYGUARD_SERVICE);
                    kl = km.newKeyguardLock("unlock");
    
                    // ��ϵͳ������ʱ�ر�
                    kl.disableKeyguard();
                    PowerManager pm = (PowerManager) getSystemService(getApplicationContext()
                            .POWER_SERVICE);
                    PowerManager.WakeLock wl = pm.newWakeLock(PowerManager.ACQUIRE_CAUSES_WAKEUP |
                            PowerManager.SCREEN_DIM_WAKE_LOCK, "bright");
                    wl.acquire();
                    wl.release();
                }
    
                try {
                    // ��notification����Ӧ��pendingintent
                    sbn.getNotification().contentIntent.send();
    
                } catch (PendingIntent.CanceledException e) {
                    e.printStackTrace();
                }
    
                // �ж��Ƿ�ò�����ʾ��
                if (sharedPreferences.getBoolean("isMusic",true)){
                    MediaPlayer mediaPlayer = new MediaPlayer().create(this, R.raw.heihei);
                    mediaPlayer.start();
                }
    
                // �������һ��ϵͳ�㲥���ж������ĻϨ��Ͱ�ϵͳ������ԭ
                IntentFilter intentFilter = new IntentFilter();
                intentFilter.addAction("android.intent.action.SCREEN_OFF");
                ScreenOffReceiver screenOffReceiver = new ScreenOffReceiver();
                registerReceiver(screenOffReceiver, intentFilter);
    
            }
    
        }
    
        class ScreenOffReceiver extends BroadcastReceiver {
            @Override
            public void onReceive(Context context, Intent intent) {
                if (kl != null) {
                    // ��ԭ����
                    kl.reenableKeyguard();
                }
            }
        }
    
        @Override
        public void onNotificationRemoved(StatusBarNotification sbn) {
            super.onNotificationRemoved(sbn);
        }
    }

View Code



����activity��ע���ڴ������ˣ��Ͳ���ϸ˵��

![](http://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif)![](http://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif)

    
    
    public class MainActivity extends AppCompatActivity implements CompoundButton
            .OnCheckedChangeListener, View.OnClickListener,SeekBar.OnSeekBarChangeListener {
    
        private LinearLayout root;
        private CheckBox isMusic;
        private CheckBox isUnlock;
        private SharedPreferences.Editor editor;
        private SharedPreferences sharedPreferences;
        private Button setPermision;
        private SeekBar seekBar;
        private AudioManager audioManager;
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            // ��ȡ�ؼ�ʵ��
            root = (LinearLayout) findViewById(R.id.root);
            isMusic = (CheckBox) findViewById(R.id.isMusic);
            isUnlock = (CheckBox) findViewById(R.id.isUnlock);
            setPermision = (Button) findViewById(R.id.setPermision);
            seekBar = (SeekBar) findViewById(R.id.seekbar);
    
            // ע�����
            isMusic.setOnCheckedChangeListener(this);
            isUnlock.setOnCheckedChangeListener(this);
            setPermision.setOnClickListener(this);
            seekBar.setOnSeekBarChangeListener(this);
    
            // ��ȡ������Ϣ
            sharedPreferences = getSharedPreferences("userdata", MODE_PRIVATE);
            editor = sharedPreferences.edit();
            boolean music = sharedPreferences.getBoolean("isMusic", true);
            boolean unlock = sharedPreferences.getBoolean("isUnlock", true);
            isMusic.setChecked(music);
            isUnlock.setChecked(unlock);
    
            // ���Audiomanager������ϵͳ����
            audioManager = (AudioManager) getSystemService(this.AUDIO_SERVICE);
            seekBar.setMax(audioManager.getStreamMaxVolume(AudioManager.STREAM_MUSIC));
            seekBar.setProgress(audioManager.getStreamVolume(AudioManager.STREAM_MUSIC));
    
            // ����ϵͳý�������ı䣬���ı�����ϵ�Seekbar�Ľ���
            IntentFilter intentFilter = new IntentFilter();
            intentFilter.addAction("android.media.VOLUME_CHANGED_ACTION");
            VolumReceiver receiver = new VolumReceiver();
            registerReceiver(receiver,intentFilter);
    
            // ��������
            Intent intent = new Intent(MainActivity.this, NotificationService.class);
            startService(intent);
        }
    
        @Override
        public boolean onKeyDown(int keyCode, KeyEvent event) {
            // �жϷ��ؼ��������ʾ�û��Ƿ�ȷ���˳�
            if (keyCode == KeyEvent.KEYCODE_BACK && event.getRepeatCount() == 0) {
                Snackbar snackbar = Snackbar.make(root, "�˳����", Snackbar.LENGTH_LONG)
                        .setAction("ȷ��", new View.OnClickListener() {
                            @Override
                            public void onClick(View v) {
                                MainActivity.this.finish();
                            }
                        });
                snackbar.show();
                return true;
            }
            return super.onKeyDown(keyCode, event);
        }
    
        @Override
        public void onCheckedChanged(CompoundButton buttonView, boolean isChecked) {
            // checkbox�ĵ������
            switch (buttonView.getId()) {
                case R.id.isMusic:
                    editor.putBoolean("isMusic", isChecked);
                    editor.commit();
                    break;
                case R.id.isUnlock:
                    editor.putBoolean("isUnlock", isChecked);
                    editor.commit();
                    break;
            }
    
        }
    
        @Override
        public void onClick(View v) {
            switch (v.getId()){
                case R.id.setPermision:
                    // ��ϵͳ����ķ��񣬷����û�ֱ�Ӹ���Ȩ��
                    Intent intent = new Intent(Settings.ACTION_NOTIFICATION_LISTENER_SETTINGS);
                    startActivity(intent);
                    break;
            }
    
        }
    
        @Override
        public void onProgressChanged(SeekBar seekBar, int progress, boolean fromUser) {
        }
    
        @Override
        public void onStartTrackingTouch(SeekBar seekBar) {
        }
    
        @Override
        public void onStopTrackingTouch(SeekBar seekBar) {
            // seekbar�ļ���������ֹͣ���޸�ϵͳý������
            audioManager.setStreamVolume(AudioManager.STREAM_MUSIC,seekBar.getProgress(),0);
        }
    
        // �����㲥����
        class VolumReceiver extends BroadcastReceiver{
            @Override
            public void onReceive(Context context, Intent intent) {
                seekBar.setProgress(audioManager.getStreamVolume(AudioManager.STREAM_MUSIC));
            }
        }
    }

View Code



 Mainfest

![](http://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif)![](http://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif)

    
    
    <?xml version="1.0" encoding="utf-8"?>
    <manifest package="com.fndroid.administrator.justforyou"
              xmlns:android="http://schemas.android.com/apk/res/android">
    
        <uses-permission android:name="android.permission.BIND_NOTIFICATION_LISTENER_SERVICE"/>
        <uses-permission android:name="android.permission.WAKE_LOCK" />
        <uses-permission android:name="android.permission.DISABLE_KEYGUARD" />
    
        <application
            android:allowBackup="true"
            android:icon="@mipmap/ic_launcher"
            android:label="@string/app_name"
            android:supportsRtl="true"
            android:theme="@style/AppTheme">
            <activity android:name=".MainActivity">
                <intent-filter>
                    <action android:name="android.intent.action.MAIN"/>
    
                    <category android:name="android.intent.category.LAUNCHER"/>
                </intent-filter>
            </activity>
            <service android:name=".NotificationService"
                     android:permission="android.permission.BIND_NOTIFICATION_LISTENER_SERVICE">
                <intent-filter>
                    <action android:name="android.service.notification.NotificationListenerService" />
                </intent-filter>
            </service>
        </application>
    
    </manifest>

View Code



gradle�����������Ϊ����Snackbar

![](http://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif)![](http://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif)

    
    
    dependencies {
        compile fileTree(dir: 'libs', include: ['*.jar'])
        testCompile 'junit:junit:4.12'
        compile 'com.android.support:appcompat-v7:23.1.1'
        compile 'com.android.support:design:23.1.1'
    }

View Code



