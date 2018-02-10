--- 
layout:     post                      
title:      Git 本地项目托管到github     
subtitle:   将本地项目上传到github进行托管  
date:       2018-02-07                  
author:     HuberyYang                
header-img: img/article_head_git_01.jpg
catalog:    true                     
tags:                             
    - Git 
    - iOS
    - ProjectManagement
---


目前，项目管理除了SVN还可以使用Git，下面介绍怎么将项目托管到GitHub。

**注册GitHub账号**

前往GitHub主页[https://github.com](https://github.com/)注册账号，之后登入。![账号注册入口](http://upload-images.jianshu.io/upload_images/2475558-d7d19652b3cc8dd2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**配置ssh**

GitHub上项目clone包含`HTTPS`、`SSH`两种方式，而使用`SSH`进行远程操作时是不需要输入账号密码的，所以你懂的。

- 查看个人文件中是否包含`.ssh`文件夹
打开终端，执行命令`cd ~/.` 进入个人文件夹；
命令`ls -a`查看当前目录下所有文件
![个人文件夹](http://upload-images.jianshu.io/upload_images/2475558-caa72f6a5ed51f6b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
如果没有该文件夹就重新创建一个：`mkdir ~/.ssh`

- 生成`ssh key`

	1.进入.ssh 文件夹 ，执行命令`cd .ssh`
		
	2.命令`ssh-Keygen -t rsa -C “你的GitHub登录邮箱”`
		
	3.接下来会让你输入用于存放 `ssh key` 的文件名，输入自定义的文件名或者直接回车使用默认文件名`id_rsa `和`id_rsa.pub`
	![ssh  key 文件名](http://upload-images.jianshu.io/upload_images/2475558-87b18b9b0487b994.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
	注意：如果之前已经存在相同的文件，直接覆盖就可以了,如下图直接输入`y`回车
	![覆盖重复文件](http://upload-images.jianshu.io/upload_images/2475558-41a187a059aa5d8f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
		
	4.然后会让你输入两次密码，该密码只用于远程push，如不想设置密码，回车即可
	![push密码设置](http://upload-images.jianshu.io/upload_images/2475558-3ed2581f0a9b27bd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
	然后出现下面情况则说明`ssh key` 创建成功
	![ssh key 创建成功](http://upload-images.jianshu.io/upload_images/2475558-ce07b8542db93500.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
		
	5.获取 `ssh key ` 公有key
	先查看`.ssh`下文件，执行命令`ls -a`
	![.ssh下文件](http://upload-images.jianshu.io/upload_images/2475558-8ee130a96febf4f2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
	执行命令`cat id_rsa.pub`,如果你自定义文件名，则执行命令`cat 自定义名字.pub`，查看文件内容，然后复制待用

**GitHub上添加 ssh key**

1.进入个人设置
![进入设置](http://upload-images.jianshu.io/upload_images/2475558-7d658479d915b82e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2.新建`ssh key`
![新建ssh key](http://upload-images.jianshu.io/upload_images/2475558-ab24a4a2e812bbca.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
填写key名称，粘贴公有key内容，确认
![内容填写](http://upload-images.jianshu.io/upload_images/2475558-f01923ba30b57eea.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
完成之后是这样：![完成后的样子](http://upload-images.jianshu.io/upload_images/2475558-581817e003123203.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3.将 ssh 连接到 GitHub
执行命令`ssh -T git@github.com`![ssh 连接到 GitHub](http://upload-images.jianshu.io/upload_images/2475558-ce483725a0519ac8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
连接成功后钥匙图标回变成绿色
![连接成功](http://upload-images.jianshu.io/upload_images/2475558-de59401498e2fef2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**创建GitHub仓库**

点击个人选择项`Your profiles`,选择`Repositories`,然后选择`New`
![](http://upload-images.jianshu.io/upload_images/2475558-276754ba6aeb5515.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](http://upload-images.jianshu.io/upload_images/2475558-fa52aa5278af85e3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

配置仓库信息
![填写仓库信息](http://upload-images.jianshu.io/upload_images/2475558-7a92f7794658698f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
如需了解开原协议，请看这里：[开源许可协议了解这些就够了](https://www.jianshu.com/p/cceeafb019ed)
创建好之后大概如下：
![](http://upload-images.jianshu.io/upload_images/2475558-b1a030b5371f1857.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**本地项目托管到GitHub**

1.以仓库名称在本地创建一个项目

![](http://upload-images.jianshu.io/upload_images/2475558-5ba3a97bc00db94e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2.进入项目文件夹内 ，执行命令`cd ProjectTest`

3.创建一个本地的git仓库，执行命令`git init`
![](http://upload-images.jianshu.io/upload_images/2475558-351447427388ce98.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

4.关联本地仓库与远程仓库，命令`git remote add origin '远程仓库地址'`,没有任何提示表示关联成功,远程仓库地址获取方式如下图：
![](http://upload-images.jianshu.io/upload_images/2475558-559a58abff4afbce.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

5.查看git当前状态，命令`git status`
![](http://upload-images.jianshu.io/upload_images/2475558-39933d79cdc45e6c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

6.将变动由工作区提交到暂存区,命令 `git add .`
> __git add -u与-A .三者的区别:__
> 
> `git add -u` 将文件的修改、文件的删除，添加到暂存区。

> `git add .`  将文件的修改，文件的新建，添加到暂存区。

> `git add -A` 将文件的修改，文件的删除，文件的新建，添加到暂存区。

7.将变动由暂存区提交到提交区,命令 `git commit -m"提交信息"`，“提交信息”是对此次操作的备注

8.将变动由提交区更新到远程仓库，先执行命令`git pull --rebase origin master`,无报错之后执行`git push origin master`,完成之后你的项目就已经托管到GitHub上了
![](http://upload-images.jianshu.io/upload_images/2475558-1795a34adac4f617.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)







