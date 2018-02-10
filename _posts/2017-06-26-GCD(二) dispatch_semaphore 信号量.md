--- 
layout:     post                      
title:      Gcd(二) Dispatch_semaphore 信号量
subtitle:   Dispatch_semaphore
date:       2017-06-26                 
author:     HuberyYang                
header-img: img/article_head_queue.jpg
catalog:    true                     
tags:                             
    - iOS
    - OC
    - GCD
    - 线程
---

在实际场景中我们需要对某一公共资源进行保护或者一些任务依次之间存在依赖关系，在GCD中可以使用一下几种处理方式：

**串行队列**

```
dispatch_queue_t sQueue = dispatch_queue_create("com.dis.seri", DISPATCH_QUEUE_SERIAL);

    //任务A
    dispatch_async(sQueue, ^{

        [NSThread sleepForTimeInterval:5.0f];
        NSLog(@"任务A -- 线程：%@",[NSThread currentThread]);
    });

    //任务B
    dispatch_async(sQueue, ^{

        [NSThread sleepForTimeInterval:5.0f];
        NSLog(@"任务B -- 线程：%@",[NSThread currentThread]);
    });

    //任务C
    dispatch_async(sQueue, ^{

        [NSThread sleepForTimeInterval:5.0f];
        NSLog(@"任务C -- 线程：%@",[NSThread currentThread]);
    });
```

**同步执行**

```
dispatch_queue_t cQueue = dispatch_queue_create("com.dis.curr", DISPATCH_QUEUE_CONCURRENT);

    //任务A
    dispatch_sync(cQueue, ^{

        [NSThread sleepForTimeInterval:5.0f];
        NSLog(@"任务A -- 线程：%@",[NSThread currentThread]);
    });

    //任务B
    dispatch_sync(cQueue, ^{

        [NSThread sleepForTimeInterval:5.0f];
        NSLog(@"任务B -- 线程：%@",[NSThread currentThread]);
    });

    //任务C
    dispatch_sync(cQueue, ^{

        [NSThread sleepForTimeInterval:5.0f];
        NSLog(@"任务C -- 线程：%@",[NSThread currentThread]);
    });
```

**信号量**

> 什么是信号量？
> 
> 信号量可以理解为一个资源计数器，通过控制可用资源数来控制任务的并发数量。同时，信号量是一个特殊的变量，程序对其访问都是原子操作，且只允许对它进行等待（即P(信号变量))和发送（即V(信号变量))信息操作。

* 信号量有3个函数，分别为

```
//用于创建信号量
//参数为当前可用资源数，取值大于等于0，等于0时进入等待状态（阻塞），大于0继续执行，通过 P操作 和 V操作 改变可用资源数
dispatch_semaphore_create(long value);

//P操作 可用资源数 -1，当资源数为0时无法进行P操作，进入等到状态
//参数一:信号量
//参数二:等待时间
dispatch_semaphore_wait(dispatch_semaphore_t dsema, dispatch_time_t timeout);

//V操作 可用资源数 +1
//参数：信号量
dispatch_semaphore_signal(dispatch_semaphore_t dsema);

```

* 通过信号量建立依赖关系

```
//创建一个信号量，设置初始资源数
    dispatch_semaphore_t sem = dispatch_semaphore_create(0);

    dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);

    //任务1
    dispatch_async(queue, ^{

        [NSThread sleepForTimeInterval:2.0f];
        NSLog(@"任务1 -- 线程：%@",[NSThread currentThread]);

        dispatch_semaphore_signal(sem);
    });

    dispatch_semaphore_wait(sem, DISPATCH_TIME_FOREVER);

    //任务2
    dispatch_async(queue, ^{

        [NSThread sleepForTimeInterval:3.0f];
        NSLog(@"任务2 -- 线程：%@",[NSThread currentThread]);

        dispatch_semaphore_signal(sem);
    });

    dispatch_semaphore_wait(sem, DISPATCH_TIME_FOREVER);

    //任务3
    dispatch_async(queue, ^{

        [NSThread sleepForTimeInterval:4.0f];
        NSLog(@"任务3 -- 线程：%@",[NSThread currentThread]);
        dispatch_semaphore_signal(sem);
    });

    dispatch_semaphore_wait(sem, DISPATCH_TIME_FOREVER);

    //任务4
    dispatch_async(queue, ^{

        [NSThread sleepForTimeInterval:5.0f];
        NSLog(@"任务4 -- 线程：%@",[NSThread currentThread]);
        dispatch_semaphore_signal(sem);
    });

    dispatch_semaphore_wait(sem, DISPATCH_TIME_FOREVER);

    //任务5
    dispatch_async(queue, ^{

        [NSThread sleepForTimeInterval:5.0f];
        NSLog(@"任务5 -- 线程：%@",[NSThread currentThread]);
    });
```

    即使异步并行也是依次执行

![](http://upload-images.jianshu.io/upload_images/2475558-1087948842f2c5c2?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 通过信号量控制最大并发数

```
//预设可用资源为2
dispatch_semaphore_t sem = dispatch_semaphore_create(2);

    dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);

    for (int index = 0; index < 11; index ++) {

        dispatch_async(queue, ^{

            dispatch_semaphore_wait(sem, DISPATCH_TIME_FOREVER);

            [NSThread sleepForTimeInterval:2.0f];
            NSLog(@"任务%d -- 线程：%@",index,[NSThread currentThread]);

            dispatch_semaphore_signal(sem);
        });

    }
```

    从结果可以看到，同时最多只有2个线程在同时执行

![](http://upload-images.jianshu.io/upload_images/2475558-38c78464a61ec064?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
