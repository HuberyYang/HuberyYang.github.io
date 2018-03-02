--- 
layout:     post                      
title:      swift全局print
subtitle:   swift debug 状态下全局打印, 打印信息包含打印时间、文件名、函数名、行数，便于程序调试
date:       2018-03-02                 
author:     HuberyYang                
header-img: img/article_head_queue.jpg
catalog:    true                     
tags:                             
    - iOS
    - OC
    - Swift
---


在OC中我们习惯于在pch文件中定义一个宏打印，以便于程序的调试，但在swift中不存在pch文件，按之前的方式是不行了。swift中可以单独创建一个文件当做pch文件使用，然后将
宏转换成函数

```swift
// debug log
func kLog<T>(message:T,file:String = #file,funcName:String = #function,lineName:Int = #line){
    
    #if DEBUG
        let  flieName = (file as NSString).lastPathComponent
        let formter = DateFormatter()
        formter.dateFormat = "yyyy-MM-dd HH:mm:ss"
        let printDate = formter.string(from: Date())
        print("\(printDate): \(flieName) \(funcName) 第\(lineName)行: \(message)")
    #endif
}


```