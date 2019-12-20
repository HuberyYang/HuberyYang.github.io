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
![](https://blog-1300956916.cos.ap-beijing.myqcloud.com/20191201/8dec842edc9cf948a654d670ac9f3c26.jpg)

IDE选 `Android Studio` 或者 `Intellij IDEA` 任一个就行了，我这里安装了 `Android Studio`

**给 `Android Studio` 安装 `flutter`、`Dart`插件**

> 菜单栏 `Android Studio` - `Preferences` - `Plugins` 搜索 `flutter`、`Dart`插件并安装

![](https://blog-1300956916.cos.ap-beijing.myqcloud.com/20191201/8a90b754321c4f5d683630cada27d124.png)

![](https://blog-1300956916.cos.ap-beijing.myqcloud.com/20191201/5d95d6a6331e0f30ef8b75f0a7f49bf1.png)

![](https://blog-1300956916.cos.ap-beijing.myqcloud.com/20191201/5440cadd5d0932bdfbb8115967a8fa76.png)

**安装 `Xcode`**

在 App Store 中下载安装 Xcode, flutter 运行使用的iOS 模拟器实际上就是 Xcode 中的模拟器

**创建第一个 `flutter` 工程**

- 启动 `Android Studio`，选择 `Start a new Flutter project`

	![](https://blog-1300956916.cos.ap-beijing.myqcloud.com/20191201/4b0892e4dbb0bacf06be12032c9182a5.jpg)

- 选择第一项创建一个`flutter`应用

	![](https://blog-1300956916.cos.ap-beijing.myqcloud.com/20191201/6f7d179d4135a37a475aff535d66ab21.jpg)
	
- 配置应用名称、存储位置、描述等信息

	![](https://blog-1300956916.cos.ap-beijing.myqcloud.com/20191201/c031cfe136f81eca3724f043531f2b9f.jpg)
	
- 点击 `Finish` 就创建完成了
	
	![](https://blog-1300956916.cos.ap-beijing.myqcloud.com/20191201/96c7ee13d7ee925675192bbe548a98a0.jpg)
	
- 选择一个 iOS / Android 模拟器，然后点击启动；OK， flutter 之路开始了 ~

	![](https://blog-1300956916.cos.ap-beijing.myqcloud.com/20191201/14a8b3fbbbd7eb5bae287ed5364f683a.jpg)
	



