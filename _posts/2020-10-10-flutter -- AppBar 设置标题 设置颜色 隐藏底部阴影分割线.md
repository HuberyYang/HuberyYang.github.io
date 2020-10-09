--- 
layout:     post                      
title:      flutter -- AppBar 设置标题 设置颜色 隐藏底部阴影分割线
subtitle:   AppBar 设置标题 设置颜色 隐藏底部阴影分割线
keywords:   flutter、AppBar
date:       2020-10-10                
author:     HuberyYang                
header-img: img/article_head_road.jpg
catalog:    true                     
tags:
   - flutter                            
---

抄袭至, 方便以后查找：[flutter AppBar 设置标题 设置颜色 隐藏底部阴影分割线](https://www.jianshu.com/p/42e2fbeafd95)


```
AppBar(
         title: Text(titleList[this._currentIndex],//标题显示什么
           style: TextStyle(
               color: Colors.black,//设置字体颜色
               fontSize: 20,//设置字体大小
           ),
         ),
//              leading  automaticallyImplyLeading 一起使用可以在有返回的按钮吧返回按钮位置给覆盖显示比如使用场景搜索，
             leading: null,
              automaticallyImplyLeading: false,
//            titleSpacing 距离屏幕两边的距离默认16，当我们设置搜索的时候感觉两边距离太大太小可以调
              titleSpacing: 10,
         backgroundColor: Colors.white,//设置导航背景颜色
         elevation: 0,//隐藏底部阴影分割线
         centerTitle: true,//标题是否居中 安卓上有效ios默认居中
         leading: IconButton(
           icon: Icon(Icons.arrow_back_ios,color: Colors.black,),
           onPressed: (){
             print('点击了返回');
           },
         ), // leading 设置左侧返回
         actions: <Widget>[
           RaisedButton(
             child: Text('菜单'),
             onPressed: (){
               print('点击了菜单');

             },
             color: Colors.red,
           ),
           IconButton(
             icon: Icon(Icons.favorite,color: Colors.black,),
             onPressed: (){
               print('点击了心心');
             },
           ),

         ],//actions 设置左侧按钮
         
       );
```