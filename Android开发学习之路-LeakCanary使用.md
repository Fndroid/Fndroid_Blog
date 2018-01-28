# Android开发学习之路-LeakCanary使用

LeakCanary是一个内存泄漏检测库，它可以在我们的应用发生内存泄漏的时候发出提醒，提醒包括通知和Log。[GitHub](https://github.com/square/leakcanary)

这个库使用起来比较简单：

①添加依赖：

    
    
    1  dependencies {
    2    debugCompile 'com.squareup.leakcanary:leakcanary-android:1.5'
    3    releaseCompile 'com.squareup.leakcanary:leakcanary-android-no-op:1.5'
    4    testCompile 'com.squareup.leakcanary:leakcanary-android-no-op:1.5'
    5  }

②自定义Application

    
    
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

这样就可以了。

我们这里通过简单的例子看看它的效果，我们都知道，内存泄漏比较容易发生的，就是因为生命周期不匹配导致的。Android中的组件都是有特定生命周期的，而当这些组件中存在着不可释放的变量时，组件的生命周期便会出现异常，导致无法被GC释放。

这里举一个简单的例子：

    
    
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

这个Activity中，存在一个静态的Demo实例，并且这个实例在Activity初始化的时候也进行了初始化，接着我们在初始化完毕后finish掉这个Activity。

因为sDemo是一个静态的变量并且不为null，所以GC不会将其清理，而Activity因为持有这个静态变量，生命周期也不能正常执行，这样这个Activity就被泄漏了。

我们在MainActivity中打开这个Activity，启动应用。

模拟器的状态栏会出现一个图标，我们打开可以得到如下界面：

![](http://images2015.cnblogs.com/blog/852227/201610/852227-20161012100609234-1023375460.png)

可以看到，和我们分析的一样，SecondActivity的实例被泄漏了。

实际上，LeakCanary除了会在界面中显示泄漏信息之外，Log中也一样会输出泄漏的具体信息：

![](http://images2015.cnblogs.com/blog/852227/201610/852227-20161012101131765-5931314.png)

这样追踪泄漏的地方就不难了。

