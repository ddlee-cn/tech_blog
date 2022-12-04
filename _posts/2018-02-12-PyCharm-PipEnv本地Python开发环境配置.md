---
title: PyCharm+PipEnv本地Python开发环境配置
layout: post
categories: [Python]
abbrlink: 2da92ac3
date: 2018-02-12 10:09:22
---


本文记录基于PyCharm的Python工作环境配置，最终实现的效果是本地修改，远程调试，运行环境可迁移。
<!-- more-->

### 准备

该环境的配置需要如下准备：

- 开发用笔记本电脑和部署用服务器
- PyCharm Pro版本，如果是学生，可以免费得到这一版本

- 依赖管理包Pipenv

逻辑是在本地编写和调试代码，调用服务器的计算资源运行，并通过PyCharm和Pipenv保证本地开发环境和服务器端运行环境的一致。

关于Pipenv：
Pipenv是一个将pip和virtualenv功能整合在一起的依赖管理包，提倡一个项目一个环境，利用Pipfile存储依赖信息并提供可迁移性。Pipenv为每个项目建立一个virtualenv，并记录pip安装依赖包的版本信息。

### 环境配置

1.（可选）在GitHub等版本控制服务建立repo。
2.在服务器上建立项目文件夹，并用命令`pipenv install`初始化项目环境。
3.本地使用PyCharm打开项目（从GitHub，或者新建在本地文件夹），并配置部署环境（Tools->Deployment->Configurations)，以SFTP方式连接，并在本地开发项目文件夹和服务器端项目文件夹建立映射。

![sftp]({{ site.baseurl }}/user-imgs/PyCharm-PipEnv本地Python开发环境配置/sftp.png)

4.打开Files->settings->project settings->project interpreter，选择add remote，勾选Deployment configuration到上一步建立的配置，选择Move this server to IDE settings。

![interpreter]({{ site.baseurl }}/user-imgs/PyCharm-PipEnv本地Python开发环境配置/interpreter.png)

最后指定好上一步pipenv建立的vitualenv（默认目录为~/.local/share/virtualenvs/）为Python解释器的路径。

![path]({{ site.baseurl }}/user-imgs/PyCharm-PipEnv本地Python开发环境配置/patch.png)


5.在Tools->Deployment->Download from将服务器代码下载到本地（主要是Pipfile），之后右击项目，将项目文件上传到服务器（本地代码文件），并在Tools->Deployment勾选Automatic Upload，使本地代码跟服务器代码保持同步。

6.在Tools->Start SSH Session利用部署配置登录服务器，使用Pipenv来安装需要的依赖后，在本地新建脚本文件进行测试。

### 工作流

1.本地修改代码，自动上传到服务器
2.利用SSH处理数据存取路径等问题
3.运行脚本（使用远程环境）并调试
4.解决报错问题
5.调试成功，将代码提交到版本控制服务
6.重复
