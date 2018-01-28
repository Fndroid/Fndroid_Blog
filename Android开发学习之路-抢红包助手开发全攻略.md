# Android开发学习之路-抢红包助手开发全攻略

背景：新年之际，微信微博支付宝红包是到处飞，但是，自己的手速总是比别人慢一点最后导致红包没抢到，红包助手就应运而生。

需求：收到红包的时候进行提醒，然后跳转到红包的界面方便用户

思路：获取"读取通知信息"权限，然后开启服务监控系统通知，判断如果是微信红包就进行提醒（声音），然后跳转到红包所在的地方

界面：

![](http://images2015.cnblogs.com/blog/852227/201602/852227-20160209123718980-1614733833.png)

 界面分为两部分，一部分是可以对App进行操作的，下面是一个可以滑动的界面，提示用户如何是软件正常工作，布局代码如下：

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
                android:text="打开提示音"/>
    
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
                android:text="音量调节"/>
    
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
                android:text="有红包亮屏并解锁"/>
    
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
            android:text="设置通知权限"/>
    
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
                    android:text="声明："/>
    
                <TextView
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="本软件为个人开发所得，只能对微信红包进行提醒。请合理使用本软件，使用不当造成的各种行为均与本人无关。软件使用过程不联网，不存在任何盗窃用户信息行为，请放心使用。"/>
    
    
                <TextView
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:layout_marginTop="10dp"
                    android:text="使用方法："/>
    
                <TextView
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="①如果未赋予软件读取通知权限，点击按钮"设置通知权限"/>
    
                <TextView
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="②在本软件右侧勾选上，并确认提示信息"/>
    
                <ImageView
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:scaleType="fitCenter"
                    android:src="@drawable/inf"/>
    
                <TextView
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="③关闭微信群的消息免打扰（取消图中的绿色按钮）"/>
    
                <ImageView
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:src="@drawable/inf2"/>
            </LinearLayout>
        </ScrollView>
    </LinearLayout>

View Code



app打开的时候开启一个服务，编写一个NotificationListenerService的子类并实现onNotificationPosted和onNotificationRemoved方法，前面的方法会在收到通知的时候调用

![](http://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif)![](http://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif)

    
    
    // 编写一个NotificationListenerService的子类并实现onNotificationPosted和onNotificationRemoved方法
    // 这两个方法在从SDK版本21的时候开始变成了非抽象，不重写则不能兼容21以下设备
    public class NotificationService extends NotificationListenerService {
    
        private KeyguardManager.KeyguardLock kl;
    
        @Override
        public void onNotificationPosted(StatusBarNotification sbn) {
            // 主界面设置的信息保存在SharedPreferences中，在这里进行获取
            SharedPreferences sharedPreferences = getSharedPreferences("userdata", MODE_PRIVATE);
    
            // 判断消息是否为微信红包
            if (sbn.getNotification().tickerText.toString().contains("[微信红包]") && sbn.getPackageName
                    ().equals("com.tencent.mm")) {
    
                // 读取设置信息，判断是否该点亮屏幕并解开锁屏，解锁的原理是把锁屏关闭掉
                if (sharedPreferences.getBoolean("isUnlock",true)) {
                    KeyguardManager km = (KeyguardManager) getSystemService(getApplicationContext()
                            .KEYGUARD_SERVICE);
                    kl = km.newKeyguardLock("unlock");
    
                    // 把系统锁屏暂时关闭
                    kl.disableKeyguard();
                    PowerManager pm = (PowerManager) getSystemService(getApplicationContext()
                            .POWER_SERVICE);
                    PowerManager.WakeLock wl = pm.newWakeLock(PowerManager.ACQUIRE_CAUSES_WAKEUP |
                            PowerManager.SCREEN_DIM_WAKE_LOCK, "bright");
                    wl.acquire();
                    wl.release();
                }
    
                try {
                    // 打开notification所对应的pendingintent
                    sbn.getNotification().contentIntent.send();
    
                } catch (PendingIntent.CanceledException e) {
                    e.printStackTrace();
                }
    
                // 判断是否该播放提示音
                if (sharedPreferences.getBoolean("isMusic",true)){
                    MediaPlayer mediaPlayer = new MediaPlayer().create(this, R.raw.heihei);
                    mediaPlayer.start();
                }
    
                // 这里监听一下系统广播，判断如果屏幕熄灭就把系统锁屏还原
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
                    // 还原锁屏
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



主的activity，注释在代码中了，就不详细说了

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
    
            // 获取控件实例
            root = (LinearLayout) findViewById(R.id.root);
            isMusic = (CheckBox) findViewById(R.id.isMusic);
            isUnlock = (CheckBox) findViewById(R.id.isUnlock);
            setPermision = (Button) findViewById(R.id.setPermision);
            seekBar = (SeekBar) findViewById(R.id.seekbar);
    
            // 注册监听
            isMusic.setOnCheckedChangeListener(this);
            isUnlock.setOnCheckedChangeListener(this);
            setPermision.setOnClickListener(this);
            seekBar.setOnSeekBarChangeListener(this);
    
            // 读取设置信息
            sharedPreferences = getSharedPreferences("userdata", MODE_PRIVATE);
            editor = sharedPreferences.edit();
            boolean music = sharedPreferences.getBoolean("isMusic", true);
            boolean unlock = sharedPreferences.getBoolean("isUnlock", true);
            isMusic.setChecked(music);
            isUnlock.setChecked(unlock);
    
            // 获得Audiomanager，控制系统音量
            audioManager = (AudioManager) getSystemService(this.AUDIO_SERVICE);
            seekBar.setMax(audioManager.getStreamMaxVolume(AudioManager.STREAM_MUSIC));
            seekBar.setProgress(audioManager.getStreamVolume(AudioManager.STREAM_MUSIC));
    
            // 监听系统媒体音量改变，并改变界面上的Seekbar的进度
            IntentFilter intentFilter = new IntentFilter();
            intentFilter.addAction("android.media.VOLUME_CHANGED_ACTION");
            VolumReceiver receiver = new VolumReceiver();
            registerReceiver(receiver,intentFilter);
    
            // 开启服务
            Intent intent = new Intent(MainActivity.this, NotificationService.class);
            startService(intent);
        }
    
        @Override
        public boolean onKeyDown(int keyCode, KeyEvent event) {
            // 判断返回键点击，提示用户是否确认退出
            if (keyCode == KeyEvent.KEYCODE_BACK && event.getRepeatCount() == 0) {
                Snackbar snackbar = Snackbar.make(root, "退出软件", Snackbar.LENGTH_LONG)
                        .setAction("确认", new View.OnClickListener() {
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
            // checkbox的点击监听
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
                    // 打开系统里面的服务，方便用户直接赋予权限
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
            // seekbar的监听，滑动停止就修改系统媒体音量
            audioManager.setStreamVolume(AudioManager.STREAM_MUSIC,seekBar.getProgress(),0);
        }
    
        // 音量广播接收
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



gradle添加依赖，因为用了Snackbar

![](http://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif)![](http://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif)

    
    
    dependencies {
        compile fileTree(dir: 'libs', include: ['*.jar'])
        testCompile 'junit:junit:4.12'
        compile 'com.android.support:appcompat-v7:23.1.1'
        compile 'com.android.support:design:23.1.1'
    }

View Code



