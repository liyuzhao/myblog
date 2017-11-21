---
title: iPhone开发-GCD
date: 2017-11-15 20:57:52
tags: [ios]
categories: ios
---

一些常用的API

<!-- more -->

```
//异步执行一个线程
dispatch_async(queue,^{});

//创建一个线程
dispatch_queue_t queue =     dispatch_queuq_create("com.example.queue",NULL);

//获取主线程的方法
dispatch_get_main_queue();

//优先级获取方法
dispath_queue_t queue = dispatch_get_global_queue(优先级，0);
Dispatch Queue 的种类 有几种

//优先级常量
DISPATCH_QUEUE_PRIORITY_HIGH
DISPATCH_QUEUE_PRIORITY_DEFAULT
DISPATCH_QUEUE_PRIORITY_LOW
DISPATCH_QUEUE_PRIORITY_BACKGROUND


//延迟执行
dispatch_after

dispatch_time_t time = dispatch_time(DISPATCH_TIME_NOW,3ull*NSEC_PER_SEC);
```

处理线程的API，有涉及到暂停，挂起，恢复，取消，等待、延迟执行，线程组，同步和异步 ，死锁，只执行一次，等系列的概念出现。这些概念大部分是对于处理线程和任务。

```
dispatch_time_t

dispatch_once_t

dispatch_after

dispatch_group_wait

dispatch_release

dispatch_barrier_async

dispatch_async //异步

dispatch_sync //同步

dispatch_apply

dispatch_io_create
```

当线程过多时候，会引发内存问题。导致系统内存开销过多，效率降低。

同样多线程在更新相同一个资源的时候，会造成数据竞争的问题。

同步执行的时候容易出现死锁的问题，导致UI卡顿的问题发生。

系统提供了标准的Dispatch Queue
1.Main Dispatch Queue
2.Global Dispatch Queue

通过dispatch_get_global_queue 可以获取到不同等级的线程
通过dispatch_get_main_queue 获取主线程

**刷新UI操作必须在主线程**

```
dispatch_async(dispatch_get_global_queue(0,0),^{
   NSInteger count = [numOfInstructions.text intValue];
   for (int i = 0; i<count;i++)
   {
      dispatch_async(dispatch_get_main_queue(),^{
      currentCount.text =[NSString stringWithFormat:@"instruction%d",i];
     });
   }
 });
```

**构建一个单例**

```
+(SingleManager) shareManager
{
  static dispatch_once_t once;
  static SingleManager *instance;
  dispatch_once(&once,^{
     instance = [[self alloc]init];
   }
   return instance;
}
```

**延迟执行某些任务**

```
MBProgressHUD *hud = [MBProgressHUD showHUDAddedTo:self.view animated:YES];
hud.labelText =@"拼命加载中";
dispatch_time_t popTime = dispatch_time(DISPATCH_TIME_NOW, 10 *NSEC_PER_SEC);
   dispatch_after(popTime, dispatch_get_main_queue(), ^{
       [MBProgressHUD hideAllHUDsForView:self.view animated:YES];
   });
```
