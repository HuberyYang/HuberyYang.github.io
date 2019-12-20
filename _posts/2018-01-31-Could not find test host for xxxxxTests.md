--- 
layout:     post                      
title:      iOS Could not find test host for xxxxTests
subtitle:   单元测试报错：Could not find test host for xxxxTests  
keywords:   ios,单元测试, Could not find test host for xxxxTests
date:       2018-01-31                  
author:     HuberyYang                
header-img: img/article_head_errors.jpg
catalog:    true                     
tags:                             
    - iOS
    - OC
---

单元测试报错：Could not find test host for xxxxTests: ，意为无法找到需要进行单元测试的 Target，完整的错误信息如下：
![屏幕快照](https://blog-1300956916.cos.ap-beijing.myqcloud.com/20180131/65e45e427df546d9fdbeb11f36a483b5.png)

解决办法：按下图找到 `Host Application` ，然后选择准备测试的Target就可以了。
![deal.png](https://blog-1300956916.cos.ap-beijing.myqcloud.com/20180131/711befbc607225b37a1b9687f43c4390.png)

