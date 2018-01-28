# Android开发学习之路-EventBus使用

EventBus是一个通过发布、订阅事件实现组件间消息传递的工具。

它存在的目的，就是为了优化组件之间传递消息的过程。传统组件之间传递消息的方法有使用广播，回调等，而这些方法使用都比较复杂。

工作原理：

![](http://images2015.cnblogs.com/blog/852227/201609/852227-20160926222551641-1282725246.png)

依赖：

    
    
    1 dependencies {
    2     compile 'org.greenrobot:eventbus:3.0.0'
    3 }

**注：EventBus是事件-订阅模型，实际上事件就是消息，订阅就是接收，本文不会很严格区分，方便理解为主！**

_1\. 从简单的入手：充当Handler_

既然能发送消息，那么自然在同一个组件下也能进行消息的发送和接收，也就是Handler的职责。

先定义一个事件的对象：

    
    
     1 public class MessageEvent {
     2     private String message;
     3 
     4     public MessageEvent(String message) {
     5         this.message = message;
     6     }
     7 
     8     public String getMessage() {
     9         return message;
    10     }
    11 
    12     @Override
    13     public String toString() {
    14         return message;
    15     }
    16 }

这个事件对象只需要是Object的子类，没其他要求，消息的内容可以随意定，这里用一个String表示事件的消息，toString直接输出事件的内容。

在需要接收消息的组件中，进行EventBus的绑定，这里由Activity充当订阅者（Subscriber），也就是消息接收者。

    
    
     1 public class MainActivity extends AppCompatActivity {
     2 
     3     @Override
     4     protected void onCreate(Bundle savedInstanceState) {
     5         super.onCreate(savedInstanceState);
     6         setContentView(R.layout.activity_main);
     7     }
     8 
     9     @Override
    10     protected void onStart() {
    11         super.onStart();
    12         EventBus.getDefault().register(this); // 将当前Activity绑定为订阅者
    13     }
    14 
    15     @Override
    16     protected void onStop() {
    17         EventBus.getDefault().unregister(this); // 解绑
    18         super.onStop();
    19     }
    20 
    21     // 声明一个订阅方法，用于接收事件
    22     @Subscribe
    23     public void onEvent(MessageEvent messageEvent) {
    24         Log.d(TAG, "onEvent() called with: messageEvent = [" + messageEvent + "]");
    25     }
    26 
    27 }

**通过EventBus.getDefault方法获取到一个EventBus的一个单例，也就是每次调用这个方法得到的都是同一个EventBus对象。而不同的EventBus对象消息不会互通。**

接着需要在Activity的onStart方法中进行对EventBus对象的绑定，在onStop方法中进行解绑。同时，需要定义用于接收事件的方法，并加上@Subscribe注解表明该方法用于接收订阅事件。

接着，我们需要发送事件：

    
    
    1 EventBus.getDefault().post(new MessageEvent("I m Fndroid"));

这里获取的默认的EventBus对象，通过post方法进行事件的发送，log如下：

![](http://images2015.cnblogs.com/blog/852227/201609/852227-20160927114403578-1116215491.png)



_2\. 组件间通讯：代替广播、回调等_

EventBus的存在，并不是为了替代Handler，而是用来进行组件间的通讯。有了上面的知识，接下来就不难了。

我们都知道，只要是同一个EventBus对象绑定的组件（Subscriber），消息是互通的，也就是我们可以在Service中向Activity发送事件进行通讯。（其他组件类似）

定义一个IntentService，并且发送一个事件，而Activity，和上面一样。

    
    
     1 public class MyIntentService extends IntentService {
     2 
     3     public MyIntentService() {
     4         super("MyIntentService");
     5     }
     6 
     7     @Override
     8     protected void onHandleIntent(Intent intent) {
     9         EventBus.getDefault().post(new MessageEvent("From service"));
    10     }
    11 
    12 }

在Activity中开启服务：

    
    
    1 startService(new Intent(this, MyIntentService.class));

Log：

![](http://images2015.cnblogs.com/blog/852227/201609/852227-20160927115150219-408652736.png)

组件间的通讯变得如此简单了。



_3\. 进阶：持久事件、线程模式、优先级_

**① 持久事件（StickyEvent）**
：对于调用post方法发送的普通消息，会在第一次被接收到的时候被消费掉，也就是我们在@Subscribe方法下处理完了，这个消息就消失了。而EventBus则提供了另一种类型的消息
----
StickyEvent，这个消息，会被保存在RAM中，直到下一个StickyEvent被发送才会被替换。每个EventBus对象都能通过geStickyEvent方法获取最近发出的持久事件。

我们对Activity稍作修改，在订阅方法中，把当前的持久消息打印出来：

    
    
    1     // 声明一个订阅方法，用于接收事件
    2     @Subscribe
    3     public void onEvent(MessageEvent messageEvent) {
    4         Log.d(TAG, "onEvent() called with: messageEvent = [" + messageEvent + "]");
    5         Log.d(TAG, "onEvent: Sticky Event = [" + EventBus.getDefault().getStickyEvent(MessageEvent.class) + "]");
    6     }

接着对Service也进行修改，令其发送一个StickyEvent：

    
    
    1     @Override
    2     protected void onHandleIntent(Intent intent) {
    3         EventBus.getDefault().post(new MessageEvent("From service"));
    4         EventBus.getDefault().postSticky(new MessageEvent("From service2"));
    5         EventBus.getDefault().post(new MessageEvent("From service3"));
    6     }

这里看到，在第二次发送的时候，调用了postSticky方法发送事件，这就表明了该事件是一个持久事件，除非有新的同类型（不同类型的事件可以共存）持久事件被发送，否则会一直被保存在内存中，我们任何时候都能获取得到。

Log：

![](http://images2015.cnblogs.com/blog/852227/201609/852227-20160927121742438-1419476863.png)

可以看到，在第三次接收到消息的时候，将StickyEvent打印出来，依旧是"From service2"。

同样的，如果我们想要移除此持久事件，可以调用EventBus的removeStickyEvent方法来实现，如果要移除所有类型的持久事件，可以调用removeAllStickyEvent方法。

**② 线程模式**

EventBus允许我们对订阅者进行线程设置， **默认情况下，订阅是和事件发送处于同一线程的** ，我们不妨把订阅的线程id打印出来看一看。

修改Activity中的@Subscribe方法：

    
    
    1     @Subscribe
    2     public void onEvent(MessageEvent messageEvent) {
    3         Log.d(TAG, "onEvent: Thread id = [" + Thread.currentThread().getId() + "]");
    4         Log.d(TAG, "onEvent() called with: messageEvent = [" + messageEvent + "]");
    5     }

修改Service，让其发送一次普通事件，并且输出当前线程id：

    
    
    1     @Override
    2     protected void onHandleIntent(Intent intent) {
    3         Log.d(TAG, "onHandleIntent: Thread id = [" + Thread.currentThread().getId() + "]");
    4         EventBus.getDefault().post(new MessageEvent("From service"));
    5     }

打印Log：

![](http://images2015.cnblogs.com/blog/852227/201609/852227-20160927122446531-948054374.png)

对于订阅者，有以下四种线程模式：

  1. **POSTING** ：默认模式，订阅和事件发送在同一线程下进行
  2. **MAIN** ：订阅在主线程下进行，可以直接修改UI
  3. **BACKGROUND** ：如果事件发送在主线程，则订阅在子线程下进行，如果事件发送在子线程，则订阅也在该子线程中进行
  4. **ASYNC** ：如果事件发送在主线程，则订阅在子线程下进行，如果事件发送在子线程，则订阅会在其他子线程中进行

设置订阅者线程很简单，只需要在注解中赋值即可，如：

    
    
    1     @Subscribe(threadMode = ThreadMode.MAIN)

**③ 优先级**

每个组件中可以有多个订阅方法，而我们可以对它们设置不同的优先级，设置的方法也很简单：

    
    
    1     @Subscribe(priority = 88)

默认的优先级为0，更高优先级的订阅方法会更先收到事件，而每个订阅方法都可以对事件进行终止，可以通过以下方法停止事件传递：

    
    
    1    EventBus.getDefault().cancelEventDelivery(messageEvent);

**④ 自定义EventBus对象**

通过getDefault方法获取的是一个EventBus默认的单例，而我们可以通过EventBus.Builder来构造一个自定义的EventBus对象。

* * *



明白了用法以后，我们分析源码也会更加简单。



