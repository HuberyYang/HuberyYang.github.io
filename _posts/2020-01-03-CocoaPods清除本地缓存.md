--- 
layout:     post                      
title:      CocoaPods清除本地缓存
subtitle:   cocoapods清除本地缓存
keywords:   CocoaPods,清除缓存
date:       2020-01-03                
author:     HuberyYang                
header-img: img/article_head_road.jpg
catalog:    true                     
tags:                             
   - CocoaPods
   - iOS
---

讲道理，平时很少需要清除 `Pod` 的缓存，毕竟缓存是省时、省力、省流量的好东西；但是，在 `pod install`  或者 `pod update` 无数遍之后仍无法更新本地 pod 库，那就可以尝试清除一下缓存。

下面来看具体如何操作：

##### 1. 查看本地缓存列表
`pod  cache list`

##### 2. 找到需要清除的缓存项并删除
`pod cache clean xxxx`

##### 3. 或者清除全部的缓存
`pod cache clean --all`

##### 4. 重新拉取
`pod install`

##### 5.  重新拉取失败时处理
在 `pod install`  或者 `pod update` 之后，对应的库并没有重新下载，此时可以删除工程目录下得到 `Pods` 文件夹 和 `Podfile.lock`  文件，然后再重新拉取。