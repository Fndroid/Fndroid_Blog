# Android����ѧϰ֮·-EventBusʹ��

EventBus��һ��ͨ�������������¼�ʵ���������Ϣ���ݵĹ��ߡ�

�����ڵ�Ŀ�ģ�����Ϊ���Ż����֮�䴫����Ϣ�Ĺ��̡���ͳ���֮�䴫����Ϣ�ķ�����ʹ�ù㲥���ص��ȣ�����Щ����ʹ�ö��Ƚϸ��ӡ�

����ԭ��

![](http://images2015.cnblogs.com/blog/852227/201609/852227-20160926222551641-1282725246.png)

������

    
    
    1 dependencies {
    2     compile 'org.greenrobot:eventbus:3.0.0'
    3 }

**ע��EventBus���¼�-����ģ�ͣ�ʵ�����¼�������Ϣ�����ľ��ǽ��գ����Ĳ�����ϸ����֣��������Ϊ����**

_1\. �Ӽ򵥵����֣��䵱Handler_

��Ȼ�ܷ�����Ϣ����ô��Ȼ��ͬһ�������Ҳ�ܽ�����Ϣ�ķ��ͺͽ��գ�Ҳ����Handler��ְ��

�ȶ���һ���¼��Ķ���

    
    
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

����¼�����ֻ��Ҫ��Object�����࣬û����Ҫ����Ϣ�����ݿ������ⶨ��������һ��String��ʾ�¼�����Ϣ��toStringֱ������¼������ݡ�

����Ҫ������Ϣ������У�����EventBus�İ󶨣�������Activity�䵱�����ߣ�Subscriber����Ҳ������Ϣ�����ߡ�

    
    
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
    12         EventBus.getDefault().register(this); // ����ǰActivity��Ϊ������
    13     }
    14 
    15     @Override
    16     protected void onStop() {
    17         EventBus.getDefault().unregister(this); // ���
    18         super.onStop();
    19     }
    20 
    21     // ����һ�����ķ��������ڽ����¼�
    22     @Subscribe
    23     public void onEvent(MessageEvent messageEvent) {
    24         Log.d(TAG, "onEvent() called with: messageEvent = [" + messageEvent + "]");
    25     }
    26 
    27 }

**ͨ��EventBus.getDefault������ȡ��һ��EventBus��һ��������Ҳ����ÿ�ε�����������õ��Ķ���ͬһ��EventBus���󡣶���ͬ��EventBus������Ϣ���ụͨ��**

������Ҫ��Activity��onStart�����н��ж�EventBus����İ󶨣���onStop�����н��н��ͬʱ����Ҫ�������ڽ����¼��ķ�����������@Subscribeע������÷������ڽ��ն����¼���

���ţ�������Ҫ�����¼���

    
    
    1 EventBus.getDefault().post(new MessageEvent("I m Fndroid"));

�����ȡ��Ĭ�ϵ�EventBus����ͨ��post���������¼��ķ��ͣ�log���£�

![](http://images2015.cnblogs.com/blog/852227/201609/852227-20160927114403578-1116215491.png)



_2\. �����ͨѶ������㲥���ص���_

EventBus�Ĵ��ڣ�������Ϊ�����Handler��������������������ͨѶ�����������֪ʶ���������Ͳ����ˡ�

���Ƕ�֪����ֻҪ��ͬһ��EventBus����󶨵������Subscriber������Ϣ�ǻ�ͨ�ģ�Ҳ�������ǿ�����Service����Activity�����¼�����ͨѶ��������������ƣ�

����һ��IntentService�����ҷ���һ���¼�����Activity��������һ����

    
    
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

��Activity�п�������

    
    
    1 startService(new Intent(this, MyIntentService.class));

Log��

![](http://images2015.cnblogs.com/blog/852227/201609/852227-20160927115150219-408652736.png)

������ͨѶ�����˼��ˡ�



_3\. ���ף��־��¼����߳�ģʽ�����ȼ�_

**�� �־��¼���StickyEvent��**
�����ڵ���post�������͵���ͨ��Ϣ�����ڵ�һ�α����յ���ʱ�����ѵ���Ҳ����������@Subscribe�����´������ˣ������Ϣ����ʧ�ˡ���EventBus���ṩ����һ�����͵���Ϣ
----
StickyEvent�������Ϣ���ᱻ������RAM�У�ֱ����һ��StickyEvent�����ͲŻᱻ�滻��ÿ��EventBus������ͨ��geStickyEvent������ȡ��������ĳ־��¼���

���Ƕ�Activity�����޸ģ��ڶ��ķ����У��ѵ�ǰ�ĳ־���Ϣ��ӡ������

    
    
    1     // ����һ�����ķ��������ڽ����¼�
    2     @Subscribe
    3     public void onEvent(MessageEvent messageEvent) {
    4         Log.d(TAG, "onEvent() called with: messageEvent = [" + messageEvent + "]");
    5         Log.d(TAG, "onEvent: Sticky Event = [" + EventBus.getDefault().getStickyEvent(MessageEvent.class) + "]");
    6     }

���Ŷ�ServiceҲ�����޸ģ����䷢��һ��StickyEvent��

    
    
    1     @Override
    2     protected void onHandleIntent(Intent intent) {
    3         EventBus.getDefault().post(new MessageEvent("From service"));
    4         EventBus.getDefault().postSticky(new MessageEvent("From service2"));
    5         EventBus.getDefault().post(new MessageEvent("From service3"));
    6     }

���￴�����ڵڶ��η��͵�ʱ�򣬵�����postSticky���������¼�����ͱ����˸��¼���һ���־��¼����������µ�ͬ���ͣ���ͬ���͵��¼����Թ��棩�־��¼������ͣ������һֱ���������ڴ��У������κ�ʱ���ܻ�ȡ�õ���

Log��

![](http://images2015.cnblogs.com/blog/852227/201609/852227-20160927121742438-1419476863.png)

���Կ������ڵ����ν��յ���Ϣ��ʱ�򣬽�StickyEvent��ӡ������������"From service2"��

ͬ���ģ����������Ҫ�Ƴ��˳־��¼������Ե���EventBus��removeStickyEvent������ʵ�֣����Ҫ�Ƴ��������͵ĳ־��¼������Ե���removeAllStickyEvent������

**�� �߳�ģʽ**

EventBus�������ǶԶ����߽����߳����ã� **Ĭ������£������Ǻ��¼����ʹ���ͬһ�̵߳�** �����ǲ����Ѷ��ĵ��߳�id��ӡ������һ����

�޸�Activity�е�@Subscribe������

    
    
    1     @Subscribe
    2     public void onEvent(MessageEvent messageEvent) {
    3         Log.d(TAG, "onEvent: Thread id = [" + Thread.currentThread().getId() + "]");
    4         Log.d(TAG, "onEvent() called with: messageEvent = [" + messageEvent + "]");
    5     }

�޸�Service�����䷢��һ����ͨ�¼������������ǰ�߳�id��

    
    
    1     @Override
    2     protected void onHandleIntent(Intent intent) {
    3         Log.d(TAG, "onHandleIntent: Thread id = [" + Thread.currentThread().getId() + "]");
    4         EventBus.getDefault().post(new MessageEvent("From service"));
    5     }

��ӡLog��

![](http://images2015.cnblogs.com/blog/852227/201609/852227-20160927122446531-948054374.png)

���ڶ����ߣ������������߳�ģʽ��

  1. **POSTING** ��Ĭ��ģʽ�����ĺ��¼�������ͬһ�߳��½���
  2. **MAIN** �����������߳��½��У�����ֱ���޸�UI
  3. **BACKGROUND** ������¼����������̣߳����������߳��½��У�����¼����������̣߳�����Ҳ�ڸ����߳��н���
  4. **ASYNC** ������¼����������̣߳����������߳��½��У�����¼����������̣߳����Ļ����������߳��н���

���ö������̺ܼ߳򵥣�ֻ��Ҫ��ע���и�ֵ���ɣ��磺

    
    
    1     @Subscribe(threadMode = ThreadMode.MAIN)

**�� ���ȼ�**

ÿ������п����ж�����ķ����������ǿ��Զ��������ò�ͬ�����ȼ������õķ���Ҳ�ܼ򵥣�

    
    
    1     @Subscribe(priority = 88)

Ĭ�ϵ����ȼ�Ϊ0���������ȼ��Ķ��ķ���������յ��¼�����ÿ�����ķ��������Զ��¼�������ֹ������ͨ�����·���ֹͣ�¼����ݣ�

    
    
    1    EventBus.getDefault().cancelEventDelivery(messageEvent);

**�� �Զ���EventBus����**

ͨ��getDefault������ȡ����һ��EventBusĬ�ϵĵ����������ǿ���ͨ��EventBus.Builder������һ���Զ����EventBus����

* * *



�������÷��Ժ����Ƿ���Դ��Ҳ����Ӽ򵥡�



