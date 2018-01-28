# Android����ѧϰ֮·-Handler��Ϣ�ɷ�����Դ�����

ע������ֻ��˵һ��sendmessage��һ�����̣�post�����Ƶ�

���������Ҫ������Ϣ�������sendMessage����

    
    
    public final boolean sendMessage(Message msg)
    {
        return sendMessageDelayed(msg, 0);
    }

���������������µ��������

    
    
    public final boolean sendMessageDelayed(Message msg, long delayMillis)
    {
        if (delayMillis < 0) {
            delayMillis = 0;
        }
        return sendMessageAtTime(msg, SystemClock.uptimeMillis() + delayMillis);
    }

�������趨�ӳ�ʱ�䣬Ȼ���������sendMessageAtTime����

    
    
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

����������Ϣ���У��������Ƿ���ڣ�Ȼ�󷵻�enqueMessage�ķ�����ִ�н������������˵����Ϣ�ܷ������е�һ������ֵ

    
    
    private boolean enqueueMessage(MessageQueue queue, Message msg, long uptimeMillis) {
        msg.target = this;
        if (mAsynchronous) {
            msg.setAsynchronous(true);
        }
        return queue.enqueueMessage(msg, uptimeMillis);
    }

�����Ƕ���Ϣ������Ӵ������������MessageQueue�ж���Ϣ�������

    
    
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

���ǶԴ��ݹ�������Ϣ����һЩ��װȻ��ŵ������У��������ǵ�sendMessage������ϣ����صĽ���ǽ����Ƿ�ɹ��Ĳ���ֵ����ô������Ϣ֮������α�������أ�



���ǿ��Կ�����Handler�����ʱ���¼��һ��Looper����Ҳ��¼��һ���ص�����

    
    
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

�����myLooper�������ص��ǵ�ǰ�̹߳�����һ��Looper����

    
    
    public static @Nullable Looper myLooper() {
        return sThreadLocal.get();
    }

��Looperʵ�������Ժ��ִ���Լ���prepare����Ȼ��ִ��loop������loop�������ǲ��ϵĶ�ȡ��Ϣ�����е���ϢȻ��ִ����Ӧ�Ĳ����ķ�������Ϊ���������߳���ִ�е�ѭ�����Բ���Ӱ�������߳�

    
    
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

��ѭ���������ȡ������Ϣ���ͻ�ִ��dispatchMessage������Ȼ���������Ϣ֮����ִ��һ��recycleUnchecked�������������Message�����ǿ���dispatchMessage����

    
    
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

���￴��ֱ��ִ����һ��handlerMessage���������������һ���ص������������Ǳ���ʵ�ֵģ�����Handlerʲô����������Ϊʲô�أ����ǵøո�˵����Handler��ʱ�����Ǽ�¼��һ��CallBack�Ļص���Handler�е����handlerMessage������һ���շ��������������д������������ڻص���ʱ��ͻ�ִ��������д�µĴ��룬Ҳ���ǽ��յ���Ϣ֮��Ҫ��ʲô��

    
    
    public interface Callback {
        public boolean handleMessage(Message msg);
    }
    
    public void handleMessage(Message msg) {
    }



**�����˵���������̣�**

**������ʵ����һ��Handler�����ಢ��дhandleMessage����֮�����ʱ��ϵͳ�Ѿ����������˼�������**

**1.ʵ������һ����Ϣ����MessageQueue**

**2.ʵ������һ��������Looper���󣬲���Looper���ϵĶ�ȡ��Ϣ����**

**3.��������д��handleMessage������¼Ϊ������Ҫ�ص��ķ���**

**������ִ��Handler��sendMessage������ʱ��ϵͳ������Ǵ���ȥ��Message������ӵ���Ϣ���У����ʱ�����Looper��ȡ������Ϣ���ͻ����Ϣ�ɷ���ȥ��Ȼ��
�ص�handleMessage������ִ�������趨�Ĵ��롣**

