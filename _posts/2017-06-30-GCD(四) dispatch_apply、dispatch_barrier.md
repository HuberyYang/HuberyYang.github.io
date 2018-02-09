--- 
layout:     post                      
title:      Gcd(四) Dispatch_apply、dispatch_barrier
subtitle:   Dispatch_apply、dispatch_barrier
date:       2017-06-30                 
author:     HuberyYang                
header-img: img/post-bg-desk.jpg  
catalog:    true                     
tags:                             
    - iOS
    - OC
    - GCD
    - 线程
---


**dispatch_apply**

`dispatch_apply` 可以像 for 循环一样多次执行其绑定的block，在所有block任务完成之后，再进行后续任务

```
/* iterations  执行次数
 * queue  执行队列，并行或者串行，会影响到任务执行顺序
 * block  具体任务
 * size_t  执行下标，区分不同block，代表每个block执行顺序 */
void dispatch_apply(size_t iterations, dispatch_queue_t queue, DISPATCH_NOESCAPE void (^block)(size_t));
```

* 使用示例：

```
dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);

dispatch_apply(5, queue, ^(NSUInteger index) {

   NSLog(@"thread = %@ , index = %ld",[NSThread currentThread],index);

});
```

![](http://upload-images.jianshu.io/upload_images/2475558-40f37b251ba49e03?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

    上面是并行队列，如果换成串行队列，会按顺序执行block

```
dispatch_queue_t queue = dispatch_queue_create("com.serial", DISPATCH_QUEUE_SERIAL);
```

![](http://upload-images.jianshu.io/upload_images/2475558-f4361745e2d88372?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 对于批量处理相同的任务`dispatch_apply` 相较于 for 和 while 能更合理的使用资源

> 为了进行对比，先创建了一个数组作为处理对象，包含50个元素（ps:50个虽然少，但也能看到效果）。循环和`dispatch_apply`都使用并行队列

```
//模拟操作对象
    NSMutableArray *testArr = [NSMutableArray array];
    for (int index = 0; index < 50; index ++) {
        [testArr addObject:@(index)];
    }

//执行队列
    dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
```

1.  使用for循环

```
for (int index = 0; index < testArr.count; index ++) {

    dispatch_async(queue, ^{

        NSLog(@"queue = %@ , index = %@",[NSThread currentThread],testArr[index]);

    });
}
NSLog(@"执行完毕");
```

![](http://upload-images.jianshu.io/upload_images/2475558-591bf1d205611708?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

    从结果中可以看出，执行过程创建了大量新线程

1.  使用`dispatch_apply`

```
dispatch_apply(testArr.count, queue, ^(NSUInteger index) {

    NSLog(@"thread = %@ , index = %@",[NSThread currentThread],testArr[index]);

});
NSLog(@"执行完毕");
```
![](http://upload-images.jianshu.io/upload_images/2475558-5bd4623e713c06c0?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 相较于 for 循环，`dispatch_apply`在执行的过程中新创建的线程数要少得多，并且`dispatch_apply`会在其关联的block任务都执行完成后再进行后续任务的执行。所以，`dispatch_apply`在批量任务处理中还是很好用的。

**dispatch_barrier**

`barrier` 意思是栅栏，`dispatch_barrier` 称为栅栏函数，在存在栅栏函数，且使用`DISPATCH_QUEUE_CONCURRENT`创建的并行队列(非全局并行队列，原因后面说明)时，任务执行顺序会变为： 栅栏之前的任务 —-> 栅栏本身提交的block任务 —-> 栅栏之后的任务。

常用的有 `dispatch_barrier_async` 和 `dispatch_barrier_sync` ，两者区别在于异步和同步。下面以`dispatch_barrier_async` 举例：

* 使用举例

```
dispatch_queue_t cQueue = dispatch_queue_create("com.concurrent", DISPATCH_QUEUE_CONCURRENT);

    dispatch_async(cQueue, ^{
        [NSThread sleepForTimeInterval:5.0f];
        NSLog(@"s1");
    });

    dispatch_async(cQueue, ^{
        [NSThread sleepForTimeInterval:5.0f];
        NSLog(@"s2");
    });

    dispatch_async(cQueue, ^{
        [NSThread sleepForTimeInterval:5.0f];
        NSLog(@"s3");
    });

    dispatch_async(cQueue, ^{
        [NSThread sleepForTimeInterval:5.0f];
        NSLog(@"s4");
    });

    //添加栅栏
    dispatch_barrier_async(cQueue, ^{

        for (int index = 0; index < 5; index ++) {
            [NSThread sleepForTimeInterval:1.0f];
            NSLog(@"thread = %@ , index = %d",[NSThread currentThread],index);
        }
    });

    dispatch_async(cQueue, ^{
        [NSThread sleepForTimeInterval:3.0f];
        NSLog(@"y1");
    });

    dispatch_async(cQueue, ^{
        [NSThread sleepForTimeInterval:3.0f];
        NSLog(@"y2");
    });

    dispatch_async(cQueue, ^{
        [NSThread sleepForTimeInterval:3.0f];
        NSLog(@"y3");
    });
```

![](http://upload-images.jianshu.io/upload_images/2475558-a76ba886ed930aed?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 注意点

使用栅栏函数时需要使用 `DISPATCH_QUEUE_CONCURRENT` 创建的并行队列，否则 `dispatch_barrier_async` 效果将与 `dispatch_async` 类似，失去栅栏的作用。
将上面的例子中队列换成 全局并行队列 再看看结果

```
dispatch_async(cQueue, ^{
        [NSThread sleepForTimeInterval:5.0f];
        NSLog(@"s1");
    });

    dispatch_async(cQueue, ^{
        [NSThread sleepForTimeInterval:5.0f];
        NSLog(@"s2");
    });

    dispatch_async(cQueue, ^{
        [NSThread sleepForTimeInterval:5.0f];
        NSLog(@"s3");
    });

    dispatch_async(cQueue, ^{
        [NSThread sleepForTimeInterval:5.0f];
        NSLog(@"s4");
    });

//替换了执行队列后，栅栏效果消失    
dispatch_barrier_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{

        for (int index = 0; index < 5; index ++) {
            [NSThread sleepForTimeInterval:1.0f];
            NSLog(@"thread = %@ , index = %d",[NSThread currentThread],index);
        }
    });

    dispatch_async(cQueue, ^{
        [NSThread sleepForTimeInterval:3.0f];
        NSLog(@"y1");
    });

    dispatch_async(cQueue, ^{
        [NSThread sleepForTimeInterval:3.0f];
        NSLog(@"y2");
    });

    dispatch_async(cQueue, ^{
        [NSThread sleepForTimeInterval:3.0f];
        NSLog(@"y3");
    });
```

![](http://upload-images.jianshu.io/upload_images/2475558-5922ecf91e31ba00?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

从截图中可以看出，栅栏已经失效；需配合 `DISPATCH_QUEUE_CONCURRENT` 创建的队列才能生效。
