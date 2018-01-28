# Android开发学习之路-该怎么学Android（Service和Activity通信为例）

在大部分地方，比如书本或者学校和培训机构，教学Android的方式都基本类似，就是告诉先上原理方法，然后对着代码讲一下。

但是，这往往不是一个很好的方法，为什么？

① 学生要掌握这个方法的用途，只能通过记忆而不是理解

② 当某些原理稍微复杂的时候，通过讲解是不能直接理解的，有时候下课回去了再看也不一定看得明白

③ 对英语文档不够重视，有问题先百度

本鸟自学Android一年，慢慢也学习到了很多的方法，如果你也是一个入门不久但是觉得很多东西都不明白的新手，希望本文对你有帮助。

我觉得要想学好Android有几个重要的难点（注意这里不是四大组件也不是什么框架），就是： **英语、文档和Google。**

![](http://images2015.cnblogs.com/blog/852227/201605/852227-20160511214538499-322464643.png)

**假设我们要写一个后台服务，在一个Activity中要控制这个服务，也就是要执行服务中的方法。当你知道这个问题之后你会怎么去解决它？**

我相信有很多人会直接就就翻书本看，或者百度一下看看别人的代码是怎么写的然后复制粘贴完成任务。很多人都是这么解决问题的，为什么？因为在课堂上看到了这些代码，但不一定记住了，所以知道课本或者百度会有答案。

看书本和百度有什么缺点么？

① 百度各种乱入的推广，搜索技术问题会给你培训推广。所以百度的唯一用处基本就是查看自家网络是否正常了。

② 看书本不是不好，但是书本始终是别人的，入门的话看看书本是有好处的，但是当需要解决问题的时候，书本不应该是第一想到的帮手。

③ 直接给出的代码可能不会引起你注意一些细小的地方，很多时候，就是这些细小的地方导致我们卡在某个地方，到时候就只能怪当时没有弄明白了。



**在开始编码之前，有几个重要的问题我们要先解决：**

**① 找到可用的翻墙工具或者VPN，保证自己可以翻墙，因为无论是Google还是SDK升级还是Android Studio升级都需要**

**② 在SDK Manager中下载最新的API文档，没有这个你都可以不用学Android开发了，如下图：**

![](http://images2015.cnblogs.com/blog/852227/201605/852227-20160511221330968-361842294.png)

**③ 打开Android Studio的文档提示，在Eclipse中鼠标放在方法名上会有文档提示，在AS中需要手动打开一下，如下图：**

![](http://images2015.cnblogs.com/blog/852227/201605/852227-20160511221534234-1618245722.png)

**④ 准备好你的翻译工具**



开始编码：

① 我们都知道，要绑定一个服务，是要通过Context.bindService方法来做到的，如下图

![](http://images2015.cnblogs.com/blog/852227/201605/852227-20160511221856030-1921197503.png)

可以看到，这个方法需要三个参数，但是这三个参数有代表什么呢，假设我们都不懂，没关系，我们不要百度，也不用翻书，直接把鼠标放在方法上，我们可以看到关于这个方法的文档，如下所示：

![](http://images2015.cnblogs.com/blog/852227/201605/852227-20160511222028437-1086707496.png)

我们可以大致看看这个方法的介绍，明白它有什么用就可以了，比如当我们没有获得绑定服务的权限的时候会抛出异常，方法不能被广播接收者调用等等。这里我们要注意的是Parameters和Returns这两个地方。

Parameters指出了方法需要的参数分别是什么意思，有何要求，对于这个方法需要三个参数分别是Intent、ServiceConnection和flags：

service：一个可以被识别且要连接到的服务意图，意图可以由服务类名实例化或者逻辑描述实例化（使用action或者catagory等等）

conn：用来接收服务启动或者停止的信息，必须要实现

flags：一个配置标记，可以是上面这些值，意思也很明了

Returns指出调用这个方法会返回的值，这里如果绑定成功会返回true，否则返回false

② 意图我们是很容易构建出来的，不外乎写一个类并继承Service，然后通过显式或者隐式调用的方法构建一个Intent对象，这里代码暂时省略

③ 我们可以看到ServiceConnection的文档如下：

![](http://images2015.cnblogs.com/blog/852227/201605/852227-20160511223320905-626912979.png)

可以看到ServiceConnection是一个接口，最后一句表示：跟系统中许多的回调一样，子类的方法都会在主线程被调用，这里我们知道，我们不能给接口new一个对象，传入bindService只能是接口的实现类，那么就需要我们写一个类，来实现这个接口，代码如下：

    
    
    private class MyServiceConnection implements ServiceConnection {
            @Override
            public void onServiceConnected(ComponentName name, IBinder service) {
                
            }
    
            @Override
            public void onServiceDisconnected(ComponentName name) {
    
            }
    }

 我们可以看到这个接口中有两个方法需要我们实现，那么这两个方法有时什么意思呢，我们先看onServiceConnected，文档如下：

![](http://images2015.cnblogs.com/blog/852227/201605/852227-20160511223809765-790775113.png)

可以看到，方法是在服务连接成功的时候回调的，并且回调时会把一个IBinder的子类返回来，在参数中我们也看到了这个信息，文档还提示我们可以通过IBinder来调用服务的方法。

④ 我们回到服务的代码中

    
    
    public class MyService extends Service {
        private static final String TAG = "MyService";
    
        @Nullable
        @Override
        public IBinder onBind(Intent intent) {
            return new MyBinder();
        }
    
        public void Method(){
            Log.d(TAG, "Method() called" + "");
        }
    
    }

Service类中有一个抽象方法onBind，我们需要实现，并且我们在服务中添加了一个叫做Method的方法，用来表示我们需要在Activity中调用的方法。接下来我们看看onBind的文档：

![](http://images2015.cnblogs.com/blog/852227/201605/852227-20160511224330109-1592543938.png)

文档指出，我们在Activity中获得的IBinder是在这个方法中返回的，既然这样，我们就可以尝试在Service中（为什么要在这里？）定义一个IBinder的子类，但是我们发现，IBinder接口中有很多方法需要实现，在文档中我们也看到其实官方不推荐我们之间实现这个接口，而应该继承IBinder的子类Binder：

![](http://images2015.cnblogs.com/blog/852227/201605/852227-20160511225223265-1552276263.png)

完成之后，我们直接在onBind方法中返回一个Binder的子类，并且在Binder子类中新建方法calllMethod来调用服务的Method方法，所以整个Service的方法如下：

    
    
    public class MyService extends Service {
        private static final String TAG = "MyService";
    
        public class MyBinder extends Binder{
            public void callMethod(){
                Method();
            }
        }
    
        @Nullable
        @Override
        public IBinder onBind(Intent intent) {
            return new MyBinder();
        }
    
        public void Method(){
            Log.d(TAG, "Method() called" + "");
        }
    
    }

⑤
因为这个时候，Service中的Binder对象已经被回调方法传回了Activity中，所以我们可以在onServiceConnected中获取得到，但是注意获取到的是IBinder类型，我们需要强制类型转换一下：

    
    
    @Override
    public void onServiceConnected(ComponentName name, IBinder service) {
         mBinder = (MyService.MyBinder) service;
    }

获取到这个Binder之后，我们就可以调用里面的方法了。



**官方文档是开发最好的指导，可能你会觉得都是英文很难看懂，看到速度没有百度一下来得爽快，但是要知道，我们掌握的技术如果要等到有中文的时候才能掌握，那你就不应该选择IT行业了。而且如果不刻意锻炼提高自己的英语水平，那你是基本告别GitHub、StackOverflow等等有用的网站了。上面的例子其实并没有提到Google，但是，如果你试过被一个问题弄到崩溃的边缘，但是把问题Google一下之后，发现Stackoverflow中已经有人遇到并且有大神回答了帮了你好一大个忙的时候，你就会明白为什么我不用百度了。其实道理也很简单，Google是全世界开发者共同使用的，而百度，是部分中国开发者才会使用的！**

最后说一下这个英语，其实本人英语也不是多好，有时候某些单词还是得靠翻译。但是，只要你开始强迫自己去阅读英语文档，慢慢的你会发现，有时候我们都不知道文档内容的中文意思，但是却明白文档要我们怎么做，这个就是我们需要的最好的状态，不要被母语束缚了，有心学什么都不难。

