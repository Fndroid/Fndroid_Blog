# Android开发学习之路-Handler消息派发机制源码分析

注：这里只是说一下sendmessage的一个过程，post就类似的

如果我们需要发送消息，会调用sendMessage方法

    
    
    public final boolean sendMessage(Message msg)
    {
        return sendMessageDelayed(msg, 0);
    }

这个方法会调用如下的这个方法

    
    
    public final boolean sendMessageDelayed(Message msg, long delayMillis)
    {
        if (delayMillis < 0) {
            delayMillis = 0;
        }
        return sendMessageAtTime(msg, SystemClock.uptimeMillis() + delayMillis);
    }

接下来设定延迟时间，然后继续调用sendMessageAtTime方法

    
    
    public boolean sendMessageAtTime(Message msg, long uptimeMillis) {
        MessageQueue queue = mQueue;
        if (queue == null) {
            RuntimeException e = new RuntimeException(
                    this + " sendMessageAtTime() called with no mQueue");
            Log.w("Looper", e.getMessage(), e);
            return false;
        }
        return enqueueMessage(queue, msg, uptimeMillis);
    }

这里获得了消息队列，检查队列是否存在，然后返回enqueMessage的方法的执行结果，这个结果是说明消息能否进入队列的一个布尔值

    
    
    private boolean enqueueMessage(MessageQueue queue, Message msg, long uptimeMillis) {
        msg.target = this;
        if (mAsynchronous) {
            msg.setAsynchronous(true);
        }
        return queue.enqueueMessage(msg, uptimeMillis);
    }

这里是对消息进行入队处理，下面就是在MessageQueue中对消息进行入队

    
    
    boolean enqueueMessage(Message msg, long when) {
        if (msg.target == null) {
            throw new IllegalArgumentException("Message must have a target.");
        }
        if (msg.isInUse()) {
            throw new IllegalStateException(msg + " This message is already in use.");
        }
    
        synchronized (this) {
            if (mQuitting) {
                IllegalStateException e = new IllegalStateException(
                        msg.target + " sending message to a Handler on a dead thread");
                Log.w(TAG, e.getMessage(), e);
                msg.recycle();
                return false;
            }
    
            msg.markInUse();
            msg.when = when;
            Message p = mMessages;
            boolean needWake;
            if (p == null || when == 0 || when < p.when) {
                // New head, wake up the event queue if blocked.
                msg.next = p;
                mMessages = msg;
                needWake = mBlocked;
            } else {
                // Inserted within the middle of the queue.  Usually we don't have to wake
                // up the event queue unless there is a barrier at the head of the queue
                // and the message is the earliest asynchronous message in the queue.
                needWake = mBlocked && p.target == null && msg.isAsynchronous();
                Message prev;
                for (;;) {
                    prev = p;
                    p = p.next;
                    if (p == null || when < p.when) {
                        break;
                    }
                    if (needWake && p.isAsynchronous()) {
                        needWake = false;
                    }
                }
                msg.next = p; // invariant: p == prev.next
                prev.next = msg;
            }
    
            // We can assume mPtr != 0 because mQuitting is false.
            if (needWake) {
                nativeWake(mPtr);
            }
        }
        return true;
    }

就是对传递过来的消息进行一些封装然后放到队列中，至此我们的sendMessage处理完毕，返回的结果是进队是否成功的布尔值，那么究竟消息之后是如何被处理的呢？



我们可以看到在Handler构造的时候记录了一个Looper对象，也记录了一个回掉函数

    
    
    public Handler(Callback callback, boolean async) {
        if (FIND_POTENTIAL_LEAKS) {
            final Class<? extends Handler> klass = getClass();
            if ((klass.isAnonymousClass() || klass.isMemberClass() || klass.isLocalClass()) &&
                    (klass.getModifiers() & Modifier.STATIC) == 0) {
                Log.w(TAG, "The following Handler class should be static or leaks might occur: " +
                    klass.getCanonicalName());
            }
        }
    
        mLooper = Looper.myLooper();
        if (mLooper == null) {
            throw new RuntimeException(
                "Can't create handler inside thread that has not called Looper.prepare()");
        }
        mQueue = mLooper.mQueue;
        mCallback = callback;
        mAsynchronous = async;
    }

这里的myLooper方法返回的是当前线程关联的一个Looper对象

    
    
    public static @Nullable Looper myLooper() {
        return sThreadLocal.get();
    }

当Looper实例化了以后会执行自己的prepare方法然后执行loop方法，loop方法就是不断的读取消息队列中的消息然后执行相应的操作的方法，因为是在其他线程中执行的循环所以不会影响其他线程

    
    
    public static void loop() {
        final Looper me = myLooper();
        if (me == null) {
            throw new RuntimeException("No Looper; Looper.prepare() wasn't called on this thread.");
        }
        final MessageQueue queue = me.mQueue;
    
        // Make sure the identity of this thread is that of the local process,
        // and keep track of what that identity token actually is.
        Binder.clearCallingIdentity();
        final long ident = Binder.clearCallingIdentity();
    
        for (;;) {
            Message msg = queue.next(); // might block
            if (msg == null) {
                // No message indicates that the message queue is quitting.
                return;
            }
    
            // This must be in a local variable, in case a UI event sets the logger
            Printer logging = me.mLogging;
            if (logging != null) {
                logging.println(">>>>> Dispatching to " + msg.target + " " +
                        msg.callback + ": " + msg.what);
            }
    
            msg.target.dispatchMessage(msg);
    
            if (logging != null) {
                logging.println("<<<<< Finished to " + msg.target + " " + msg.callback);
            }
    
            // Make sure that during the course of dispatching the
            // identity of the thread wasn't corrupted.
            final long newIdent = Binder.clearCallingIdentity();
            if (ident != newIdent) {
                Log.wtf(TAG, "Thread identity changed from 0x"
                        + Long.toHexString(ident) + " to 0x"
                        + Long.toHexString(newIdent) + " while dispatching to "
                        + msg.target.getClass().getName() + " "
                        + msg.callback + " what=" + msg.what);
            }
    
            msg.recycleUnchecked();
        }
    }

在循环中如果读取到了消息，就会执行dispatchMessage方法，然后分派完消息之后再执行一次recycleUnchecked方法来重用这个Message，我们看到dispatchMessage方法

    
    
    public void dispatchMessage(Message msg) {
        if (msg.callback != null) {
            handleCallback(msg);
        } else {
            if (mCallback != null) {
                if (mCallback.handleMessage(msg)) {
                    return;
                }
            }
            handleMessage(msg);
        }
    }

这里看到直接执行了一个handlerMessage方法，这个方法是一个回调方法，我们是必须实现的，否则Handler什么都不会做，为什么呢？还记得刚刚说构造Handler的时候我们记录了一个CallBack的回掉吗？Handler中的这个handlerMessage方法是一个空方法，如果我们重写了这个方法，在回调的时候就会执行我们先写下的代码，也就是接收到消息之后要做什么。

    
    
    public interface Callback {
        public boolean handleMessage(Message msg);
    }
    
    public void handleMessage(Message msg) {
    }



**这里简单说下整个过程：**

**当我们实例化一个Handler的子类并重写handleMessage方法之后，这个时候系统已经帮我们做了几个事情**

**1.实例化了一个消息队列MessageQueue**

**2.实例化了一个关联的Looper对象，并让Looper不断的读取消息队列**

**3.把我们重写的handleMessage方法记录为我们需要回调的方法**

**当我们执行Handler的sendMessage方法的时候，系统会把我们传过去的Message对象添加到消息队列，这个时候如果Looper读取到了消息，就会把消息派发出去，然后
回调handleMessage方法，执行我们设定的代码。**

