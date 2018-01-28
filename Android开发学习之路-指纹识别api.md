# Android开发学习之路-指纹识别api

在android6.0之后谷歌对指纹识别进行了官方支持，今天还在放假，所以就随意尝试了一下这个api，但是遇到了各种各样的问题

①在使用FingerPrintManager这个类实现的时候发现了很多问题，这个类里面的一些函数是被hide了的，也就是我们不能调用，比如enroll()，也就是说，当前的官方支持其实是有限的，我们能读取到本机已经存在的指纹（用于解锁的），然后验证这些指纹，但是不能让用户在app使用的时候录入一个指纹，用于app的其他功能，这个是一个缺陷吧目前来说，下面的图也是展示了识别一个可以用来解锁的指纹的功能。

②使用FingerPrintManager的时候会遇到要在应用中判断权限的问题，不清楚是因为android
M要求的还是api需要，调用的时候也没有反应，既没有功能，也没有提示给予权限，原因有待查证。

③在一顿尝试无果之后发现了两个特别的类，一个叫做FingerPrintManagerCompat，这是一个兼容的FingerPrint操作类，还有一个类似的叫做FingerPrintManagerCompatApi23，使用这两个类也能实现到识别指纹的功能，但是也是有局限的，如①所说的。这两个类都能实现这个效果，但是需要API
Level 23，如果达不到不会报错，这些类会什么都不做。

展示图，因为没有真机，所以只能用模拟器调试一下，这里也只能用官方的，Genymotion免费版貌似不能模拟指纹：

![](http://images2015.cnblogs.com/blog/852227/201602/852227-20160221145838561-1224180971.gif)

 直接上代码：

    
    
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
    
            // 获取一个FingerPrintManagerCompat的实例
            manager = FingerprintManagerCompat.from(this);
        }
    
        @Override
        public void onClick(View v) {
            switch (v.getId()) {
                case R.id.btn_check:
                    /**
                     * 开始验证，什么时候停止由系统来确定，如果验证成功，那么系统会关系sensor，如果失败，则允许
                     * 多次尝试，如果依旧失败，则会拒绝一段时间，然后关闭sensor，过一段时候之后再重新允许尝试
                     * 
                     * 第四个参数为重点，需要传入一个FingerprintManagerCompat.AuthenticationCallback的子类
                     * 并重写一些方法，不同的情况回调不同的函数
                     */
                    manager.authenticate(null, 0, null, new MyCallBack(), null);
                    break;
            }
        }
    
        public class MyCallBack extends FingerprintManagerCompat.AuthenticationCallback {
            private static final String TAG = "MyCallBack";
    
            // 当出现错误的时候回调此函数，比如多次尝试都失败了的时候，errString是错误信息
            @Override
            public void onAuthenticationError(int errMsgId, CharSequence errString) {
                Log.d(TAG, "onAuthenticationError: " + errString);
            }
    
            // 当指纹验证失败的时候会回调此函数，失败之后允许多次尝试，失败次数过多会停止响应一段时间然后再停止sensor的工作
            @Override
            public void onAuthenticationFailed() {
                Log.d(TAG, "onAuthenticationFailed: " + "验证失败");
            }
    
            @Override
            public void onAuthenticationHelp(int helpMsgId, CharSequence helpString) {
                Log.d(TAG, "onAuthenticationHelp: " + helpString);
            }
    
            // 当验证的指纹成功时会回调此函数，然后不再监听指纹sensor
            @Override
            public void onAuthenticationSucceeded(FingerprintManagerCompat.AuthenticationResult
                                                          result) {
                Log.d(TAG, "onAuthenticationSucceeded: " + "验证成功");
            }
        }
    
    }



有博友提出了问题，这里记录下：

1.如何让失败或者成功之后Sensor继续保持监听新的指纹？

答：因为API较新的缘故，这个兼容的Manager类还不能做到自动重启的功能，但是我们可以自己写一个。因为Api中规定了如果回调了Error或者Succeed方法之后，sensor会被关闭，直到下一次重新调用authenticate方法授权，但是我们不能在Error或Succeed直接调用这个方法，因为处于安全性的考虑，不允许开发者短时间内连续授权，经过粗略的测试，android允许我们在30s之后重新打开Sensor授权监听，所以我们要做的，就是通过Handler的sendMessageDelayed方法发送一个延迟的消息，再在Handler中重新调用authenticate方法，具体的代码如下：

    
    
    private Handler handler = new Handler() {
        @Override
        public void handleMessage(Message msg) {
            super.handleMessage(msg);
            Log.d(TAG, "handleMessage: 重启指纹模块");
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
        Log.d(TAG, "onAuthenticationSucceeded: " + "验证成功");
    }



2.为什么6.0以下的一些带指纹手机可以用FingerprintManager来操作指纹，而没有指纹的手机会崩溃。

这个估计时因为某些厂商（小米、vivo等）的指纹识别机器的Rom中添加了FingerprintManager的API，实际上这个API是在6.0才加入的。文档：

![](http://images2015.cnblogs.com/blog/852227/201609/852227-20160924210048246-484109509.png)

这就导出了一个问题：如果我要给6.0以下或者没有适配6.0指纹的手机进行指纹操作的时候，要怎么做？

使用FingerprintManagerCompat肯定是不行的，因为文档也说了，低于M的系统版本，FingerprintManagerCompat无论手机是否有指纹识别模块，均认为没有指纹识别。那么我们实际上是可以用FingerprintManager来做的，因为小米等厂商已经把API加进去了（这里要充分测试，毕竟不是官方的api）。

在工程中，使用下面代码来获得一个FingerprintManager：

    
    
    　　　　FingerprintManager manager = (FingerprintManager) getSystemService(FINGERPRINT_SERVICE);

这个地方要保证你的CompleteSdkVersion版本要大于23，否则IDE也找不到这个类。

接着，假设我们要检查手机是否支持指纹识别：

    
    
    　　　　manager.hasEnrolledFingerprints();

这个时候，如果你的App需要在6.0的及以上的平台运行，还需要进行[运行时权限检查](http://www.cnblogs.com/Fndroid/p/5542526.html)，代码如下：

    
    
            if (ActivityCompat.checkSelfPermission(this, Manifest.permission.USE_FINGERPRINT) !=
                    PackageManager.PERMISSION_GRANTED) {
                manager.hasEnrolledFingerprints();
                return;
            }

到了这里，Studio中还会提示，这个方法要添加注解，表明在6.0及以上的平台中才能使用：

![](http://images2015.cnblogs.com/blog/852227/201609/852227-20160924211029965-363735357.png)

而实际上，我们不能添加这个注解，因为我们不仅仅要给6.0及以上的平台使用，还要给6.0以下的平台使用，那么我们可以直接选中第4行的Disable
inspection来忽略这个错误。

当我们把APP安装到手机中的时候，如果手机没有指纹识别模块，APP就会Crash，Log中会显示ClassNotFoundException，而在低版本带指纹识别的机器上运行却没有问题。这就佐证了我们一开始说到的，厂商的Rom中，没有指纹识别的手机并不会添加对应的API，所以自然会提示找不到该类。

那么我们究竟怎么用到这个FingerprintManager类呢？

很简单，在Application中先进行判断，通过反射来检查是否存在该类，然后把结果保存起来：

    
    
    public class MyApplication extends Application {
        public static final String HAS_FINGERPRINT_API = "hasFingerPrintApi";
        public static final String SETTINGS = "settings";
    
        @Override
        public void onCreate() {
            super.onCreate();
            SharedPreferences sp = getSharedPreferences(SETTINGS, MODE_PRIVATE);
            if (sp.contains(HAS_FINGERPRINT_API)) { // 检查是否存在该值，不必每次都通过反射来检查
                return;
            }
            SharedPreferences.Editor editor = sp.edit();
            try {
                Class.forName("android.hardware.fingerprint.FingerprintManager"); // 通过反射判断是否存在该类
                editor.putBoolean(HAS_FINGERPRINT_API, true);
            } catch (ClassNotFoundException e) {
                editor.putBoolean(HAS_FINGERPRINT_API, false);
                e.printStackTrace();
            }
            editor.apply();
        }
    }

其他地方只需要将该值取出判断即可。

试了一下身边的几个手机，小米支持，乐视不支持。



3.怎么取消监听？

很简单，authenticate方法中的第二个参数是一个CancellationSignal对象，这个对象是用来维护取消操作的，这些操作包括取消监听和设定取消回调等。所以，如果要取消，这个参数就不能是null，可以把代码稍作修改：

在Activity中添加一个CancellationSignal变量：

    
    
    1     private CancellationSignal mCancellationSignal = new CancellationSignal();

接着，在要验证的时候传入这个对象，在要取消的时候，调用这个对象的cancel方法即可：

    
    
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



