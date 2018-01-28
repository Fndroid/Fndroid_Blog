# Android开发学习之路-二维码学习

这个月装逼有点少了，为什么呢，因为去考软件射鸡师了，快到儿童节了，赶紧写篇博纪念一下逝去的青春，唔，请忽略这句话。



二维码其实有很多种，但是我们常见的微信使用的是一种叫做 **QRCode** 的二维码，像下面这样的，可以放心的扫，这只是我的博客主页链接啦：

![](http://images2015.cnblogs.com/blog/852227/201605/852227-20160529220234194-1171199824.png)

关于QR码编码的二维码，我们要知道几个特点：

1\. 扫描时可以从各个角度去扫，也就是旋转多少度都没关系，不信吗？下次去肯德基买单试试咯。

2\.
二维码有容错率，容错率越大，生成的二维码也就越复杂，但是越不容易出错，并且，当二维码被遮挡的时候也越容易扫描出来。这里我上传的二维码容错为30%，可以从左上角开始扫描，大概扫描到下图范围的时候就可以识别出来了：

![](http://images2015.cnblogs.com/blog/852227/201605/852227-20160529221753553-2000036227.png)

3\. 二维码的字符内容是有限制的，并且内容越多，二维码越复杂。如果想你的二维码容易被一些像素低的手机扫描出来，就尽量不要太复杂。

注意：二维码生成可以通过各种网站来完成，只需要输入内容即可得到。



既然二维码生成了，我们就要知道怎么用手机来扫描然后得到二维码代表的内容，这里介绍一个最简单的第三方库： **barcodescaner**

我们通过这个库，可以直接编写一个用于拍摄并识别的活动，然后通过活动返回的结果进行结果处理，具体的步骤如下：

**1\. 添加依赖**

    
    
    compile 'me.dm7.barcodescanner:zxing:1.8.4'

**2\. 创建一个活动，代码如下所示：**

    
    
     public class ScannerActivity extends AppCompatActivity implements ZXingScannerView.ResultHandler {
        private ZXingScannerView mZXingScannerView;
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            mZXingScannerView = new ZXingScannerView(this); // 将ZXingScannerView作为布局
            setContentView(mZXingScannerView);
        }
    
        @Override
        protected void onResume() {
            super.onResume();
            mZXingScannerView.setResultHandler(this); // 设置处理结果回调
            mZXingScannerView.startCamera(); // 打开摄像头
        }
    
        @Override
        protected void onPause() {
            super.onPause();
            mZXingScannerView.stopCamera(); // 活动失去焦点的时候关闭摄像头
        }
    
        @Override
        public void handleResult(Result result) { // 实现回调接口，将数据回传并结束活动
            Intent data = new Intent();
            data.putExtra("text", result.getText());
            setResult(RESULT_OK, data);
            finish();
        }
    }

**3\. 在主活动中打开这个活动，并处理传回的数据：**

    
    
     public class HomeActivity extends AppCompatActivity {
        private TextView mTextView;
        private WebView mWebView;
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_home);
            mTextView = (TextView) findViewById(R.id.tv);
            mWebView = (WebView) findViewById(R.id.wv);
        }
    
        public void scanCode(View view){
            startActivityForResult(new Intent(this, ScannerActivity.class), 1);
        }
    
        @Override
        protected void onActivityResult(int requestCode, int resultCode, Intent data) {
            super.onActivityResult(requestCode, resultCode, data);
            if (resultCode == RESULT_OK){
                mTextView.setText(data.getStringExtra("text")); // 显示识别到的文字
                mWebView.loadUrl(data.getStringExtra("text")); // 将识别的内容当作网址加载到WebView
            }
        }
    }

**4\. 添加摄像头和访问网络权限：**

    
    
    <uses-permission android:name="android.permission.CAMERA"/>
    <uses-permission android:name="android.permission.INTERNET"/>



使用这个工具类的方法非常简单，只需要将一个ZXingScannerView作为整个活动的布局，然后设置解析成功的回调接口，实现回调的方法将数据回传个主活动即可。

当然，如果是需要一个自定义扫描界面的效果，就没有这么简单了。



