---
title: 个性化你的Ubuntu-2：GNOME安装与工具
layout: post
categories: [Linux]
abbrlink: 938c3141
date: 2016-06-02 22:19:50
excerpt: 如何安装Gnome桌面环境。
---

### GNOME安装
从上一篇文章，大家可以看到，GNOME是一系列软件的集合，安装时可以有不同的取舍。对于Ubuntu用户来说，可以有以下两类体验GNOME的方式。（参考：[GNOME installation](https://wiki.ubuntuusers.de/GNOME_Installation/)）

#### 1.Ubuntu GNOME（系统）
Ubuntu GNOME是Ubuntu的一个发行版本（也称Ubuntu variants），就像Ubuntu和Fedora等都是GNU/Linux的发行版那样。Ubuntu GNOME不仅包含了Ubuntu的核心部分、GNOME的核心部分，还有一系列的标准应用。


##### Install from DVD
如果可以接受重新安装系统，请到这里[下载](http://ubuntugnome.org/download/)Ubuntu GNOME。

##### Install with current system
你也可以通过安装metapackage，这样在安装GNOME桌面环境时，你的系统中未安装的标准应用也会被同时安装。

`sudo apt-get install ubuntu-gnome-desktop`

#### 2.GNOME（仅桌面环境）
##### The "real" GNOME
标准的GNOME桌面环境，没有Ubuntu的特性（尽管我区分不出哪些是Ubuntu提供的），也不安装附加的标准应用：

`sudo apt-get install gnome`
##### The minimux GNOME
GNOME的核心部分，不安装附加的标准应用：

`sudo apt-get install gnome-core`
##### GNOME shell
仅安装GNOME的图形界面：
`sudo apt-get install gnome-shell`

你还需要：
`sudo apt-get install gnome-session`

#### 注意
在同一系统上安装不同的桌面环境可能会造成一些意料不到的问题（如锁屏界面丢失），最推荐的方案还是重新安装Ubuntu GNOME，其次，可以安装`ubuntu-gnome-desktop`。

#### 使用新的桌面环境
安装完毕后，重启，可在登录界面选择桌面环境。

![login1]({{ site.baseurl }}/user-imgs/Ubuntu-2/gnome-login1.png)

![login2]({{ site.baseurl }}/user-imgs/Ubuntu-2/gnome-login2.png)

### GNOME配置工具：gnome-tweak-tool
想要充分个性化GNOME桌面环境，扩展GNOME的功能，你还需要安装GNOME的配置工具：gnome tweak tool

`sudo apt-get install gnome-tweak-tool`

![图片：gnome tweak tool提供的功能]({{ site.baseurl }}/user-imgs/Ubuntu-2/gnome-tweak-tool_004.jpg)

利用gnome tweak tool，你可以管理桌面主题、调整窗口特性、调整显示字体、加载GNOME扩展、管理开机自启程序等等。

### 扩展插件
在Ubuntu上，要调整桌面主题，可没有Windows上鼠标右击一下那么简单。
你要先安装上面的tweak tool，然后有人告诉你需要User theme扩展插件，而你跑到`extensions.gnome.org`，遇到的却是这个：
![错误]({{ site.baseurl }}/user-imgs/Ubuntu-2/Selection_019.jpg)

我明明装了GNOME的啊！

这是因为，`extensions.gnome.org`需要与浏览器通信，调用click-to-play的功能，我们需要安装GNOMNE shell intergration这个插件。
#### Chrome用户
##### 利用PPA
```
sudo add-apt-repository ppa:ne0sight/chrome-gnome-shell
sudo apt-get update
sudo apt-get install chrome-gnome-shell
```
##### 通过Chrome Web Store:[GNOME Shell integration](https://chrome.google.com/webstore/detail/gnome-shell-integration/gphhapmejobijbbhgpjhcjognlahblep)

可能需要通过CMake安装native connector,请参考这一[页面](https://wiki.gnome.org/Projects/GnomeShellIntegrationForChrome/Installation)。

#### FireFox用户
使用FireFox访问`extensions.gnome.org`时会有运行GNOME shell integration的通知，允许运行后刷新即可。

更多信息，请参考这一[页面](https://extensions.gnome.org/about/#no-detection)


安装好`tweak-tool`后，祝贺你已经打开了新世界的大门。下篇文章是关于扩展插件的推荐，欢迎继续阅读。

@[ddlee](http://ddlee.cc)
