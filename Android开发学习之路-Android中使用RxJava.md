# Android����ѧϰ֮·-Android��ʹ��RxJava

RxJava�ĺ������ݺܼ򵥣����ǽ����첽������������Handler��AsyncTask�Ĺ��ܣ������ڴ���ṹ�ϲ�ͬ��

RxJavaʹ���˹۲���ģʽ�ͽ�����ģʽ�е���ʽ���ã�������C#��LINQ����

�۲���ģʽ��Observable�����۲��ߣ���Observer���۲��ߣ����ģ�Subscribe��֮��Observable�ڷ�����Ϣ��ʱ���֪ͨ��Ӧ��Observer�����ң�һ��Observable�����б����Observer���ġ�

��ʽ���ã���Builderģʽ���ƣ����ö�Ӧ�ķ�����ԭ������д���󷵻�ԭ���󣬴Ӷ�������ʽ���á�

* * *

 �������һЩ������õ������ʣ�

  1. Observable�����۲��ߣ�Ҳ������Ϣ�ķ�����
  2. Observer���۲��ߣ���Ϣ�Ľ�����
  3. Subscriber�������ߣ��۲��ߵ���һ�ֱ�ʾ
  4. Scheduler���������������߳��л�

* * *

�ȴӼ�����ģ��һ��������̣�

�������������汾Ϊ��ǰ���£���

    
    
    dependencies {
        ...
        compile 'io.reactivex:rxjava:1.1.8'
        compile 'io.reactivex:rxandroid:1.2.1'
    }

�����ڹ��̵�onCreate������ֱ�Ӽ������´��룺

    
    
     1         Observable<String> observable = Observable.create(new Observable.OnSubscribe<String>() {
     2             @Override
     3             public void call(Subscriber<? super String> subscriber) {
     4                 subscriber.onNext("hello");
     5                 subscriber.onNext("android");
     6                 subscriber.onCompleted();
     7             }
     8         });
     9 
    10         Subscriber<String> subscriber = new Subscriber<String>() {
    11             @Override
    12             public void onCompleted() {
    13                 Log.d(TAG, "onCompleted() called");
    14             }
    15 
    16             @Override
    17             public void onError(Throwable e) {
    18                 Log.d(TAG, "onError() called with: e = [" + e + "]");
    19             }
    20 
    21             @Override
    22             public void onNext(String s) {
    23                 Log.d(TAG, "onNext() called with: s = [" + s + "]");
    24             }
    25         };
    26 
    27         observable.subscribe(subscriber);

1-8�д�����һ��Observable��������������ͨ��create����������������һ��OnSubscribe��ʵ���࣬������onCall�����е���subscriber��onNext����������Ϣ�ķ��ͣ��ֱ��������ַ���Ȼ�����onComplete������ʾ������ɡ�

10-25�д�����һ��Subscriber�������ߣ������������Observer�����࣬Observer�Ǵ�ͳ�Ĺ۲��ߣ���Subscriber��ḻ��Observer��Subsciber����������������ֱ��ǣ�

  * onStart����Subscriber��Observable����֮����Ϣ����֮ǰ���ã�Ҳ��������onNext��������
  * unsubscirbe��������Ĺ�ϵ��Subscriber�����յ���Observable���͵���Ϣ

�������Ҫ�õ�������������ֱ�Ӵ���ObserverҲ�ǿ��Եģ������ķ�������Subscriber��

27�е�����Observable��subscribe������Subscriber���а󶨡�

���г�����ӡ���µ�Log��

![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160811183426574-1566678882.png)

 ��򵥵����̾��������������������еľֲ��������Ǳ���Ļ���������Ա��������ʾ��

    
    
            Observable.create(new Observable.OnSubscribe<String>() {
                @Override
                public void call(Subscriber<? super String> subscriber) {
                    subscriber.onNext("hello");
                    subscriber.onNext("android");
                    subscriber.onCompleted();
                }
            }).subscribe(new Subscriber<String>() {
                @Override
                public void onCompleted() {
                    Log.d(TAG, "onCompleted() called");
                }
    
                @Override
                public void onError(Throwable e) {
                    Log.d(TAG, "onError() called with: e = [" + e + "]");
                }
    
                @Override
                public void onNext(String s) {
                    Log.d(TAG, "onNext() called with: s = [" + s + "]");
                }
            });

Ч������ȫһ���ģ����ſ��Կ������ף�����������ʽ��Ƚϳ�����



����Ҫ����������----Actionx��Funcx��������������xΪ���֣���

Actionx��ָ����һ����������ڼ�Subscriber���������������x�����������ҷ���Void

Funcx��ָ����һ�鷽�������ڶ���Ϣ���д�������ı任��ʹ�õ��������������x��������1������ֵ

���ﻹ�Ǻ���������һ������������ֻ����Subscriber��onNext��������������onCompleted��onError��������ô��һ��Subscriber�Ϳ��Ա��滻Ϊһ��Action1���������£�

    
    
            Observable.create(new Observable.OnSubscribe<String>() {
                @Override
                public void call(Subscriber<? super String> subscriber) {
                    subscriber.onNext("hello");
                    subscriber.onNext("android");
                    subscriber.onCompleted();
                }
            }).subscribe(new Action1<String>() {
                @Override
                public void call(String s) {
                    Log.d(TAG, "call() called with: s = [" + s + "]");
                }
            });

 �������ֻ����onNext��onError�������������Action1����ʾ����Ϊsubscribe����������������ô�����������г��ԡ�

![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160811185340574-1429379544.png)

* * *



��ΪRxJava��������ʵ�Ƚ϶࣬����Ͷ�����һЩ���н��ܣ�����Ȥ�Ŀ���ת���������ĵط������һЩ�ο����¡�

**�� ��������**

�����Ĵ��������м��֣�Create��From��Just��Interval�������������Բο���ĩ���£�

�����������ֱ��ʹ����create��������Observable�Ĵ������������ͨ��just�����м򵥵Ĵ������������£�

    
    
    1         Observable.just("hello", "world").subscribe(new Action1<String>() {
    2             @Override
    3             public void call(String s) {
    4                 Log.d(TAG, "call() called with: s = [" + s + "]");
    5             }
    6         });

�������е�Ч����һ���ģ���������Ҫע���ˣ�just�������Դ����κ����͵Ķ��󣬵��Ǳ����Action1����Subscriber��ָ���ķ���һ�¡�

Ҳ���Ǽ�������Ҫ��ӡ�Ĳ����ַ����������֣�������������ʾ��

    
    
    1         Observable.just(1, 2).subscribe(new Action1<Integer>() {
    2             @Override
    3             public void call(Integer s) {
    4                 Log.d(TAG, "call() called with: s = [" + s + "]");
    5             }
    6         });

�� **Action1��ָ���ķ�����String��ΪInteger��call�����Ĳ���Ҳ��Ӧ�ĸ��ġ�**

from����Ҳ���Դ���Observable���÷��������г��ԡ�

����˵һ��Interval������Android�����п��ܻ��õ���Interval�Ƕ�ʱ����ÿ��һ��ʱ�䷢��һ����Ϣ�������Ϣ��һ��Long���͵�ֵ��ʾ����0��ʼ�����룺

    
    
    1         Observable.interval(1, TimeUnit.SECONDS).subscribe(new Action1<Long>() {
    2             @Override
    3             public void call(Long aLong) {
    4                 Log.d(TAG, "call() called with: s = [" + aLong + "]");
    5             }
    6         });

���д���֮��Logcat��ÿ��һ���ӡһ��Log����aLong��ֵ���0��ʼÿ�ε���1��interval�����Ĳ����ֱ��ǣ����ֵ�������λ���������ָ����ÿ��1s����һ�Ρ�

���м�ʱ�������У�������ô�����Զ�ֹͣ�أ�����˵��ӡ5�Ρ������漰��RxJava�Ĺ������⣬����ʹ��take���������뷢�ʹ����������ʹ����������ֵ��ʱ����������Ϣ���������ˣ����ǵ�Subscriber���ٽ��ա��������£�

    
    
    1         Observable.interval(1, TimeUnit.SECONDS).take(5).subscribe(new Action1<Long>() {
    2             @Override
    3             public void call(Long aLong) {
    4                 Log.d(TAG, "call() called with: s = [" + aLong + "]");
    5             }
    6         });

�� **�����take����������subscribe��������ã���Ϊsubscribe��������ֵ������Observable**

��Ȼ��take���������������أ��������г��ԡ�



**�� �任����**

�任������Ҫ�Ƕ�Observable���͵���Ϣ���б任��ʹ����Subscriber�д����������Ӽ򵥡���ͨ�������������˵���ɣ������趨�˵ļ�ʱ���У�ÿ�η��ظ�Subscriber��Action1����ֵ��Long���͵ģ���������Ҫ�ڽ��ն˵õ�һ��Integer���͵�ֵ����ô���أ���Ҫʹ�ñ任����õı任����map��

    
    
     1         Observable.interval(1, TimeUnit.SECONDS).take(5).map(new Func1<Long, Integer>() {
     2             @Override
     3             public Integer call(Long aLong) {
     4                 return aLong.intValue();
     5             }
     6         }).subscribe(new Action1<Integer>() {
     7             @Override
     8             public void call(Integer integer) {
     9                 Log.d(TAG, "call() called with: s = [" + integer + "]");
    10             }
    11         });

���Կ������ʱ��Action1ʹ�õķ��ͱ�ΪInteger������Long����Ϊ������subscribe����֮ǰ������map��������Ϣ���б任�����Կ������任��Ҫ����һ��Funcx�Ķ��󣬲�ʵ��һ��call������Long���͵�����ת��ΪInteger���͡�ϣ���㻹�ǵ�Func1��ʾ����һ��������һ������ֵ��

������˵һ���Ƚϳ��õģ�������flatmap���������ã��ǰ�һ���������ͱ��һ��Observable����Ҳ����˵Observable�������Observable�����½�������Observable�е���Ϣ�ύ����ԭ����Observable���з��͡���������ף����Կ����ӣ�

�����������࣬һ����ѧ����������ѡ�Σ���һ���ǿγ̣���������ѧ�����е�ѡ�����������ͣ�Ҫ���Ǵ�ӡ����ѧ����ÿ��ѡ�γɼ���

    
    
        private class Student{
            String name;
            Course[] courses;
            Student(String name, Course[] courses) {
                this.name = name;
                this.courses = courses;
            }
        }
    
        private class Course{
            int score;
            private Course(int score) {this.score = score;}
        }

�������������������������£�

    
    
     1         Student s1 = new Student("Jack", new Course[]{new Course(80), new Course(79)});
     2         Student s2 = new Student("Rose", new Course[]{new Course(87), new Course(69)});
     3 
     4         Observable.just(s1, s2).subscribe(new Action1<Student>() {
     5             @Override
     6             public void call(Student student) {
     7                 for (int i = 0; i < student.courses.length; i++) {
     8                     Log.d(TAG, "score = " + student.courses[i].score);
     9                 }
    10             }
    11         });

�����ڽ��ն˽��յ�һ��Student���͵�ֵ����ʹ��forѭ����ӡ��Ӧ���Ե�ֵ��

����������ǲ���ʹ��forѭ��������ϣ��ֱ���ٽ��ն˾Ϳ��Դ����Ӧ�ĳɼ�����Ҫ�õ�flatmap���������£�

    
    
     1         Observable.just(s1, s2).flatMap(new Func1<Student, Observable<Course>>() {
     2             @Override
     3             public Observable<Course> call(Student student) {
     4                 return Observable.from(student.courses);
     5             }
     6         }).subscribe(new Action1<Course>() {
     7             @Override
     8             public void call(Course course) {
     9                 Log.d(TAG, "score = " + course.score);
    10             }
    11         });

��
Observable�ķ��͸�Ϊ����Ҫת��������Course����ʾ���ǰ�����Student�����ÿ��Course���ͳ�ȥ����Func1�е�call�����и��ݴ����Student�����courses���Թ���һ��Observable�����أ�RxJava������ǰ�ÿ��Course������ͳ�ȥ��log������ʾ��

![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160811195353968-534405772.png)

�����������û��ǲ����������ÿ���ɼ����Integer�ٷ��ͣ�����ô����

���ǿ����ڷ���Observable֮ǰ�ٽ���һ��mapת�������ʱ��õ��ľ���Integer�ˣ��ǵø���Observable��Ӧ�ķ���ΪIntegerŶ���������£�

![](http://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif)![](http://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif)

    
    
     1         Observable.just(s1, s2).flatMap(new Func1<Student, Observable<Integer>>() {
     2             @Override
     3             public Observable<Integer> call(Student student) {
     4                 return Observable.from(student.courses).map(new Func1<Course, Integer>() {
     5                     @Override
     6                     public Integer call(Course course) {
     7                         return course.score;
     8                     }
     9                 });
    10             }
    11         }).subscribe(new Action1<Integer>() {
    12             @Override
    13             public void call(Integer score) {
    14                 Log.d(TAG, "score = " + score);
    15             }
    16         });

View Code

�任������������һЩ�����������˽Ⳣ�ԡ�



**�� ���˲���**

������ļ�ʱ���������������Ѿ�ʹ���˹��˲�����take���ǱȽϼ򵥵Ĺ����ˡ�����򵥽���һ��debounce�������������ǹ��˵�һ��ʱ���ڵ���Ϣ��

![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160811201136593-1468323909.png)

�趨��debounce֮����һ����Ϣ����֮���һ��ʱ��֮�ڷ��͵���Ϣ���ᱻ����������������û�뵽���õ����ӣ�ԭ���ң�

    
    
    1         Observable.interval(1, TimeUnit.SECONDS).debounce(2, TimeUnit.SECONDS)
    2                 .subscribe(new Action1<Long>() {
    3             @Override
    4             public void call(Long aLong) {
    5                 Log.d(TAG, "call() called with: aLong = [" + aLong + "]");
    6             }
    7         });

���Կ����������õĶ�ʱ��ÿ��1s����һ����Ϣ�����ָ����趨��debounceΪ2s��ʲô��˼�أ����Ǽ��С��2s����Ϣ����������������δ���û���κ������

* * *



ʵ���ϣ�RxJava��������һЩ�����������Ⱥ��ԡ� **˵һЩAndroid�����йص�** ��

**���ǵ��첽����һ��������������������ģ����߽��к�ʱ��������ô���ʱ��϶����������߳��н��У���RxJava��һ���ŵ��ǣ����ǿ���������л��̣߳����ң��л������ǳ����㡣**

Ϊ����֤���ڵ��̣߳������ȱ�дһ����������ȡ��ǰ�̵߳�id��

    
    
    1     private long getCurrentThreadId() {
    2         return Thread.currentThread().getId();
    3     }

������������֤һ���������Ϣ�ķ��ͺͽ��գ�

    
    
     1         Observable.create(new Observable.OnSubscribe<String>() {
     2             @Override
     3             public void call(Subscriber<? super String> subscriber) {
     4                 Log.d(TAG, "Thread id of sending message is " + getCurrentThreadId());
     5                 subscriber.onNext("hello");
     6             }
     7         }).subscribe(new Action1<String>() {
     8             @Override
     9             public void call(String s) {
    10                 Log.d(TAG, "Thread id of receiving message is " + getCurrentThreadId());
    11             }
    12         });

��δ���ܼ򵥣��ֱ��ڷ��ͺͽ��յĵط���ӡ��ǰ���߳�id��log���£�

![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160811202221453-46612809.png)

��ô������л��߳��أ�����Ҫ�õ�����������

  * subscribeOn��ָ���¼��������߳�
  * observeOn�� ָ���¼����յ��߳�

��������������Ҫ����Scheduler���󣬶�����������ͨ��Schedulers��AndroidSchedulers����ȡ��

  * Scheduler.newThread()��ʼ�տ����µ��߳�
  * Scheduler.immediate()����ǰ�̣߳�Ĭ�ϣ�
  * Scheduler.io()��IO�̣߳����������أ��ļ���д
  * Scheduler.computation()�������̣߳���ͼ�μ����
  * AndroidSchedulers.mainThread()��Android�е�UI�߳�
  * AndroidSchedulers.from(Looper looper)������Looper����ȡ��Ӧ���̣߳�������Ը���Handler��ȡ��Handler��Ӧ���߳�

���������Ҫ���µ��߳��н�����Ϣ���ͣ��������أ�����UI�߳��и���UI����ô����Ӧ��������ʾ��

    
    
     1         Observable.create(new Observable.OnSubscribe<String>() {
     2             @Override
     3             public void call(Subscriber<? super String> subscriber) {
     4                 Log.d(TAG, "Thread id of sending message is " + getCurrentThreadId());
     5                 subscriber.onNext("hello");
     6             }
     7         })
     8         .subscribeOn(Schedulers.io())
     9         .observeOn(AndroidSchedulers.mainThread())
    10         .subscribe(new Action1<String>() {
    11             @Override
    12             public void call(String s) {
    13                 Log.d(TAG, "Thread id of receiving message is " + getCurrentThreadId());
    14             }
    15         });

���ʱ��Log������ʾ��

![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160811203606406-302862568.png)

���Կ������������������Ѿ����ڲ�ͬ�߳��ˡ�

����п��ҵ���һƪ���£���������Ҫ���и�˹ģ���ļ��㣬���������̿��ܻ���Ҫһ��ʱ�䣬��������ý��濨���ǽ��鿪���µ��߳̽��еģ����������ÿ����õ���������Computation���ǲ��Ǻܷ����ء�



�ο����£�

  1. [ReactiveX/RxJava�ĵ����İ�](https://mcxiaoke.gitbooks.io/rxdocs/content/)
  2. [��Android�����ߵ�RxJava���](http://gank.io/post/560e15be2dca930e00da1083#toc_18)

