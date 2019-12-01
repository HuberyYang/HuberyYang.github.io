--- 
layout:     post                      
title:      mac 下 flutter 环境搭建
subtitle:   flutter 环境搭建
keywords:   mac, flutter, 大前端
date:       2019-12-01             
author:     HuberyYang                
header-img: img/article_head_road.jpg
catalog:    true                     
tags:                             
    - mac
    - flutter
    - 大前端
---


**下载 flutter 源码**

```
git clone https://gitee.com/mirrors/flutter.git
```
**配置环境变量**

```
cd ~

// 打开 .bash_profile 文件，如果使用的 zsh, 则打开 .zshrc 文件, 
vi .bash_profile / vi .zshrc

// 添加环境变量, xxxx 为上面下载的 flutter 代码放置的位置
export PUB_HOSTED_URL=https: //pub.flutter-io.cn
export flutter_STORAGE_BASE_URL=https: //storage.flutter-io.cn
export PATH=/xxxx/flutter/bin:$PATH

// Esc 退出终端编辑模式，保存并退出
:qw

```

**运行 flutter doctor**

```
flutter doctor
```
![](https://ws2.sinaimg.cn/large/006tNbRwly1g9heg6i2n1j30vu0dkdgh.jpg)

IDE选 `Android Studio` 或者 `Intellij IDEA` 任一个就行了，我这里安装了 `Android Studio`

**给 `Android Studio` 安装 `flutter`、`Dart`插件**

> 菜单栏 `Android Studio` - `Preferences` - `Plugins` 搜索 `flutter`、`Dart`插件并安装

![](http://q1u57zg2s.bkt.clouddn.com/blog/2019-12-01-preferences_%20plugins_flutter.png)

![](http://q1u57zg2s.bkt.clouddn.com/blog/2019-12-01-preferences_%20plugins_dart.png)

![](http://q1u57zg2s.bkt.clouddn.com/blog/2019-12-01-preferences_%20plugins_installed.png)

**安装 `Xcode`**

在 App Store 中下载安装 Xcode, flutter 运行使用的iOS 模拟器实际上就是 Xcode 中的模拟器

**创建第一个 `flutter` 工程**

- 启动 `Android Studio`，选择 `Start a new Flutter project`

	![](http://q1u57zg2s.bkt.clouddn.com/blog/2019-12-01-1.jpg)

- 选择第一项创建一个`flutter`应用

	![](http://q1u57zg2s.bkt.clouddn.com/blog/2019-12-01-2.jpg)
	
- 配置应用名称、存储位置、描述等信息

	![](http://q1u57zg2s.bkt.clouddn.com/blog/2019-12-01-3.jpg)
	
- 点击 `Finish` 就创建完成了
	
	![](http://q1u57zg2s.bkt.clouddn.com/blog/2019-12-01-4.jpg)
	
- 选择一个 iOS / Android 模拟器，然后点击启动；OK， flutter 之路开始了 ~

	![](http://q1u57zg2s.bkt.clouddn.com/blog/2019-12-01-5.jpg)
	



