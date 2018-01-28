# Android开发学习之路-回调机制学习笔记

不知道是我学Java的时候没有认真听还是怎么的，曾经一直不知道什么是"回调"，它有什么用，百度一大堆，都太复杂看不明白（好吧是我笨），所以想把自己理解的分享给其他看到的人，大家都真正认识一下这个重要的机制。

如果你要进行Android开发，那么回调肯定是会经常用到的，为什么？下面慢慢来说，大牛其实这个地方已经可以离开了......

先从简单的开始，什么是回调？

**Java允许我们调用接口的方法，但是有一个前提，就是编译的时候接口的对象必须是一个具体的类，并且这个类实现了接口。当我们调用接口的方法的时候，接口的具体子类中的具体方法会被调用，这就是回调。**
嗯，我说什么鬼？还是上代码吧。

    
    
    public interface A {
        void printClassName();
    }
    
    
    public class B implements A {
        public void printClassName() {
            System.out.println("This is class B!");
        }
    }
    
    
    public static void main(String[] args) {
            A a = new B();
            a.printClassName();
    }

输出的结果如下：

![](http://images2015.cnblogs.com/blog/852227/201603/852227-20160322211945370-1331081274.png)

从上面的代码可以看到，我们在主函数中定义了接口A，也调用了接口A的方法，但是要注意，我们实例化的时候其实new的是一个实现了A接口的具体类，我们调用A中的printClassName方法的时候Java就会去回调B中的该方法。这样做的优点是什么？就是我们在调用的时候，可以完全不管A的子类是如何实现A中的方法的，只需要调用这个方法即可，怎么实现，我们不管，那是子类的事情了，这样其实是解耦的一种很好的方法，如果我们要修改为别的类，只需要把new后面的子类改掉就好。

是不是很简单？如果代码你都懂，那么恭喜你，你已经距离成功只有一步之遥了。

说回Androd，其实里面是有很多地方用到这个机制的，我们也是从简单的说起。

很多时候，我们对某个控件实现点击事件的监听，会像下面这样做：

    
    
    Button btn = new Button(this);
    btn.setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View v) {
            // doSth
        }
    });

很多人都觉得，这样很直接明了啊，可是如果我问你，为什么我们要new一个OnClickListener进去呢？如果你不知道，证明你还不完全了解回调。按照我们上面所说的，我们可以调用接口的方法，但是接口需要一个具体的实现，所以我们应该知道，setOnClickListener中的参数应该是一个接口，不信？我们一起看看源码

    
    
    public interface OnClickListener {
            /**
             * Called when a view has been clicked.
             *
             * @param v The view that was clicked.
             */
            void onClick(View v);
    }

果然，接口中还有一个onClick方法。既然是回调，我们再看看这个方法在哪里被调用了

先看setOnClickListener方法

    
    
    public void setOnClickListener(@Nullable OnClickListener l) {
            if (!isClickable()) {
                setClickable(true);
            }
            getListenerInfo().mOnClickListener = l;
    }

关键是把OnClickListener的对象赋值给了ListenerInfo的mOnClickListener，我们再看下去

    
    
    public boolean performClick() {
            final boolean result;
            final ListenerInfo li = mListenerInfo;
            if (li != null && li.mOnClickListener != null) {
                playSoundEffect(SoundEffectConstants.CLICK);
                li.mOnClickListener.onClick(this);
                result = true;
            } else {
                result = false;
            }
    
            sendAccessibilityEvent(AccessibilityEvent.TYPE_VIEW_CLICKED);
            return result;
        }

我们看到在这个叫做performClick的方法中调用了onClick方法，还有另外一个叫做callOnClick的方法也会调用onClick，区别可以去看看源码的说明，这里不详细。那么这两个方法又在什么时候被执行的呢？其实他们都是在屏幕接收到点击信号的时候，把数据一层一层的往上传递给framework，由framework来调用。对于了解回调，我觉得到这里就足够了，再往下就有点偏了。

要明白的一点就是，在这个类中，调用的是接口的方法。现在你应该明白了吧，为什么我们要new一个OnClickListener的子类呢？就是让编译器知道，调用接口方法的时候，去回调我们设置的OnClickListener子类的对应方法。

如果到这里你都懂了，很好，我们再说多一些，具体一些。

究竟我们知道了这个机制之后，它有什么用呢？一般来说，用得比较多，就是通信以及MVP模式

通信，其实就是不同组件之间的通信，可以是Fragment和Activity，也可以是Adapter和Activity，这里不详细说太多，可以看看之前的博文：http://www.cnblogs.com/Fndroid/p/5187444.html

上面我们说到了MVP，这个模式真的很重要，对于比较大的项目来说，意义很深远，既能帮助我们解耦不堪重负的Activity，也能便于我们维护和扩展，有兴趣可以参考下面这个项目：https://github.com/bboyfeiyu/the-
tech-frontier-app

