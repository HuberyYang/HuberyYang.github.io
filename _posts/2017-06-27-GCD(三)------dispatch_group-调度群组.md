--- 
layout:     post                      
title:      Gcd(三) Dispatch_group 调度群组
subtitle:   Dispatch_group
date:       2017-06-27                 
author:     HuberyYang                
header-img: img/post-bg-desk.jpg  
catalog:    true                     
tags:                             
    - iOS
    - OC
    - GCD
    - 线程
---

假设一下下面的场景：某APP首页分为多个功能模块，每个模块使用不同的数据接口，为了提升用户体验，在加载首页时可以先将所有模块数据拿到后再加载UI，但不用考虑哪个模块先得到数据。这种情况下使用GCD的 `dispatch_group 调度群组` 很容易处理。

**dispatch_group 包含的函数**

```
/* 生成 group 实例 */
dispatch_group_create()

/* group  调度群组
 * queue  任务执行队列
 * block  任务内容 */
dispatch_group_async(<#dispatch_group_t _Nonnull group#>, <#dispatch_queue_t _Nonnull queue#>, <#^(void)block#>)

/* group  调度群组，与被提交的函数相关联。该群组被系统保留，直到应用程序中定义好的函数执行完毕。不可以为NULL。
 * queue  调度队列，用于提交系统定义好的函数，异步调用。该队列被系统保留，直到应用程序定义好的函数执行完毕。不可以为NULL。
 * context  是由程序定义的，用于传递给work的参数。
 * work  应用程序定义的函数，在目标队列中调用。work的第一个参数值是context。*/
dispatch_group_async_f(<#dispatch_group_t _Nonnull group#>, <#dispatch_queue_t _Nonnull queue#>, <#void * _Nullable context#>, <#dispatch_function_t _Nonnull work#>) 

/* 调用一次该函数会使当前群组中未执行任务的计数加一，与 `dispatch_group_leave` 一起使用，两者调用次数应保持一致。
 * group  调度群组 */
dispatch_group_enter(<#dispatch_group_t _Nonnull group#>)

/* 调用一次该函数会使当前群组中未执行任务的计数减一，与`dispatch_group_enter` 一起使用，两者调用次数应保持一致。
 * group  调度群组 */
dispatch_group_leave(<#dispatch_group_t _Nonnull group#>)

/* 等待调度群组关联的block内任务执行完成，等待期间会堵塞当前线程，任务完成后恢复正常，执行后续任务。
 * group  调度群组
 * timeout  等待时间 */
dispatch_group_wait(<#dispatch_group_t _Nonnull group#>, <#dispatch_time_t timeout#>)

/* 作用与 `dispatch_group_wait` 类似，但不会堵塞当前线程，并且可以指定group内任务完成之后后续任务的执行队列，后续任务在block中执行
 * group —— 调度群组
 * queue —— 后续任务执行队列
 * block —— 在这里添加后续任务 */
dispatch_group_notify(<#dispatch_group_t _Nonnull group#>, <#dispatch_queue_t _Nonnull queue#>, <#^(void)block#>)

```

**使用dispatch_group_wait**

```
dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
dispatch_group_t group = dispatch_group_create();

for (int index = 0; index < 5; index ++) {

    dispatch_group_async(group, queue, ^{

        NSLog(@"s1 线程：%@ -- index = %d",[NSThread currentThread],index);
        [NSThread sleepForTimeInterval:3.0f];
        NSLog(@"s2 线程：%@ -- index = %d",[NSThread currentThread],index);
    });

}

dispatch_group_wait(group, DISPATCH_TIME_FOREVER);    
NSLog(@"执行后续任务");
```

![](http://upload-images.jianshu.io/upload_images/2475558-5759920039647f73?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**使用dispatch_group_notify**

```
dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
dispatch_group_t group = dispatch_group_create();

for (int index = 0; index < 5; index ++) {

    dispatch_group_async(group, queue, ^{

       NSLog(@"s1 线程：%@ -- index = %d",[NSThread currentThread],index);
       [NSThread sleepForTimeInterval:3.0f];
       NSLog(@"s2 线程：%@ -- index = %d",[NSThread currentThread],index);

    });

}

dispatch_group_notify(group, queue, ^{

    NSLog(@"执行后续任务");
});
```

![](http://upload-images.jianshu.io/upload_images/2475558-3fbaaeaafd3f3809?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**dispatch_group_enter 与 dispatch_group_leave 的使用**

如果在调度群组关联的block内直接异步提交新的任务，group 不会等待嵌套的异步任务执行完毕后在进入 `dispatch_group_notify` 和 `dispatch_group_wait` 状态，如：

```
dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0); 
dispatch_group_t group = dispatch_group_create();

for (int index = 0; index < 5; index ++) {

    dispatch_group_async(group, queue, ^{

       NSLog(@"s1 线程：%@ -- index = %d",[NSThread currentThread],index);
       [NSThread sleepForTimeInterval:3.0f];
       NSLog(@"s2 线程：%@ -- index = %d",[NSThread currentThread],index);

       dispatch_async(queue, ^{
          [NSThread sleepForTimeInterval:2.0f];
          NSLog(@"s3 线程：%@ -- index = %d",[NSThread currentThread],index);
       });

    });    
}

dispatch_group_notify(group, queue, ^{

   NSLog(@"后续任务");
});
```

![](http://upload-images.jianshu.io/upload_images/2475558-360e3fdc0c22e524?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

从结果可以看出，后续任务没有在s1、s2、s3都执行完成后执行，此时就可以使用`dispatch_group_enter` 告诉调度群有组新的任务加入，使未完成任务数增加；使用`dispatch_group_leave` 使未完成任务数减少

```
dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
dispatch_group_t group = dispatch_group_create();

for (int index = 0; index < 5; index ++) {

    dispatch_group_async(group, queue, ^{

        NSLog(@"s1 线程：%@ -- index = %d",[NSThread currentThread],index);
        [NSThread sleepForTimeInterval:3.0f];
        NSLog(@"s2 线程：%@ -- index = %d",[NSThread currentThread],index);

        //有新任务加入
        dispatch_group_enter(group);

        dispatch_async(queue, ^{
            [NSThread sleepForTimeInterval:2.0f];
            NSLog(@"s3 线程：%@ -- index = %d",[NSThread currentThread],index);

            //有任务完成
            dispatch_group_leave(group);
        });

    });

}

dispatch_group_notify(group, queue, ^{

    NSLog(@"后续任务");
});
```

![](http://upload-images.jianshu.io/upload_images/2475558-17f64b34f95edcf9?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 此外，dispatch_group_enter 与 dispatch_group_leave 也可以直接使用，但需要保持两者调用次数一致，配合异步执行，其效果和 `dispatch_group_async` 类似

```
dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
dispatch_group_t group = dispatch_group_create();

for (int index = 0; index < 5; index ++) {

    dispatch_group_enter(group);

    dispatch_async(queue, ^{

        NSLog(@"s1 线程：%@ -- index = %d",[NSThread currentThread],index);
        [NSThread sleepForTimeInterval:3.0f];
        NSLog(@"s2 线程：%@ -- index = %d",[NSThread currentThread],index);

        dispatch_group_leave(group);

    });
}

dispatch_group_notify(group, queue, ^{

    NSLog(@"后续任务");
});
```
