---
title: Android 异步消息处理机制
date: 2017-07-22 09:57:34
categories: Android 系统
tags: 
 - Handler
 - Android 系统
 - 消息机制
---

![message](http://oslynwh8c.bkt.clouddn.com/image/hexo/message.jpg)

### 前言

Android 中的异步消息处理机制，也就是Handler，作为面试常客不得不理清楚。

这个笔记是以前记录的，现在重新整理。

<!-- more -->

### 异步消息机制

Android中的异步消息处理机制用于同一进程中线程间通讯，Activity 的生命周期的方法都是在 **ActivityThread** 中通过 Handler 接收消息并做回调的 。

主要由四个部分组成，**Message、Handler、MessageQueue 和 Looper**.



#### Message

`Message` 是在线程之间传递的消息，它可以在内部携带少量信息，用于在不同线程之间交互数据。

它的主要字段如下：

```java
//用于绑定此消息要发送的目标，通过这个字段来回调handler里面的方法 
//msg.target.dispatchMessage(msg);
Handler target;    

//用于指向下一个Message对象，形成一种队列结构 
Message next;     

//用户自定义的识别码
public int what;    

//用于携带数据
public int arg1; 
public int arg2;        
public Object obj;
```





#### Handler

Handler是处理者，主要用于发送消息和处理消息的，一个线程可用创建多个Handler对象。



##### 构造函数

在创建对象时，其构造方法中，会取出存放在`ThreadLocal` 中的 `Looper` （即当前线程创建的Looper）

```java
    public Handler(Callback callback, boolean async) {
      //匿名类、内部类或本地类都必须申明为static，否则会警告可能出现内存泄露
        if (FIND_POTENTIAL_LEAKS) {
            final Class<? extends Handler> klass = getClass();
            if ((klass.isAnonymousClass() || klass.isMemberClass() || klass.isLocalClass()) &&
                    (klass.getModifiers() & Modifier.STATIC) == 0) {
                Log.w(TAG, "The following Handler class should be static or leaks might occur: " +
                    klass.getCanonicalName());
            }
        }
		//这里取出ThreadLocal中的Looper 
        mLooper = Looper.myLooper();
        //如果此线程创建handler之前没有Looper.prepare()，则会报错
      	//UI线程不报错是因为在ActivityThread中已经创建过一次
        if (mLooper == null) {
            throw new RuntimeException(
                "Can't create handler inside thread that has not called Looper.prepare()");
        }
      	//在Looper中拿到绑定的MessageQueue
        mQueue = mLooper.mQueue;
        mCallback = callback;
        mAsynchronous = async;
    }
```



##### 发送消息

同一进程中资源共享，我们在子线程中使用主线程的 handler ，并调用 handler 的 `sendMessage（）` 或者`sendMessageDelayed()` 等方法，而这些方法都最终会调用到 **enqueueMessage** 这个方法。

![Handler 发送消息](http://gityuan.com/images/handler/java_sendmessage.png)

最终调用到 `enqueueMessage`  方法：

```java
	//参数1:创建时获取的消息队列
	//参数2：在子线程创建的msg
	//参数3：发送消息的时间（自系统开机到现在的毫秒值加上延时时间）
    private boolean enqueueMessage(MessageQueue queue, Message msg, long uptimeMillis) {
        msg.target = this;		//将当前的Handler捆绑到msg的target属性上
        if (mAsynchronous) {
            msg.setAsynchronous(true);
        }
      	//调用了主线程创建的MessageQueue对象中的enqueueMessage()入队方法
        return queue.enqueueMessage(msg, uptimeMillis);
    }
```



##### 分发消息

在handler中还有一个回调的方法，这个方法会在 `Looper.loop( )` 方法中回调`msg.target.dispatchMessage(msg)` 

```java
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
```

**分发消息流程：**

1. 当 `Message` 的回调方法不为空时，则回调方法 `msg.callback.run()` ，其中callBack数据类型为Runnable,否则进入步骤2；
2. 当 `Handler` 的 `mCallback` 成员变量不为空时，则回调方法 `mCallback.handleMessage(msg)` ,否则进入步骤3；
3. 调用 `Handler` 自身的回调方法 `handleMessage()` ，该方法默认为空，Handler子类通过覆写该方法来完成具体的逻辑。



#### MessageQueue

`MessageQueue` 是消息队列，用来管理消息的。每个线程只会有一个 `MessageQueue` 对象，因为MessageQueue对象是在 Looper 的构造方法中创建，而 Looper 在一个线程中只能创建一次。

消息队列提供了入队方法和出队方法。



##### 入队列

```java
    boolean enqueueMessage(Message msg, long when) {
        if (msg.target == null) {
            throw new IllegalArgumentException("Message must have a target.");
        }
        if (msg.isInUse()) {
            throw new IllegalStateException(msg + " This message is already in use.");
        }

        synchronized (this) {
            if (mQuitting) {	//正在退出时，回收msg，加入到消息池
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
                //p为null(代表MessageQueue没有消息） 
              	//或者msg的触发时间是队列中最早的， 则进入该该分支
                msg.next = p;
                mMessages = msg;
                needWake = mBlocked;//当阻塞时需要唤醒
            } else {
                //将消息按时间顺序插入到MessageQueue。一般地，不需要唤醒事件队列，除非
            	//消息队头存在barrier，并且同时Message是队列中最早的异步消息。
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

            //消息没有退出，我们认为此时mPtr != 0
            if (needWake) {
                nativeWake(mPtr);
            }
        }
        return true;
    }
```

`MessageQueue` 并没有使用一个集合把所有的消息都保存起来，它只使用了一个 `mMessages` 对象表示当前待处理的消息。

观察上面的代码我们就可以看出，所谓的入队其实就是将所有的消息按时间来进行排序。

具体的操作方法就根据时间的顺序调用 `msg.next`，从而为每一个消息指定它的下一个消息是什么。这时会把 `mMessages`  赋值为新入队的这条消息，然后将这条消息的next指定为刚才的`mMessages`，这样也就完成了添加消息到队列头部的操作。



##### 出队列

```java
    Message next() {
        //当消息循环已经退出，则直接返回
        final long ptr = mPtr;
        if (ptr == 0) {
            return null;
        }

        int pendingIdleHandlerCount = -1; // 循环迭代的首次为-1
        int nextPollTimeoutMillis = 0;
        for (;;) {
            if (nextPollTimeoutMillis != 0) {
                Binder.flushPendingCommands();
            }

            nativePollOnce(ptr, nextPollTimeoutMillis);

            synchronized (this) {
                //阻塞操作，当等待nextPollTimeoutMillis时长，或者消息队列被唤醒，都会返回
                final long now = SystemClock.uptimeMillis();
                Message prevMsg = null;
                Message msg = mMessages;
                if (msg != null && msg.target == null) {
                    //当消息Handler为空时，查询MessageQueue中的下一条异步消息msg，则退出循环。
                    do {
                        prevMsg = msg;
                        msg = msg.next;
                    } while (msg != null && !msg.isAsynchronous());
                }
                if (msg != null) {
                    if (now < msg.when) {
                        //当异步消息触发时间大于当前时间，则设置下一次轮询的超时时长
                        nextPollTimeoutMillis = (int) Math.min(msg.when - now, Integer.MAX_VALUE);
                    } else {
                        // 获取一条消息，并返回
                        mBlocked = false;
                        if (prevMsg != null) {
                            prevMsg.next = msg.next;
                        } else {
                            mMessages = msg.next;
                        }
                        msg.next = null;
                        if (DEBUG) Log.v(TAG, "Returning message: " + msg);
                      	//设置消息的使用状态，即flags |= FLAG_IN_USE
                        msg.markInUse();
                      	//成功地获取MessageQueue中的下一条即将要执行的消息
                        return msg;
                    }
                } else {
                    //没有消息
                    nextPollTimeoutMillis = -1;
                }

                //消息正在退出，返回null
                if (mQuitting) {
                    dispose();
                    return null;
                }

                //当消息队列为空，或者是消息队列的第一个消息时
                if (pendingIdleHandlerCount < 0
                        && (mMessages == null || now < mMessages.when)) {
                    pendingIdleHandlerCount = mIdleHandlers.size();
                }
                if (pendingIdleHandlerCount <= 0) {
                    //没有idle handlers 需要运行，则循环并等待。
                    mBlocked = true;
                    continue;
                }

                if (mPendingIdleHandlers == null) {
                    mPendingIdleHandlers = new IdleHandler[Math.max(pendingIdleHandlerCount, 4)];
                }
                mPendingIdleHandlers = mIdleHandlers.toArray(mPendingIdleHandlers);
            }

            //只有第一次循环时，会运行idle handlers，执行完成后，重置pendingIdleHandlerCount为0.
            for (int i = 0; i < pendingIdleHandlerCount; i++) {
                final IdleHandler idler = mPendingIdleHandlers[i];
                mPendingIdleHandlers[i] = null; //去掉handler的引用

                boolean keep = false;
                try {
                    keep = idler.queueIdle();	//idle时执行的方法
                } catch (Throwable t) {
                    Log.wtf(TAG, "IdleHandler threw exception", t);
                }

                if (!keep) {
                    synchronized (this) {
                        mIdleHandlers.remove(idler);
                    }
                }
            }

            //重置idle handler个数为0，以保证不会再次重复运行
            pendingIdleHandlerCount = 0;

            //当调用一个空闲handler时，一个新message能够被分发，因此无需等待可以直接查询pending message.
            nextPollTimeoutMillis = 0;
        }
    }
```

总结来说，如果当前 `MessageQueue` 中存在 `mMessages` (即待处理消息)，就将这个消息出队，然后让下一条消息成为 `mMessages` ，否则就进入一个阻塞状态，一直等到有新的消息入队，所以这里是一个死循环 。



#### Looper

Looper 是每个线程中的 MessageQueue 的管家。

##### 构造方法

```java
    private Looper(boolean quitAllowed) {
        mQueue = new MessageQueue(quitAllowed); //在构造方法中，创建了一个MessageQueue（消息队列）
        mThread = Thread.currentThread();
    }
```



Looper主要作用：

1、	与当前线程绑定，保证一个线程只会有一个 `Looper` 实例，同时一个 `Looper` 实例也只有一个 `MessageQueue` 。

2、	`loop()` 方法，不断从  `MessageQueue` 中去取消息，交给消息的 target 的 Handler 调用 `dispatchMessage` 处理消息。



重点关注两个方法，`prepare（）`和 ` loop（）`。

##### prepare()

```java
    private static void prepare(boolean quitAllowed) {
        if (sThreadLocal.get() != null) {
            throw new RuntimeException("Only one Looper may be created per thread");
        }
        sThreadLocal.set(new Looper(quitAllowed));
    }
```

`sThreadLocal` 是一个 `ThreadLocal` 对象，可以在一个线程中存储变量。prepare 方法首先判断了 ThreadLocal 中是否存在 Looper ，存在则抛出异常，否则创建一个 Looper 的实例，并放入了 ThreadLocal。这也就说明了Looper.prepare()方法不能被调用两次，同时也保证了一个线程中只有一个Looper实例。



##### Loop()

```java
    public static void loop() {
        final Looper me = myLooper();//该方法直接返回了sThreadLocal存储的Looper实例。
        if (me == null) {	//如果Looper为null则抛出异常，也就是说looper方法必须在prepare方法之后运行
            throw new RuntimeException("No Looper; Looper.prepare() wasn't called on this thread.");
        }
        final MessageQueue queue = me.mQueue;//拿到该looper实例中的mQueue（消息队列）

        // Make sure the identity of this thread is that of the local process,
        // and keep track of what that identity token actually is.
        Binder.clearCallingIdentity();
        final long ident = Binder.clearCallingIdentity();

      	//进入了死循环。
        for (;;) {
            Message msg = queue.next(); //取出一条消息，如果没有消息则在此阻塞
            if (msg == null) {
                //没有消息，则退出循环
                return;
            }

            //默认为null，可通过setMessageLogging()方法来指定输出，用于debug功能
            final Printer logging = me.mLogging;
            if (logging != null) {
                logging.println(">>>>> Dispatching to " + msg.target + " " +
                        msg.callback + ": " + msg.what);
            }

            final long traceTag = me.mTraceTag;
            if (traceTag != 0 && Trace.isTagEnabled(traceTag)) {
                Trace.traceBegin(traceTag, msg.target.getTraceName(msg));
            }
            try {
              //取出msg中捆绑的Handler对象，并调用handler的dispatchMessage（）方法
                msg.target.dispatchMessage(msg);
            } finally {
                if (traceTag != 0) {
                    Trace.traceEnd(traceTag);
                }
            }

            if (logging != null) {
                logging.println("<<<<< Finished to " + msg.target + " " + msg.callback);
            }

          	//确保分发过程中identity不会损坏
            final long newIdent = Binder.clearCallingIdentity();
            if (ident != newIdent) {
                Log.wtf(TAG, "Thread identity changed from 0x"
                        + Long.toHexString(ident) + " to 0x"
                        + Long.toHexString(newIdent) + " while dispatching to "
                        + msg.target.getClass().getName() + " "
                        + msg.callback + " what=" + msg.what);
            }
			//将Message放入消息池
            msg.recycleUnchecked();
        }
    }
```

Looper.loop() 中的 for 循环会让当前线程阻塞，不是卡死，这个和操作系统有关。如果你在主线程操作，如activity生命周期 onCreate、主线程的 handler，实际上是都是通过handler发消息的，消息会在刚才的for循环中处理，这个消息会唤醒线程，如果这个时候你搞个耗时操作（在activity生命周期onCreate、主线程的handler中），那就会卡死了。



### 总结

整个消息机制如图

![handler 机制](http://gityuan.com/images/handler/handler_java.jpg)



- Handler通过sendMessage()发送Message到MessageQueue队列；
- Looper通过loop()，不断提取出达到触发条件的Message，并将Message交给target来处理；
- 经过dispatchMessage()后，交回给Handler的handleMessage()来进行相应地处理。
- 将Message加入MessageQueue时，处往管道写入字符，可以会唤醒loop线程；如果MessageQueue中没有Message，并处于Idle状态，则会执行IdelHandler接口中的方法，往往用于做一些清理性地工作。



**主线程的死循环一直运行是不是特别消耗CPU资源呢？**

其实不然，这里就涉及到 Linux  `pipe/epoll` 机制，简单说就是在主线程的 MessageQueue 没有消息时，便阻塞在 loop 的 `queue.next()` 中的 `nativePollOnce()` 方法里，此时主线程会释放 CPU 资源进入休眠状态，直到下个消息到达或者有事务发生，通过往pipe管道写端写入数据来唤醒主线程工作。这里采用的 `epoll` 机制，是一种 IO 多路复用机制，可以同时监控多个描述符，当某个描述符就绪(读或写就绪)，则立刻通知相应程序进行读或写操作，本质同步 I/O，即读写是阻塞的。 所以说，主线程大多数时候都是处于休眠状态，并不会消耗大量CPU资源。



### 参考

[Android中为什么主线程不会因为Looper.loop()里的死循环卡死](https://www.zhihu.com/question/34652589/answer/90344494)

[Android消息机制1-Handler(Java层)](http://gityuan.com/2015/12/26/handler-message-framework/#next)



