# Android开发学习之路-Android中使用RxJava

RxJava的核心内容很简单，就是进行异步操作。类似于Handler和AsyncTask的功能，但是在代码结构上不同。

RxJava使用了观察者模式和建造者模式中的链式调用（类似于C#的LINQ）。

观察者模式：Observable（被观察者）被Observer（观察者）订阅（Subscribe）之后，Observable在发出消息的时候会通知对应的Observer，并且，一个Observable可以有被多个Observer订阅。

链式调用：和Builder模式类似，调用对应的方法对原对象进行处理后返回原对象，从而做到链式调用。

* * *

 这里介绍一些下面会用到的名词：

  1. Observable：被观察者，也就是消息的发送者
  2. Observer：观察者，消息的接收者
  3. Subscriber：订阅者，观察者的另一种表示
  4. Scheduler：调度器，进行线程切换

* * *

先从简单入手模拟一下这个过程：

先配置依赖（版本为当前最新）：

    
    
    dependencies {
        ...
        compile 'io.reactivex:rxjava:1.1.8'
        compile 'io.reactivex:rxandroid:1.2.1'
    }

接着在工程的onCreate方法中直接加入如下代码：

    
    
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

1-8行创建了一个Observable对象，这个对象可以通过create方法来创建，传入一个OnSubscribe的实现类，并在其onCall方法中调用subscriber的onNext方法进行消息的发送，分别发送两个字符串然后调用onComplete方法表示发送完成。

10-25行创建了一个Subscriber（订阅者）对象，这个类是Observer的子类，Observer是传统的观察者，而Subscriber则丰富了Observer，Subsciber添加了两个方法，分别是：

  * onStart：在Subscriber和Observable连接之后，消息发送之前调用，也就是先于onNext方法调用
  * unsubscirbe：解除订阅关系，Subscriber不再收到从Observable发送的消息

如果不需要用到这两个方法，直接创建Observer也是可以的，创建的方法类似Subscriber。

27行调用了Observable的subscribe方法对Subscriber进行绑定。

运行程序会打印如下的Log：

![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160811183426574-1566678882.png)

 最简单的流程就是这样，如果上面代码中的局部变量不是必须的话，代码可以变成如下所示：

    
    
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

效果是完全一样的，相信可以看得明白，后面这种形式会比较常见。



接着要介绍两个类----Actionx和Funcx（这两个类最后的x为数字）。

Actionx：指的是一组操作（用于简化Subscriber），而这组操作有x个参数，并且返回Void

Funcx：指的是一组方法（用于对消息进行处理，下面的变换会使用到），这个方法有x个参数和1个返回值

这里还是和上面例子一样，假设我们只关心Subscriber的onNext方法，而不关心onCompleted和onError方法，那么这一个Subscriber就可以被替换为一个Action1，代码如下：

    
    
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

 如果我们只关心onNext和onError，则可以用两个Action1来表示，因为subscribe的重载允许我们这么做，可以自行尝试。

![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160811185340574-1429379544.png)

* * *



因为RxJava的内容其实比较多，这里就对其中一些进行介绍，有兴趣的可以转到文章最后的地方会给出一些参考文章。

**① 创建操作**

常见的创建操作有几种：Create、From、Just和Interval（还有其他可以参考文末文章）

上面的例子中直接使用了create方法进行Observable的创建，下面可以通过just来进行简单的创建，代码如下：

    
    
    1         Observable.just("hello", "world").subscribe(new Action1<String>() {
    2             @Override
    3             public void call(String s) {
    4                 Log.d(TAG, "call() called with: s = [" + s + "]");
    5             }
    6         });

和例子中的效果是一样的，但是这里要注意了，just方法可以传入任何类型的对象，但是必须和Action1或者Subscriber所指定的泛型一致。

也就是假设我们要打印的不是字符串而是数字，代码变成如下所示：

    
    
    1         Observable.just(1, 2).subscribe(new Action1<Integer>() {
    2             @Override
    3             public void call(Integer s) {
    4                 Log.d(TAG, "call() called with: s = [" + s + "]");
    5             }
    6         });

※ **Action1所指定的泛型由String改为Integer，call方法的参数也相应的更改。**

from方法也可以创建Observable，用法可以自行尝试。

这里说一下Interval方法，Android开发中可能会用到。Interval是定时器，每隔一段时间发送一个消息，这个消息由一个Long类型的值表示，从0开始。代码：

    
    
    1         Observable.interval(1, TimeUnit.SECONDS).subscribe(new Action1<Long>() {
    2             @Override
    3             public void call(Long aLong) {
    4                 Log.d(TAG, "call() called with: s = [" + aLong + "]");
    5             }
    6         });

运行代码之后，Logcat会每隔一秒打印一句Log，而aLong的值会从0开始每次递增1。interval方法的参数分别是：间隔值、间隔单位，上面代码指的是每隔1s触发一次。

光有计时器还不行，我们怎么让它自动停止呢，比如说打印5次。这里涉及到RxJava的过滤问题，就是使用take方法并传入发送次数，当发送次数大于这个值的时候，其他的消息都被过滤了，我们的Subscriber不再接收。代码如下：

    
    
    1         Observable.interval(1, TimeUnit.SECONDS).take(5).subscribe(new Action1<Long>() {
    2             @Override
    3             public void call(Long aLong) {
    4                 Log.d(TAG, "call() called with: s = [" + aLong + "]");
    5             }
    6         });

※ **这里的take方法不能在subscribe方法后调用，因为subscribe方法返回值不再是Observable**

当然，take方法还有其他重载，可以自行尝试。



**② 变换操作**

变换操作主要是对Observable发送的消息进行变换，使得在Subscriber中处理起来更加简单。就通过上面的例子来说明吧，我们设定了的计时器中，每次返回给Subscriber（Action1）的值是Long类型的，假设我们要在接收端得到一个Integer类型的值，怎么办呢？就要使用变换，最常用的变换就是map。

    
    
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

可以看到这个时候Action1使用的泛型变为Integer而不是Long，因为我们在subscribe方法之前调用了map方法对消息进行变换，可以看到，变换需要传入一个Funcx的对象，并实现一个call方法将Long类型的数据转换为Integer类型。希望你还记得Func1表示含有一个参数和一个返回值。

接着再说一个比较常用的，名字是flatmap。它的作用，是把一个数据类型变成一个Observable对象。也就是说Observable里面包含Observable，而新建出来的Observable中的消息会交付给原来的Observable进行发送。如果不明白，可以看例子：

假设有两个类，一个是学生（姓名、选课），一个是课程（分数），学生类中的选课是数组类型，要求是打印所有学生的每个选课成绩：

    
    
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

如果我们上面的做法，代码如下：

    
    
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

就是在接收端接收到一个Student类型的值，再使用for循环打印对应属性的值。

但是如果我们不想使用for循环，而是希望直接再接收端就可以处理对应的成绩，就要用到flatmap。代码如下：

    
    
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

※
Observable的泛型改为我们要转换的类型Course，表示我们把两个Student对象的每个Course发送出去。在Func1中的call方法中根据传入的Student对象的courses属性构造一个Observable并返回，RxJava会帮我们把每个Course逐个发送出去，log如下所示：

![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160811195353968-534405772.png)

这里如果你觉得还是不够，你想把每个成绩变成Integer再发送，该怎么做？

我们可以在返回Observable之前再进行一次map转换，这个时候得到的就是Integer了，记得更改Observable对应的泛型为Integer哦，代码如下：

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

变换操作还有其他一些，可以自行了解尝试。



**③ 过滤操作**

在上面的计时器的例子中我们已经使用了过滤操作，take算是比较简单的过滤了。这里简单介绍一下debounce，它可以让我们过滤掉一段时间内的消息。

![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160811201136593-1468323909.png)

设定了debounce之后，在一个消息发送之后的一段时间之内发送的消息将会被过滤抛弃掉。这里没想到更好的例子，原谅我：

    
    
    1         Observable.interval(1, TimeUnit.SECONDS).debounce(2, TimeUnit.SECONDS)
    2                 .subscribe(new Action1<Long>() {
    3             @Override
    4             public void call(Long aLong) {
    5                 Log.d(TAG, "call() called with: aLong = [" + aLong + "]");
    6             }
    7         });

可以看到我们设置的定时，每隔1s发送一个消息，而又给它设定了debounce为2s，什么意思呢，就是间隔小于2s的消息都被抛弃，所以这段代码没有任何输出。

* * *



实际上，RxJava还有其他一些操作，这里先忽略。 **说一些Android开发有关的** 。

**我们的异步操作一般是用来进行网络请求的，或者进行耗时操作，那么这个时候肯定不能在主线程中进行，而RxJava的一大优点是，我们可以任意的切换线程，并且，切换起来非常方便。**

为了验证所在的线程，我们先编写一个方法来获取当前线程的id：

    
    
    1     private long getCurrentThreadId() {
    2         return Thread.currentThread().getId();
    3     }

接着我们先验证一般情况下消息的发送和接收：

    
    
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

这段代码很简单，分别在发送和接收的地方打印当前的线程id，log如下：

![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160811202221453-46612809.png)

那么该如何切换线程呢？这里要用到两个方法：

  * subscribeOn：指定事件发生的线程
  * observeOn： 指定事件接收的线程

这两个方法都需要传入Scheduler对象，而这个对象可以通过Schedulers和AndroidSchedulers来获取。

  * Scheduler.newThread()：始终开启新的线程
  * Scheduler.immediate()：当前线程（默认）
  * Scheduler.io()：IO线程，如网络下载，文件读写
  * Scheduler.computation()：计算线程，如图形计算等
  * AndroidSchedulers.mainThread()：Android中的UI线程
  * AndroidSchedulers.from(Looper looper)：根据Looper来获取对应的线程，这里可以根据Handler来取得Handler对应的线程

如果我们需要在新的线程中进行消息发送（网络下载），在UI线程中更新UI，那么代码应该如下所示：

    
    
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

这个时候Log如下所示：

![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160811203606406-302862568.png)

可以看到，现在两个操作已经处于不同线程了。

如果有看我的上一篇文章，文章中需要进行高斯模糊的计算，这个计算过程可能会需要一点时间，如果不想让界面卡顿是建议开启新的线程进行的，而这里正好可以用到调度器的Computation，是不是很方便呢。



参考文章：

  1. [ReactiveX/RxJava文档中文版](https://mcxiaoke.gitbooks.io/rxdocs/content/)
  2. [给Android开发者的RxJava详解](http://gank.io/post/560e15be2dca930e00da1083#toc_18)

