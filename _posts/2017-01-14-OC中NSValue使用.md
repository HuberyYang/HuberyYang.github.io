--- 
layout:     post                      
title:      OC中NSValue使用
subtitle:   OC中NSValue使用
date:       2017-01-14               
author:     HuberyYang                
header-img: img/post-bg-desk.jpg  
catalog:    true                     
tags:                             
    - iOS
    - OC
---

NSValue对象是用来存储一个C或者Objective－C数据的简单容器。它可以保存任意类型的数据，如int，float，char，pointers,structures,  objectids。

1.初始化NSValue 并存入相应的值，然后再取出

```
int a = 10;  
          
/* 
第一个参数：需要转化的数据的地址 
第二个参数：要转化的数据的类型的字符串 @encode() 
*/  
NSValue *value1 = [[NSValue alloc] initWithBytes:&a objCType:@encode(int)];  
NSLog(@"%@", value1);  
          
//取出存放的值   
int a1;    
//参数表示转化后的变量的地址  
[value1 getValue:&a1];  
NSLog(@"a1 = %d", a1); 
```
2.存取结构体

```
typedef struct{  
    int x;  
    int y;  
}MyPoint; 

//定义结构体的变量  
MyPoint p = {1, 2};  
//把结构体转换为NSValue对象  
NSValue *v2 = [[NSValue alloc] initWithBytes:&p objCType:@encode(MyPoint)];  
NSLog(@"%@", v2);  
              
MyPoint p1;  
[v2 getValue:&p1];  
NSLog(@"%d %d", p1.x, p1.y);  
```
3.NSRange的存取 

```
NSRange range = {1, 4};  
NSValue *v3 = [NSValue valueWithRange:range];  
NSLog(@"%@", v3);  
          
NSRange range1;  
[v3 getValue:&range1];  
NSLog(@"%ld %ld", range1.location, range1.length);

// 判断NSValue对象里面存储的数据类型是不是int型  
int ret = strcmp([value1 objCType], @encode(int));  
if (ret == 0) {  
    NSLog(@"YES");  
} else{  
    NSLog(@"No");  
}  
```

