--- 
layout:     post                      
title:      Git 本地项目托管到github     
subtitle:   将本地项目上传到github进行托管 
keywords:   git,项目托管,git项目托管    
date:       2018-02-07                  
author:     HuberyYang                
header-img: img/article_head_git_01.jpg
catalog:    true                     
tags:                             
    - Git 
---


目前，项目管理除了SVN还可以使用Git，下面介绍怎么将项目托管到GitHub。

**注册GitHub账号**

前往GitHub主页[https://github.com](https://github.com/)注册账号，之后登入。
![账号注册入口](https://huberyblog.oss-cn-hangzhou.aliyuncs.com/20180207/c64e927786640fafb2e3e189d8970f73.png)

**配置ssh**

GitHub上项目clone包含`HTTPS`、`SSH`两种方式，而使用`SSH`进行远程操作时是不需要输入账号密码的，所以你懂的。

- 查看个人文件中是否包含`.ssh`文件夹
打开终端，执行命令`cd ~/.` 进入个人文件夹；
命令`ls -a`查看当前目录下所有文件
![个人文件夹](https://huberyblog.oss-cn-hangzhou.aliyuncs.com/20180207/ec61b69e928207d2aa3af09ed0a3b0f8.png)
如果没有该文件夹就重新创建一个：`mkdir ~/.ssh`

- 生成`ssh key`

	1.进入.ssh 文件夹 ，执行命令`cd .ssh`
		
	2.命令`ssh-Keygen -t rsa -C “你的GitHub登录邮箱”`
		
	3.接下来会让你输入用于存放 `ssh key` 的文件名，输入自定义的文件名或者直接回车使用默认文件名`id_rsa `和`id_rsa.pub`
	![ssh  key 文件名](https://huberyblog.oss-cn-hangzhou.aliyuncs.com/20180207/4b50cc60ac55788eec60b5768f7414be.png)
	注意：如果之前已经存在相同的文件，直接覆盖就可以了,如下图直接输入`y`回车
	![覆盖重复文件](https://huberyblog.oss-cn-hangzhou.aliyuncs.com/20180207/5c96af35ced46cb34ad2fac0b1a53086.png)
		
	4.然后会让你输入两次密码，该密码只用于远程push，如不想设置密码，回车即可
	![push密码设置](https://huberyblog.oss-cn-hangzhou.aliyuncs.com/20180207/e158a42cd88222170835af5c349a4b27.png)
	然后出现下面情况则说明`ssh key` 创建成功
	![ssh key 创建成功](https://huberyblog.oss-cn-hangzhou.aliyuncs.com/20180207/ac60435a07acc43439fcfde494e6e318.png)
		
	5.获取 `ssh key ` 公有key
	先查看`.ssh`下文件，执行命令`ls -a`
	![.ssh下文件](https://huberyblog.oss-cn-hangzhou.aliyuncs.com/20180207/aa81bcdc4ee4f33a22b3028ecec0aacf.png)
	执行命令`cat id_rsa.pub`,如果你自定义文件名，则执行命令`cat 自定义名字.pub`，查看文件内容，然后复制待用

**GitHub上添加 ssh key**

1.进入个人设置
![进入设置](https://huberyblog.oss-cn-hangzhou.aliyuncs.com/20180207/dbac106983f77784f6489a7f44efaa51.png)

2.新建`ssh key`
![新建ssh key](https://huberyblog.oss-cn-hangzhou.aliyuncs.com/20180207/417a3a5a6036b90c15843a1567184ef9.png)
填写key名称，粘贴公有key内容，确认
![内容填写](https://huberyblog.oss-cn-hangzhou.aliyuncs.com/20180207/19768509b336b90c0506115a54c0b4dd.png)
完成之后是这样：![完成后的样子](https://huberyblog.oss-cn-hangzhou.aliyuncs.com/20180207/5aa994e99ef3806e28a80b70ed3b8a8f.png)

3.将 ssh 连接到 GitHub
执行命令`ssh -T git@github.com`![ssh 连接到 GitHub](https://huberyblog.oss-cn-hangzhou.aliyuncs.com/20180207/49c8e6bfcd87c0a7adce2e46f731bcd2.png)
连接成功后钥匙图标回变成绿色
![连接成功](https://huberyblog.oss-cn-hangzhou.aliyuncs.com/20180207/31e74a9986beeee5e346c6d9722c7684.png)

**创建GitHub仓库**

点击个人选择项`Your profiles`,选择`Repositories`,然后选择`New`
![](https://huberyblog.oss-cn-hangzhou.aliyuncs.com/20180207/290c9fd201d26afeae6c0c64b3fa0954.png)
![](https://huberyblog.oss-cn-hangzhou.aliyuncs.com/20180207/c82577e972bb0da7741005e469033a35.png)

配置仓库信息
![填写仓库信息](https://huberyblog.oss-cn-hangzhou.aliyuncs.com/20180207/c9de8b892aabc11a6a81238e7f170fab.png)
如需了解开原协议，请看这里：[开源许可协议了解这些就够了](https://www.jianshu.com/p/cceeafb019ed)
创建好之后大概如下：
![](https://huberyblog.oss-cn-hangzhou.aliyuncs.com/20180207/65ac6632085ec9d95d17d4bd269efaa8.png)

**本地项目托管到GitHub**

1.以仓库名称在本地创建一个项目

![](https://huberyblog.oss-cn-hangzhou.aliyuncs.com/20180207/20b08335dd965290f37b0d0994702770.png)

2.进入项目文件夹内 ，执行命令`cd ProjectTest`

3.创建一个本地的git仓库，执行命令`git init`
![](https://huberyblog.oss-cn-hangzhou.aliyuncs.com/20180207/5c735052cc20dd7f4614f8ab9e51cc22.png)

4.关联本地仓库与远程仓库，命令`git remote add origin '远程仓库地址'`,没有任何提示表示关联成功,远程仓库地址获取方式如下图：
![](https://huberyblog.oss-cn-hangzhou.aliyuncs.com/20180207/520ffba23993cd4206c066b1134ac6e2.png)

5.查看git当前状态，命令`git status`
![](https://huberyblog.oss-cn-hangzhou.aliyuncs.com/20180207/f7c39cbc7159ac6ea0ecf0edbda9b025.png)

6.将变动由工作区提交到暂存区,命令 `git add .`
> __git add -u与-A .三者的区别:__
> 
> `git add -u` 将文件的修改、文件的删除，添加到暂存区。

> `git add .`  将文件的修改，文件的新建，添加到暂存区。

> `git add -A` 将文件的修改，文件的删除，文件的新建，添加到暂存区。

7.将变动由暂存区提交到提交区,命令 `git commit -m"提交信息"`，“提交信息”是对此次操作的备注

8.将变动由提交区更新到远程仓库，先执行命令`git pull --rebase origin master`,无报错之后执行`git push origin master`,完成之后你的项目就已经托管到GitHub上了
![](https://huberyblog.oss-cn-hangzhou.aliyuncs.com/20180207/8647f1d785a4e7df6c6833b10442ee78.png)
