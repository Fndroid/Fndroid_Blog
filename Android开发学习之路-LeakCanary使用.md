# Android����ѧϰ֮·-LeakCanaryʹ��

LeakCanary��һ���ڴ�й©���⣬�����������ǵ�Ӧ�÷����ڴ�й©��ʱ�򷢳����ѣ����Ѱ���֪ͨ��Log��[GitHub](https://github.com/square/leakcanary)

�����ʹ�������Ƚϼ򵥣�

�����������

    
    
    1  dependencies {
    2    debugCompile 'com.squareup.leakcanary:leakcanary-android:1.5'
    3    releaseCompile 'com.squareup.leakcanary:leakcanary-android-no-op:1.5'
    4    testCompile 'com.squareup.leakcanary:leakcanary-android-no-op:1.5'
    5  }

���Զ���Application

    
    
     1 public class MyApplication extends Application {
     2     @Override
     3     public void onCreate() {
     4         super.onCreate();
     5         if (LeakCanary.isInAnalyzerProcess(this)) {
     6             return;
     7         }
     8         LeakCanary.install(this);
     9     }
    10 }

�����Ϳ����ˡ�

��������ͨ���򵥵����ӿ�������Ч�������Ƕ�֪�����ڴ�й©�Ƚ����׷����ģ�������Ϊ�������ڲ�ƥ�䵼�µġ�Android�е�����������ض��������ڵģ�������Щ����д����Ų����ͷŵı���ʱ��������������ڱ������쳣�������޷���GC�ͷš�

�����һ���򵥵����ӣ�

    
    
     1 public class SecondActivity extends AppCompatActivity {
     2     static Demo sDemo;
     3 
     4     @Override
     5     protected void onCreate(Bundle savedInstanceState) {
     6         super.onCreate(savedInstanceState);
     7         setContentView(R.layout.activity_second);
     8         if (sDemo == null) {
     9             sDemo = new Demo();
    10         }
    11         finish();
    12     }
    13 
    14     class Demo {
    15     }
    16 
    17 }

���Activity�У�����һ����̬��Demoʵ�����������ʵ����Activity��ʼ����ʱ��Ҳ�����˳�ʼ�������������ڳ�ʼ����Ϻ�finish�����Activity��

��ΪsDemo��һ����̬�ı������Ҳ�Ϊnull������GC���Ὣ��������Activity��Ϊ���������̬��������������Ҳ��������ִ�У��������Activity�ͱ�й©�ˡ�

������MainActivity�д����Activity������Ӧ�á�

ģ������״̬�������һ��ͼ�꣬���Ǵ򿪿��Եõ����½��棺

![](http://images2015.cnblogs.com/blog/852227/201610/852227-20161012100609234-1023375460.png)

���Կ����������Ƿ�����һ����SecondActivity��ʵ����й©�ˡ�

ʵ���ϣ�LeakCanary���˻��ڽ�������ʾй©��Ϣ֮�⣬Log��Ҳһ�������й©�ľ�����Ϣ��

![](http://images2015.cnblogs.com/blog/852227/201610/852227-20161012101131765-5931314.png)

����׷��й©�ĵط��Ͳ����ˡ�

