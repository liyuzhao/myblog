---
title: AsyncTask源码了解
date: 2018-04-11 11:13:33
tags: [AsyncTask, android]
categories: android
---

简单了解下AsyncTask源码，作为以后复习时用。


<!-- more -->

>package: android.os;

功能：在进行异步请求时，解决子线程和主线程之间的通讯等。

此类为：抽象类，因此不能实例化，需要继承自它，再使用。

#### 源码解析

##### AsyncTask 类包含：
 
 - 3个静态内部类 [AsyncTaskResult、InternalHandler、SerialExector]
 - 1个静态内部抽象类 [WorkerRunnable]
 - 1个枚举类 [Status]
 
##### 线程池部分：

```
 /**
     * An {@link Executor} that can be used to execute tasks in parallel.
     */
    public static final Executor THREAD_POOL_EXECUTOR;

    static {
        ThreadPoolExecutor threadPoolExecutor = new ThreadPoolExecutor(
                CORE_POOL_SIZE, MAXIMUM_POOL_SIZE, KEEP_ALIVE_SECONDS, TimeUnit.SECONDS,
                sPoolWorkQueue, sThreadFactory);
        threadPoolExecutor.allowCoreThreadTimeOut(true);
        THREAD_POOL_EXECUTOR = threadPoolExecutor;
    }
       
```
其中参数：

```
	private static final int CPU_COUNT = Runtime.getRuntime().availableProcessors();
	// 核心线程数最小为2， 最大为4，根据cpu的核数确定。
    private static final int CORE_POOL_SIZE = Math.max(2, Math.min(CPU_COUNT - 1, 4));
    // 最大线程池数量为 cpu 的核数的2倍 + 1
    private static final int MAXIMUM_POOL_SIZE = CPU_COUNT * 2 + 1;
    // 非核心线程数存活时间为30s
    private static final int KEEP_ALIVE_SECONDS = 30;  
```

```
阻塞队列长度为128
private static final BlockingQueue<Runnable> sPoolWorkQueue =
            new LinkedBlockingQueue<Runnable>(128);

```

##### 使用中重要的方法：

```
// 任务开始前需要的操作：主线程
protected void onPreExecute() {}
// 抽象类，必须要实现。 耗时的操作：子线程
protected abstract Result doInBackground(Params... params);
// 后台任务执行的进度，用于显示进度条：主线程
protected void onProgressUpdate(Progress... values) {}
// 任务执行完成后，需要调用的操作：主线程
protected void onPostExecute(Result result) {}

```

##### execute执行方法：

```
第一种：需要传入调度器和参数
public final AsyncTask<Params, Progress, Result> executeOnExecutor(Executor exec,
            Params... params)
第二种：内部调用了第一种方式，使用默认的调度器，传入参数即可
public final AsyncTask<Params, Progress, Result> execute(Params... params) 
            
第三种：通过默认调度器执行runnable方法
public static void execute(Runnable runnable)

```

##### 默认调度器：sDefaultExecutor

```
private static volatile Executor sDefaultExecutor = SERIAL_EXECUTOR;

public static final Executor SERIAL_EXECUTOR = new SerialExecutor();

private static class SerialExecutor implements Executor {
        final ArrayDeque<Runnable> mTasks = new ArrayDeque<Runnable>();
        Runnable mActive;

        public synchronized void execute(final Runnable r) {
            mTasks.offer(new Runnable() {
                public void run() {
                    try {
                        r.run();
                    } finally {
                        scheduleNext();
                    }
                }
            });
            if (mActive == null) {
                scheduleNext();
            }
        }

        protected synchronized void scheduleNext() {
            if ((mActive = mTasks.poll()) != null) {
                THREAD_POOL_EXECUTOR.execute(mActive);
            }
        }
    }


```
SerialExecutor 介绍

1. SerialExecutor是通过ArrayDeque这个队列管理Runnable的，当我们启动了很多个任务，首先在第一次运行execute()方法的时候，会调用ArrayDeque的offer()方法将传入的Runnable对象添加到队列的尾部，然后判断mActive对象是不是等于null, 第一次运行肯定是null了，于是调用scheduleNext()方法。这个队列会从头部取值，并赋值给mActive对象，然后调用THREAD_POOL_EXECUTOR.execute(mActive)方法，去执行取出的Runnable对象。
2. execute 和 scheduleNext 都被synchronized修饰，因此这两个方法拥有同一把对象锁，不可以同时运行，在execute方法中，r.run()方法为子线程中的耗时任务，当执行完成后，最终会进入到finally中调用scheduleNext去取下一个top任务。
3. mTasks.offer是每次来了task都放到ArrayDeque的尾部，保证先来的先被执行。

##### executeOnExecutor()方法：

```
public final AsyncTask<Params, Progress, Result> executeOnExecutor(Executor exec,
            Params... params) {
        if (mStatus != Status.PENDING) {
            switch (mStatus) {
                case RUNNING:
                    throw new IllegalStateException("Cannot execute task:"
                            + " the task is already running.");
                case FINISHED:
                    throw new IllegalStateException("Cannot execute task:"
                            + " the task has already been executed "
                            + "(a task can be executed only once)");
            }
        }

        mStatus = Status.RUNNING;

        onPreExecute();

        mWorker.mParams = params;
        exec.execute(mFuture);

        return this;
    }
```

executeOnExecutor方法中执行了onPreExecute(),然后执行了exec.execute(mFuture);

其中Future为：

```
mFuture = new FutureTask<Result>(mWorker) {
            @Override
            protected void done() {
                try {
                    postResultIfNotInvoked(get());
                } catch (InterruptedException e) {
                    android.util.Log.w(LOG_TAG, e);
                } catch (ExecutionException e) {
                    throw new RuntimeException("An error occurred while executing doInBackground()",
                            e.getCause());
                } catch (CancellationException e) {
                    postResultIfNotInvoked(null);
                }
            }
        };
```

从executeOnExecutor方法执行中可以看出，AsyncTask内，在主线程中先执行了onPreExecute()方法，然后在线程中调用了doInBackground()方法，任务执行完成后，通过postResult 或者 postResultIfNotInvoked 方法发送消息到主线程


##### postResult方法

```
private void postResultIfNotInvoked(Result result) {
        final boolean wasTaskInvoked = mTaskInvoked.get();
        if (!wasTaskInvoked) {
            postResult(result);
        }
    }

    private Result postResult(Result result) {
        @SuppressWarnings("unchecked")
        Message message = getHandler().obtainMessage(MESSAGE_POST_RESULT,
                new AsyncTaskResult<Result>(this, result));
        message.sendToTarget();
        return result;
    }
```

postResult方法的作用即为任务结束后，发送message给主线程，反馈Result结果。

```
 private static class InternalHandler extends Handler {
        public InternalHandler(Looper looper) {
            super(looper);
        }

        @SuppressWarnings({"unchecked", "RawUseOfParameterizedType"})
        @Override
        public void handleMessage(Message msg) {
            AsyncTaskResult<?> result = (AsyncTaskResult<?>) msg.obj;
            switch (msg.what) {
                case MESSAGE_POST_RESULT:
                    // There is only one result
                    result.mTask.finish(result.mData[0]);
                    break;
                case MESSAGE_POST_PROGRESS:
                    result.mTask.onProgressUpdate(result.mData);
                    break;
            }
        }
    }

```
接收结果的为InternalHandler类，接收到结果并调用mTask的finish方法并把结果返回过去。

##### finish方法


```
private void finish(Result result) {
        if (isCancelled()) {
            onCancelled(result);
        } else {
            onPostExecute(result);
        }
        mStatus = Status.FINISHED;
    }
```
当Task结束后，会调用finish方法，如AsyncTask已经被取消，则会返回onCancelled回调，当未被取消则会调用onPostExecute回调。因此，onPostExecute回调也是主线程。

##### 总结：

过去一直在用AsyncTask，也知道怎样调用，但并不知道它的工作原理，经过我们分析源码可以看到，AsyncTask为我们做了很好的线程管理(在应用中滥用线程，会造成线程过多引起很严重的问题)，提供了执行前，执行中进度条更新，执行后等回调操作，便利了我们使用。 
