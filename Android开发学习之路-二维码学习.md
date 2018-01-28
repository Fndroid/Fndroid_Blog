# Android����ѧϰ֮·-��ά��ѧϰ

�����װ���е����ˣ�Ϊʲô�أ���Ϊȥ������伦ʦ�ˣ��쵽��ͯ���ˣ��Ͻ�дƪ������һ����ȥ���ഺ�����������仰��



��ά����ʵ�кܶ��֣��������ǳ�����΢��ʹ�õ���һ�ֽ��� **QRCode** �Ķ�ά�룬�����������ģ����Է��ĵ�ɨ����ֻ���ҵĲ�����ҳ��������

![](http://images2015.cnblogs.com/blog/852227/201605/852227-20160529220234194-1171199824.png)

����QR�����Ķ�ά�룬����Ҫ֪�������ص㣺

1\. ɨ��ʱ���ԴӸ����Ƕ�ȥɨ��Ҳ������ת���ٶȶ�û��ϵ���������´�ȥ�ϵ»������Կ���

2\.
��ά�����ݴ��ʣ��ݴ���Խ�����ɵĶ�ά��Ҳ��Խ���ӣ�����Խ�����׳������ң�����ά�뱻�ڵ���ʱ��ҲԽ����ɨ��������������ϴ��Ķ�ά���ݴ�Ϊ30%�����Դ����Ͻǿ�ʼɨ�裬���ɨ�赽��ͼ��Χ��ʱ��Ϳ���ʶ������ˣ�

![](http://images2015.cnblogs.com/blog/852227/201605/852227-20160529221753553-2000036227.png)

3\. ��ά����ַ������������Ƶģ���������Խ�࣬��ά��Խ���ӡ��������Ķ�ά�����ױ�һЩ���ص͵��ֻ�ɨ��������;�����Ҫ̫���ӡ�

ע�⣺��ά�����ɿ���ͨ��������վ����ɣ�ֻ��Ҫ�������ݼ��ɵõ���



��Ȼ��ά�������ˣ����Ǿ�Ҫ֪����ô���ֻ���ɨ��Ȼ��õ���ά���������ݣ��������һ����򵥵ĵ������⣺ **barcodescaner**

����ͨ������⣬����ֱ�ӱ�дһ���������㲢ʶ��Ļ��Ȼ��ͨ������صĽ�����н����������Ĳ������£�

**1\. �������**

    
    
    compile 'me.dm7.barcodescanner:zxing:1.8.4'

**2\. ����һ���������������ʾ��**

    
    
     public class ScannerActivity extends AppCompatActivity implements ZXingScannerView.ResultHandler {
        private ZXingScannerView mZXingScannerView;
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            mZXingScannerView = new ZXingScannerView(this); // ��ZXingScannerView��Ϊ����
            setContentView(mZXingScannerView);
        }
    
        @Override
        protected void onResume() {
            super.onResume();
            mZXingScannerView.setResultHandler(this); // ���ô������ص�
            mZXingScannerView.startCamera(); // ������ͷ
        }
    
        @Override
        protected void onPause() {
            super.onPause();
            mZXingScannerView.stopCamera(); // �ʧȥ�����ʱ��ر�����ͷ
        }
    
        @Override
        public void handleResult(Result result) { // ʵ�ֻص��ӿڣ������ݻش��������
            Intent data = new Intent();
            data.putExtra("text", result.getText());
            setResult(RESULT_OK, data);
            finish();
        }
    }

**3\. ������д��������������ص����ݣ�**

    
    
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
                mTextView.setText(data.getStringExtra("text")); // ��ʾʶ�𵽵�����
                mWebView.loadUrl(data.getStringExtra("text")); // ��ʶ������ݵ�����ַ���ص�WebView
            }
        }
    }

**4\. �������ͷ�ͷ�������Ȩ�ޣ�**

    
    
    <uses-permission android:name="android.permission.CAMERA"/>
    <uses-permission android:name="android.permission.INTERNET"/>



ʹ�����������ķ����ǳ��򵥣�ֻ��Ҫ��һ��ZXingScannerView��Ϊ������Ĳ��֣�Ȼ�����ý����ɹ��Ļص��ӿڣ�ʵ�ֻص��ķ��������ݻش���������ɡ�

��Ȼ���������Ҫһ���Զ���ɨ������Ч������û����ô���ˡ�



