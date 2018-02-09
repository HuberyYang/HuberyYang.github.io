--- 
layout:     post                      
title:      Could not find test host for xxxxTests
subtitle:   单元测试报错：Could not find test host for xxxxTests
date:       2018-01-31                  
author:     HuberyYang                
header-img: img/post-bg-desk.jpg  
catalog:    true                     
tags:                             
    - iOS
    - Collections 
---

单元测试报错：Could not find test host for xxxxTests: ，意为无法找到需要进行单元测试的 Target，完整的错误信息如下：
![屏幕快照](http://upload-images.jianshu.io/upload_images/2475558-b354c616e439b697.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

解决办法：按下图找到 `Host Application` ，然后选择准备测试的Target就可以了。
![deal.png](http://upload-images.jianshu.io/upload_images/2475558-ab1cde08e464a837.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

